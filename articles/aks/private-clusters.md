---
title: 创建专用的 Azure Kubernetes 服务群集
description: 了解如何创建私有 Azure Kubernetes 服务（AKS）群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: 934dfdb43d6d2e4ccc346b728f0ac4f5febea327
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932593"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>创建私有 Azure Kubernetes Service 群集（预览版）

在专用群集中，控制平面或 API 服务器具有内部 IP 地址，这些地址在[专用 intranets 文档的 RFC1918 分配](https://tools.ietf.org/html/rfc1918)中定义。 通过使用专用群集，可以确保 API 服务器与节点池之间的网络流量仅保留在专用网络上。

控制平面或 API 服务器位于 Azure Kubernetes 服务（AKS）管理的 Azure 订阅中。 客户的群集或节点池在客户的订阅中。 服务器和群集或节点池可以通过 API 服务器虚拟网络中的[Azure 专用链接服务][private-link-service]和在客户 AKS 群集的子网中公开的专用终结点相互通信。

> [!IMPORTANT]
> AKS 预览功能是自助服务，可通过选择进行提供。 预览*按*原样*提供，并*已从服务级别协议（SLA）和有限担保中排除。 *最大程度地*减少了客户支持 AKS 的预览。 因此，这些功能并不用于生产。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="prerequisites"></a>必备组件

* Azure CLI 版本2.0.77 或更高版本，以及 Azure CLI AKS 预览版扩展版本0.4.18

## <a name="currently-supported-regions"></a>当前支持的区域
* 美国西部
* 美国西部 2
* 美国东部 2
* 加拿大中部
* 欧洲北部
* 欧洲西部
* 澳大利亚东部

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安装最新 Azure CLI AKS 预览版扩展

若要使用专用群集，需要 Azure CLI AKS 预览版扩展版本0.4.18 或更高版本。 使用[az extension add][az-extension-add]命令安装 Azure CLI AKS Preview 扩展，然后使用以下[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 在订阅上注册功能时，当前无法注册该功能。 启用某些预览功能后，可以使用订阅中创建的所有 AKS 群集的默认设置。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

注册状态显示为 "*已注册*" 可能需要几分钟时间。 可以通过使用以下[az feature list][az-feature-list]命令来检查状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

注册状态后，使用以下[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>创建专用 AKS 群集

### <a name="default-basic-networking"></a>默认基本网络 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中 *--enable-private-群集*是专用群集的必需标志。 

### <a name="advanced-networking"></a>高级网络  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
其中 *--enable-private-群集*是专用群集的必需标志。 

> [!NOTE]
> 如果 Docker 桥地址 CIDR （172.17.0.1/16）与子网 CIDR 冲突，请相应地更改 Docker 桥地址。

## <a name="connect-to-the-private-cluster"></a>连接到专用群集
API 服务器终结点没有公共 IP 地址。 因此，必须在虚拟网络中创建 Azure 虚拟机（VM）并连接到 API 服务器。 为此，请执行以下操作：

1. 获取用于连接到群集的凭据。

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. 执行下列任一操作：
   * 在 AKS 群集所在的虚拟网络中创建 VM。  
   * 在不同的虚拟网络中创建一个 VM，并将此虚拟网络与 AKS 群集虚拟网络对等互连。

     如果在不同的虚拟网络中创建 VM，请在此虚拟网络与专用 DNS 区域之间设置链接。 为此，请执行以下操作：
    
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 中转到 Azure 门户中的 MC_ * 资源组。  
     b.保留“数据库类型”设置，即设置为“共享”。 选择专用 DNS 区域。   
     c. 在左窗格中，选择 "**虚拟网络**" 链接。  
     d.单击“下一步”。 创建新链接，将 VM 的虚拟网络添加到专用 DNS 区域。 DNS 区域链接将需要几分钟的时间。  
     e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 返回到 Azure 门户中的 MC_ * 资源组。  
     f. 在右侧窗格中，选择 "虚拟网络"。 虚拟网络名称的格式为 aks- *\** 。  
     g. 在左窗格中，选择 "**对等互连**"。  
     h. 选择 "**添加**"，添加 VM 的虚拟网络，然后创建对等互连。  
     i. 前往具有 VM 的虚拟网络，选择**对等互连**，选择 AKS 虚拟网络，然后创建对等互连。 如果 AKS 虚拟网络上的地址范围和 VM 的虚拟网络冲突，则对等互连失败。 有关详细信息，请参阅[虚拟网络对等互连][virtual-network-peering]。

1. 通过安全外壳（SSH）访问 VM。
1. 安装 Kubectl 工具，并运行 Kubectl 命令。


## <a name="dependencies"></a>依赖项  
* 仅标准 Azure 负载均衡器支持专用链接服务。 不支持基本的 Azure 负载均衡器。  
* 若要使用自定义 DNS 服务器，请将具有 DNS 的 AD 服务器部署到此 IP 168.63.129.16

## <a name="limitations"></a>限制 
* 目前仅支持美国东部2和美国西部2区域的可用性区域
* [Azure 专用链接服务限制][private-link-service]适用于专用群集、Azure 专用终结点和虚拟网络服务终结点，这些终结点当前在同一虚拟网络中不受支持。
* 不支持专用群集中的虚拟节点在专用的 Azure 虚拟网络中实现专用 Azure 容器实例（ACI）
* 不支持具有专用群集的现成的 Azure DevOps 集成
* 对于需要使 Azure 容器注册表能与专用 AKS 配合使用的客户，容器注册表虚拟网络必须与代理群集虚拟网络对等互连。
* 当前不支持 Azure Dev Spaces
* 不支持将现有的 AKS 群集转换为专用群集
* 删除或修改客户子网中的专用终结点将导致群集停止工作。 
* 容器 Azure Monitor 当前不支持。
* 当前不支持*自带 DNS* 。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

