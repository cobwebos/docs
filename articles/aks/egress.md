---
title: 将静态 IP 用于出口流量
titleSuffix: Azure Kubernetes Service
description: 了解如何创建和使用 Azure Kubernetes 服务 (AKS) 群集中出口流量的静态公共 IP 地址
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: f7ea25c3348b96ec6d8818e8e1db4660b308dabc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517767"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>将静态公共 IP 地址用于 Azure Kubernetes 服务中的*基本*SKU 负载均衡器的出口流量（AKS）

默认情况下，Azure Kubernetes 服务 (AKS) 群集的出口 IP 地址是随机分配的。 例如，当需要标识用于访问外部服务的 IP 地址时，此配置是不理想的。 相反，你可能需要分配要添加到服务访问允许列表的静态 IP 地址。

本文介绍了如何创建和使用静态公共 IP 地址，以便用于 AKS 群集中的出口流量。

## <a name="before-you-begin"></a>准备阶段

本文假定你使用的是 Azure 基本负载均衡器。  建议使用[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)，并且可以使用更高级的功能来[控制 AKS 出口流量](https://docs.microsoft.com/azure/aks/limit-egress-traffic)。

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

> [!IMPORTANT]
> 本文使用带有单一节点池的*基本*SKU 负载均衡器。 此配置不可用于多个节点池，因为多个节点池不支持*基本*SKU 负载均衡器。 有关使用*标准*SKU 负载均衡器的更多详细信息，请参阅[使用 Azure KUBERNETES Service （AKS）中的公共标准负载均衡器][slb]。

## <a name="egress-traffic-overview"></a>出口流量概述

AKS 群集的出站流量遵循 [Azure 负载均衡器约定][outbound-connections]。 在创建 `LoadBalancer` 类型的第一个 Kubernetes 服务之前，AKS 群集中的代理节点不是任何 Azure 负载均衡器池的一部分。 在此配置中，节点没有实例级公共 IP 地址。 Azure 将出站流转换为不可配置的或确定性的公用源 IP 地址。

创建 `LoadBalancer` 类型的 Kubernetes 服务后，会向 Azure 负载均衡器池添加代理节点。 对于出站流，Azure 将其转换为在负载均衡器上配置的第一个公共 IP 地址。 此公共 IP 地址仅对该资源的生命期有效。 如果删除 Kubernetes 负载均衡器服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公共 IP 地址。

## <a name="create-a-static-public-ip"></a>创建静态公共 IP

使用 [az aks show][az-aks-show] 命令并添加 `--query nodeResourceGroup` 查询参数获取资源组名称。 以下示例获取名为 myResourceGroup 的资源组中 AKS 群集名称 myAKSCluster 的节点资源组：

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

现在，使用 [az network public ip create][az-network-public-ip-create] 命令创建静态公共 IP 地址。 指定上一命令中获取的节点资源组名称，然后指定 IP 地址资源的名称，如 myAKSPublicIP**：

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

将显示 IP 地址，如以下精简版示例输出中所示：

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

稍后可以使用 [az network public-ip list][az-network-public-ip-list] 命令获取公共 IP 地址。 指定节点资源组的名称，然后查询 ipAddress，如以下示例中所示：

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>使用静态 IP 创建服务

若要使用静态公共 IP 地址创建服务，请将 `loadBalancerIP` 属性和静态公共 IP 地址的值添加到 YAML 清单。 创建名为 `egress-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公共 IP 地址。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

使用 `kubectl apply` 命令创建服务和部署。

```console
kubectl apply -f egress-service.yaml
```

此服务将在 Azure 负载均衡器上配置一个新的前端 IP。 如果没有配置任何其他 IP，则**所有**出口流量现在都应当使用此地址。 在 Azure 负载均衡器上配置多个地址时，所有这些公共 IP 地址都是出站流的候选项，并且会随机选择一个。

## <a name="verify-egress-address"></a>验证出口地址

若要验证是否正在使用静态公共 IP 地址，可以使用 DNS 查找服务，例如 `checkip.dyndns.org`。

启动并附加到基本 Debian pod：

```console
kubectl run -it --rm aks-ip --image=debian
```

若要从容器中访问网站，请使用 `apt-get` 将 `curl` 安装到容器。

```console
apt-get update && apt-get install curl -y
```

现在，使用 curl 访问 checkip.dyndns.org 站点。 将显示出口 IP 地址，如以下示例输出中所示。 此 IP 地址与为负载均衡器服务创建和定义的静态公共 IP 地址相匹配：

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>后续步骤

若要避免在 Azure 负载均衡器上维护多个公共 IP 地址，可以使用入口控制器。 入口控制器提供的其他好处包括：SSL/TLS 终止、对 URI 重写的支持以及上游 SSL/TLS 加密。 有关详细信息，请参阅[在 AKS 中创建基本入口控制器][ingress-aks-cluster]。

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
