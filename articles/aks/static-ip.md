---
title: 将静态 IP 地址和 DNS 标签与 Azure 库伯奈斯服务 （AKS） 负载均衡器一起使用
description: 了解如何创建静态 IP 地址并将其用于 Azure Kubernetes 服务 (AKS) 负载均衡器。
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047954"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>将静态公共 IP 地址和 DNS 标签与 Azure 库伯奈斯服务 （AKS） 负载均衡器一起使用

默认情况下，分配给 AKS 群集创建的负载均衡器资源的公共 IP 地址仅在该资源的保留期内有效。 如果删除 Kubernetes 服务，则会同时删除关联的负载均衡器和 IP 地址。 如果要分配特定 IP 地址或保留已重新部署的 Kubernetes 服务的 IP 地址，请创建并使用静态公共 IP 地址。

本文介绍如何创建静态公共 IP 地址并将其分配给 Kubernetes 服务。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

本文介绍使用*标准*SKU IP 和*标准*SKU 负载均衡器。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>创建静态 IP 地址

使用[az 网络公共 IP 创建命令创建][az-network-public-ip-create]静态公共 IP 地址。 以下在*myResourceGroup 资源*组中创建名为*myAKSPublicIP*的静态 IP 资源：

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> 如果在 AKS 群集中使用*基本*SKU 负载均衡器，则在定义公共 IP 时对*sKU*参数使用*Basic。* 只有*基本*SKU IP 与*基本*SKU 负载均衡器配合使用，只有*标准*SKU IP 与*标准*SKU 负载均衡器一起工作。 

将显示 IP 地址，如以下精简版示例输出中所示：

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

稍后可以使用 [az network public-ip list][az-network-public-ip-list] 命令获取公共 IP 地址。 指定节点资源组的名称和创建的公共 IP 地址，然后查询 ipAddress**，如以下示例中所示：

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用静态 IP 地址创建服务

在创建服务之前，请确保 AKS 群集使用的服务主体将权限委派给其他资源组。 例如：

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

或者，您可以使用为权限分配的系统分配托管标识，而不是服务主体。 有关详细信息，请参阅[使用托管标识](use-managed-identity.md)。

要使用静态公共 IP 地址创建*LoadBalancer*服务，`loadBalancerIP`请将静态公共 IP 地址的属性和值添加到 YAML 清单中。 创建名为 `load-balancer-service.yaml` 的文件，并将其复制到以下 YAML 中。 提供在前面的步骤中创建的你自己的公共 IP 地址。 下面的示例还将注释设置到名为*myResourceGroup*的资源组。 提供您自己的资源组名称。

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

## <a name="apply-a-dns-label-to-the-service"></a>将 DNS 标签应用于服务

如果您的服务使用动态或静态公共 IP 地址，则可以使用服务注释`service.beta.kubernetes.io/azure-dns-label-name`设置面向公众的 DNS 标签。 这将使用 Azure 的公共 DNS 服务器和顶级域为服务发布完全限定的域名。 注释值在 Azure 位置中必须是唯一的，因此建议使用足够限定的标签。   

然后，Azure 将自动将默认子网（`<location>.cloudapp.azure.com`位置是您选择的区域）追加到您提供的名称中，以创建完全限定的 DNS 名称。 例如：

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
> 要在自己的域上发布服务，请参阅[Azure DNS][azure-dns-zone]和[外部 dns][external-dns]项目。

## <a name="troubleshoot"></a>疑难解答

如果 Kubernetes 服务清单的 *loadBalancerIP* 属性中定义的静态 IP 地址不存在或尚未在节点资源组中创建，并且尚未配置其他托管，则负载均衡器服务创建将失败。 若要排除此故障，请用 [kubectl describe][kubectl-describe] 命令复查服务创建事件。 提供 YAML 清单中指定的服务的名称，如以下示例中所示：

```console
kubectl describe service azure-load-balancer
```

将显示有关 Kubernetes 服务资源的信息。 以下示例输出末尾的“事件”** 指示“找不到用户提供的 IP 地址”**。 在这些情况下，请验证是否已在节点资源组中创建静态公共 IP 地址，以及在 Kubernetes 服务清单中指定的 IP 地址是否正确。

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

如需获得对流向应用程序的网络流量的额外控制，你需要改为[创建入口控制器][aks-ingress-basic]。 此外，还可以[使用静态公共 IP 地址创建入口控制器][aks-static-ingress]。

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
