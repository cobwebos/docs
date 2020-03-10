---
title: 使用 Azure Kubernetes 服务（AKS）负载均衡器的静态 IP 地址和 DNS 标签
description: 了解如何创建静态 IP 地址并将其用于 Azure Kubernetes 服务 (AKS) 负载均衡器。
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 32889dbbcafd9510f8d04cb9c602d4802c6d1a1a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943571"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>使用 Azure Kubernetes 服务（AKS）负载均衡器的静态公共 IP 地址和 DNS 标签

默认情况下，分配给 AKS 群集创建的负载均衡器资源的公共 IP 地址仅在该资源的保留期内有效。 如果删除 Kubernetes 服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公共 IP 地址。

本文介绍如何创建静态公共 IP 地址并将其分配给 Kubernetes 服务。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

还需要安装并配置 Azure CLI 版本2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

本文介绍如何将*标准*sku IP 与*标准*sku 负载均衡器结合使用。 有关详细信息，请参阅[Azure 中的 IP 地址类型和分配方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>创建静态 IP 地址

使用[az network 公共 ip create][az-network-public-ip-create]命令创建静态公共 ip 地址。 下面在*myResourceGroup*资源组中创建名为*MYAKSPUBLICIP*的静态 IP 资源：

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> 如果在 AKS 群集中使用*基本*sku 负载平衡器，请在定义公共 IP 时使用*基本*的*sku*参数。 只有*基本*sku ip 适用于*基本*sku 负载均衡器，并且只有*标准*sku ip 适用于*标准*sku 负载均衡器。 

将显示 IP 地址，如以下精简示例输出所示：

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

稍后可以使用[az network 公共 ip list][az-network-public-ip-list]命令获取公共 IP 地址。 指定节点资源组的名称和创建的公共 IP 地址，然后查询 ipAddress，如以下示例中所示：

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用静态 IP 地址创建服务

在创建服务之前，请确保 AKS 群集使用的服务主体已将权限委派给其他资源组。 例如：

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

若要创建具有静态公共 IP 地址的*LoadBalancer*服务，请将 `loadBalancerIP` 属性和静态公共 ip 地址的值添加到 YAML 清单。 创建名为 `load-balancer-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公共 IP 地址。 以下示例还将批注设置为名为*myResourceGroup*的资源组。 提供自己的资源组名称。

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

## <a name="apply-a-dns-label-to-the-service"></a>向服务应用 DNS 标签

如果服务使用动态或静态公共 IP 地址，则可以使用 "服务批注 `service.beta.kubernetes.io/azure-dns-label-name` 来设置面向公众的 DNS 标签。 这将使用 Azure 的公共 DNS 服务器和顶级域为你的服务发布完全限定的域名。 批注值在 Azure 位置中必须是唯一的，因此建议使用完全限定的标签。   

然后，Azure 会自动将默认子网（`<location>.cloudapp.azure.com` 例如，location 是所选区域）追加到你提供的名称，以创建完全限定的 DNS 名称。 例如：

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> 若要在自己的域中发布服务，请参阅[Azure DNS][azure-dns-zone]和[外部 DNS][external-dns]项目。

## <a name="troubleshoot"></a>故障排除

如果在 Kubernetes service 清单的*loadBalancerIP*属性中定义的静态 IP 地址不存在，或者尚未在节点资源组中创建，且未配置其他委派，则负载均衡器服务创建将失败。 若要进行故障排除，请查看服务创建事件，并提供[kubectl 描述][kubectl-describe]命令。 提供 YAML 清单中指定的服务的名称，如以下示例中所示：

```console
kubectl describe service azure-load-balancer
```

将显示有关 Kubernetes 服务资源的信息。 以下示例输出末尾的“事件”指示“找不到用户提供的 IP 地址”。 在这些情况下，请验证是否已在节点资源组中创建静态公共 IP 地址，以及在 Kubernetes 服务清单中指定的 IP 地址是否正确。

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

若要对应用程序的网络流量进行其他控制，可以改为[创建入口控制器][aks-ingress-basic]。 你还可以[使用静态公共 IP 地址创建入口控制器][aks-static-ingress]。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
