---
title: 将静态 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器
description: 了解如何创建静态 IP 地址并将其用于 Azure Kubernetes 服务 (AKS) 负载均衡器。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/06/2019
ms.author: mlearned
ms.openlocfilehash: 8457f1c0c5b6107c4b44f6f00236a33f7c67452a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325445"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>将静态公用 IP 地址用于 Azure Kubernetes 服务 (AKS) 负载均衡器

默认情况下，分配给 AKS 群集创建的负载均衡器资源的公用 IP 地址仅在该资源的保留期内有效。 如果删除 Kubernetes 服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公用 IP 地址。

本文介绍如何创建静态公用 IP 地址并将其分配给 Kubernetes 服务。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 If you need an AKS cluster, see the AKS quickstart [using the Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

You also need the Azure CLI version 2.0.59 or later installed and configured. 运行  `az --version` 即可查找版本。 If you need to install or upgrade, see [Install Azure CLI][install-azure-cli].

This article covers using a *Standard* SKU IP with a *Standard* SKU load balancer. For more information, see [IP address types and allocation methods in Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>创建静态 IP 地址

Create a static public IP address with the [az network public ip create][az-network-public-ip-create] command. The following creates a static IP resource named *myAKSPublicIP* in the *myResourceGroup* resource group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> If you are using a *Basic* SKU load balancer in your AKS cluster, use *Basic* for the *sku* parameter when defining a public IP. Only *Basic* SKU IPs work with the *Basic* SKU load balancer and only *Standard* SKU IPs work with *Standard* SKU load balancers. 

The IP address is displayed, as shown in the following condensed example output:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

You can later get the public IP address using the [az network public-ip list][az-network-public-ip-list] command. 指定节点资源组的名称和创建的公共 IP 地址，然后查询 ipAddress，如以下示例中所示：

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用静态 IP 地址创建服务

Before creating a service, ensure the service principal used by the AKS cluster has delegated permissions to the other resource group. 例如：

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

To create a *LoadBalancer* service with the static public IP address, add the `loadBalancerIP` property and the value of the static public IP address to the YAML manifest. 创建名为 `load-balancer-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公用 IP 地址。 The following example also sets the annotation to the resource group named *myResourceGroup*. Provide your own resource group name.

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

使用 `kubectl apply` 命令创建服务和部署。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>故障排除

If the static IP address defined in the *loadBalancerIP* property of the Kubernetes service manifest does not exist, or has not been created in the node resource group and no additional delegations configured, the load balancer service creation fails. To troubleshoot, review the service creation events with the [kubectl describe][kubectl-describe] command. 提供 YAML 清单中指定的服务的名称，如以下示例中所示：

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

For additional control over the network traffic to your applications, you may want to instead [create an ingress controller][aks-ingress-basic]. You can also [create an ingress controller with a static public IP address][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
