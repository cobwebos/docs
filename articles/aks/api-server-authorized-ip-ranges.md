---
title: Azure Kubernetes Service （AKS）中的 API 服务器授权 IP 范围
description: 了解如何使用用于访问 Azure Kubernetes Service （AKS）中的 API 服务器的 IP 地址范围保护群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 487940bfb5d6e7c5eebf99f804f57c3e17709377
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276484"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service （AKS）中的授权 IP 地址范围进行预览-安全访问 API 服务器

在 Kubernetes 中，API 服务器接收到在群集中执行操作的请求，如创建资源或缩放节点数。 API 服务器是与群集进行交互和管理群集的中心方法。 若要提高群集安全性并将攻击降到最低，只能从有限的一组 IP 地址范围访问 API 服务器。

本文介绍如何使用 API 服务器授权的 IP 地址范围来限制对控制平面的请求。 此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

本文 assumess 了使用 [kubenet] [kubenet] 的群集。  使用 [Azure 容器网络接口（CNI）] [CNI] 的群集时，你将不会获得安全访问所需的路由表。  你将需要手动创建路由表。  有关详细信息，请参阅[管理路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

API 服务器授权的 IP 范围仅适用于你创建的新 AKS 群集。 本文介绍如何使用 Azure CLI 创建 AKS 群集。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要配置 API 服务器授权的 IP 范围，需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>注册订阅的功能标志

若要使用 API 服务器授权的 IP 范围，请首先在订阅上启用功能标志。 若要注册*APIServerSecurityPreview*功能标志，请使用[az feature register][az-feature-register]命令，如以下示例中所示：

> [!CAUTION]
> 在订阅上注册功能时，当前无法注册该功能。 启用某些预览功能后，默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

配置 API 服务器授权的 IP 范围时，以下限制适用：

* 当前无法使用 Azure Dev Spaces，因为也会阻止与 API 服务器的通信。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器授权的 IP 范围概述

Kubernetes API 服务器是基础 Kubernetes Api 的公开方式。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 提供具有专用 API 服务器的单租户群集主机。 默认情况下，将为 API 服务器分配一个公共 IP 地址，并且应使用基于角色的访问控制（RBAC）来控制访问权限。

若要保护对其他可公开访问的 AKS 控制平面/API 服务器的访问，可以启用并使用授权 IP 范围。 这些授权的 IP 范围仅允许已定义的 IP 地址范围与 API 服务器进行通信。 阻止了从不属于这些授权 IP 范围的 IP 地址对 API 服务器发出的请求。 你应继续使用 RBAC，然后对用户及其请求的操作进行授权。

有关 API 服务器和其他群集组件的详细信息，请参阅[Kubernetes core 概念 FOR AKS][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

API 服务器授权的 IP 范围仅适用于新的 AKS 群集。 在群集创建操作过程中，不能启用授权的 IP 范围。 如果尝试在群集创建过程中启用授权的 IP 范围，则群集节点在部署过程中无法访问 API 服务器，因为未在该点定义出口 IP 地址。

首先，使用[az aks create][az-aks-create]命令创建群集。 以下示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的单节点群集。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>为防火墙规则创建出站网关

若要确保在下一部分中启用授权的 IP 范围时，群集中的节点可以可靠地与 API 服务器通信，请创建一个用作出站网关的 Azure 防火墙。 然后，将 Azure 防火墙的 IP 地址添加到下一节中的授权 API 服务器 IP 地址列表。

> [!WARNING]
> 使用 Azure 防火墙可能会在每月计费周期内产生显著的成本。 只有在此初始预览期间才需要使用 Azure 防火墙。 有关详细信息和成本计划，请参阅[Azure 防火墙定价][azure-firewall-costs]。
>
> 或者，如果群集使用[标准 SKU 负载平衡器][standard-sku-lb]，则无需将 Azure 防火墙配置为出站网关。 使用[az network 公共 ip 列表][az-network-public-ip-list]并指定 AKS 群集的资源组，该资源组通常以*MC_* 开头。 这会显示你的群集的公共 IP，你可以将该 IP 列入允许列表。 例如：
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

首先，获取 AKS 群集和虚拟网络的*MC_* 资源组名称。 然后，使用[az network vnet subnet create][az-network-vnet-subnet-create]命令创建一个子网。 以下示例创建一个名为*AzureFirewallSubnet*的子网，其中 CIDR 范围为*10.200.0.0/16*：

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

若要创建 Azure 防火墙，请使用[az extension add][az-extension-add]命令安装*azure 防火墙*CLI 扩展。 然后，使用[az network firewall create][az-network-firewall-create]命令创建防火墙。 以下示例创建名为*myAzureFirewall*的 Azure 防火墙：

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

向 Azure 防火墙分配传出流量流过的公共 IP 地址。 使用[az network 公共 ip create][az-network-public-ip-create]命令创建公用地址，然后使用[az network firewall ip-config create][az-network-firewall-ip-config-create] （应用公共 ip）在防火墙上创建 ip 配置：

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

现在，创建 Azure 防火墙网络规则，以*允许*使用[az 网络防火墙网络规则 create][az-network-firewall-network-rule-create]命令的所有*TCP*流量。 下面的示例创建一个名为*AllowTCPOutbound*的网络规则，该规则具有任何源或目标地址的流量：

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

若要将 Azure 防火墙与网络路由关联，请获取现有路由表信息、Azure 防火墙的内部 IP 地址，然后获取 API 服务器的 IP 地址。 下一节中将指定这些 IP 地址，以控制应如何路由流量以进行群集通信。

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

最后，在现有 AKS 网络路由表中创建一个路由，使用[az network route-table route create][az-network-route-table-route-create]命令，该命令允许流量使用 Azure 防火墙设备进行 API 服务器通信。

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

记下 Azure 防火墙设备的公共 IP 地址。 在下一部分中，此地址已添加到 API 服务器授权的 IP 范围列表。

## <a name="enable-authorized-ip-ranges"></a>启用授权的 IP 范围

若要启用 API 服务器授权的 IP 范围，请提供授权 IP 地址范围的列表。 指定 CIDR 范围时，请从该范围中的第一个 IP 地址开始。 例如， *137.117.106.90/29*是有效的范围，但请确保指定范围内的第一个 IP 地址，例如*137.117.106.88/29*。

使用[az aks update][az-aks-update]命令并指定 *--api-服务器授权的 ip 范围*以允许。 这些 IP 地址范围通常是本地网络所使用的地址范围。 添加在上一步中获取的自己的 Azure 防火墙的公共 IP 地址，例如*20.42.25.196/32*。

以下示例在名为*myResourceGroup*的资源组中名为*myAKSCluster*的群集上启用 API 服务器授权的 IP 范围。 要授权的 IP 地址范围是*20.42.25.196/32* （Azure 防火墙公共 IP 地址），然后是*172.0.0.0/16*和*168.10.0.0/18*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>更新或禁用授权的 IP 范围

若要更新或禁用授权 IP 范围，请再次使用[az aks update][az-aks-update]命令。 指定要允许的已更新 CIDR 范围，或指定一个空范围以禁用 API 服务器授权的 IP 范围，如以下示例中所示：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>后续步骤

本文介绍了已启用 API 服务器授权的 IP 范围。 此方法是如何运行安全 AKS 群集的一部分。

有关详细信息，请参阅[AKS 中的应用程序和群集的安全概念][concepts-security]和[AKS 中群集安全性和升级的最佳方案][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
