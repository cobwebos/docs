---
title: Azure Kubernetes 服务 (AKS) 中出口流量的静态 IP 地址
description: 了解如何创建和使用 Azure Kubernetes 服务 (AKS) 群集中出口流量的静态公用 IP 地址
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 175fa625a94626cde4d782abd1e9629530cab8b4
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408498"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>为 Azure Kubernetes 服务 (AKS) 中的出口流量使用公用静态 IP 地址

默认情况下，Azure Kubernetes 服务 (AKS) 群集的出口 IP 地址是随机分配的。 例如，当需要标识用于访问外部服务的 IP 地址时，此配置是不理想的。 而是可能需要分配静态 IP 地址，此地址可被列入服务访问白名单。

本文介绍了如何创建和使用静态公用 IP 地址，以便用于 AKS 群集中的出口流量。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>出口流量概述

AKS 群集的出口流量遵循 [Azure 负载均衡器约定][outbound-connections]。 在创建 `LoadBalancer` 类型的第一个 Kubernetes 服务之前，AKS 群集中的代理节点不是任何 Azure 负载均衡器池的一部分。 在此配置中，节点没有实例级公用 IP 地址。 Azure 将出站流转换为不可配置的或确定性的公用源 IP 地址。

创建 `LoadBalancer` 类型的 Kubernetes 服务后，会向 Azure 负载均衡器池添加代理节点。 对于出站流，Azure 将其转换为在负载均衡器上配置的第一个公用 IP 地址。 此公用 IP 地址仅对该资源的生命期有效。 如果删除 Kubernetes 负载均衡器服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公用 IP 地址。

## <a name="create-a-static-public-ip"></a>创建静态公共 IP

创建静态公用 IP 地址以用于 AKS 时，必须在节点资源组中创建 IP 地址资源。 使用 [az aks show][az-aks-show] 命令获取资源组名称并添加 `--query nodeResourceGroup` 查询参数。 以下示例获取名为 myResourceGroup 的资源组中 AKS 群集名称 myAKSCluster 的节点资源组：

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

现在，使用 [az network public ip create][az-network-public-ip-create] 命令创建静态公用 IP 地址。 指定上一命令中获取的节点资源组名称，然后指定 IP 地址资源的名称，如 myAKSPublicIP：

```azurecli
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
````

稍后可以使用 [az network public-ip list][az-network-public-ip-list] 命令获取公用 IP 地址。 指定节点资源组的名称，然后查询 ipAddress，如以下示例中所示：

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>使用静态 IP 创建服务

若要使用静态公用 IP 地址创建服务，请将 `loadBalancerIP` 属性和静态公用 IP 地址的值添加到 YAML 清单。 创建名为 `egress-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公用 IP 地址。

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

此服务将在 Azure 负载均衡器上配置一个新的前端 IP。 如果没有配置任何其他 IP，则**所有**出口流量现在都应当使用此地址。 当在 Azure 负载均衡器上配置了多个地址时，出口将使用该负载均衡器上的第一个 IP。

## <a name="verify-egress-address"></a>验证出口地址

若要验证是否正在使用静态公用 IP 地址，可以使用 DNS 查找服务，例如 `checkip.dyndns.org`。

启动并附加到基本 Debian pod：

```console
kubectl run -it --rm aks-ip --image=debian
```

若要从容器中访问网站，请使用 `apt-get` 将 `curl` 安装到容器。

```console
apt-get update && apt-get install curl -y
```

现在，使用 curl 访问 checkip.dyndns.org 站点。 将显示出口 IP 地址，如以下示例输出中所示。 此 IP 地址与为负载均衡器服务创建和定义的静态公用 IP 地址相匹配：

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

## <a name="next-steps"></a>后续步骤

若要避免在 Azure 负载均衡器上维护多个公用 IP 地址，可以使用入口控制器。 入口控制器提供的其他好处包括：SSL/TLS 终止、对 URI 重写的支持以及上游 SSL/TLS 加密。 有关详细信息，请参阅[在 AKS 中创建基本入口控制器][ingress-aks-cluster]。

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