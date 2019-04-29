---
title: 将静态 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器
description: 了解如何创建静态 IP 地址并将其用于 Azure Kubernetes 服务 (AKS) 负载均衡器。
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031636"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>将静态公用 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器

默认情况下，分配给 AKS 群集创建的负载均衡器资源的公用 IP 地址仅在该资源的保留期内有效。 如果删除 Kubernetes 服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公用 IP 地址。

本文介绍如何创建静态公用 IP 地址并将其分配给 Kubernetes 服务。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

目前仅支持基本 IP SKU。 支持标准 IP 资源 SKU 的开发正在进行中。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>创建静态 IP 地址

创建静态公共 IP 地址以用于 AKS 时，应在**节点**资源组中创建 IP 地址资源。 若要分隔资源，请参阅以下部分，以便[在节点资源组外部使用静态 IP 地址](#use-a-static-ip-address-outside-of-the-node-resource-group)。

首先，请使用 [az aks show][az-aks-show] 命令并添加 `--query nodeResourceGroup` 查询参数获取节点资源组名称。 以下示例获取名为 myResourceGroup 的资源组中 AKS 群集名称 myAKSCluster 的节点资源组：

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

现在，使用 [az network public ip create][az-network-public-ip-create] 命令创建静态公用 IP 地址。 指定上一命令中获取的节点资源组名称，然后指定 IP 地址资源的名称，如 myAKSPublicIP：

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

将显示 IP 地址，如以下精简版示例输出中所示：

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

稍后可以使用 [az network public-ip list][az-network-public-ip-list] 命令获取公用 IP 地址。 指定节点资源组的名称和创建的公共 IP 地址，然后查询 ipAddress，如以下示例中所示：

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用静态 IP 地址创建服务

若要使用静态公用 IP 地址创建服务，请将 `loadBalancerIP` 属性和静态公用 IP 地址的值添加到 YAML 清单。 创建名为 `load-balancer-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公用 IP 地址。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

使用 `kubectl apply` 命令创建服务和部署。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>在节点资源组外部使用静态 IP 地址

使用 Kubernetes 1.10 或更高版本，可以使用在节点资源组外部创建的静态 IP 地址。 AKS 群集使用的服务主体必须将权限委托给其他资源组，如下例所示：

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

若要在节点资源组之外使用 IP 地址，请向服务定义添加注释。 以下示例将注释设置为名为 *myResourceGroup* 的资源组。 提供自己的资源组名称：

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>故障排除

如果 Kubernetes 服务清单的 *loadBalancerIP* 属性中定义的静态 IP 地址不存在或尚未在节点资源组中创建，并且尚未配置其他托管，则负载均衡器服务创建将失败。 若要排除此故障，请用 [kubectl describe][kubectl-describe] 命令复查服务创建事件。 提供 YAML 清单中指定的服务的名称，如以下示例中所示：

```console
kubectl describe service azure-load-balancer
```

将显示有关 Kubernetes 服务资源的信息。 以下示例输出末尾的“事件”指示“找不到用户提供的 IP 地址”。 在这些情况下，请验证是否已在节点资源组中创建静态公用 IP 地址，以及在 Kubernetes 服务清单中指定的 IP 地址是否正确。

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>后续步骤

如需获得对流向应用程序的网络流量的额外控制，你需要改为[创建入口控制器][aks-ingress-basic]。 此外，还可以[使用静态公用 IP 地址创建入口控制器][aks-static-ingress]。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku