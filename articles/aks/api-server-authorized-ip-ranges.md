---
title: API 服务器授权 Azure Kubernetes 服务 (AKS) 中的 IP 范围
description: 了解如何到安全群集使用 IP 地址范围用于访问 API 服务器在 Azure Kubernetes 服务 (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 1b983c534ab92218759175655bbf396788e4c39d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956498"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>预览版-对 API 服务器使用的安全访问授权所需的 IP 地址范围中 Azure Kubernetes 服务 (AKS)

在 Kubernetes 中，为 API 服务器接收请求，以执行如群集中的操作来创建资源或缩放的节点数。 API 服务器是重要的方法进行交互并管理群集。 若要提高群集安全性和最小化攻击，API 服务器应仅可从一组有限的 IP 地址范围。

本文介绍如何使用 API 授权服务器的 IP 地址范围来限制对控制平面的请求。 此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是自助服务和可以选择加入的功能。 提供预览是为了从我们的社区收集反馈和 bug。 但是，Azure 技术支持部门不为其提供支持。 如果你创建一个群集，或者将这些功能添加到现有群集，则除非该功能不再为预览版并升级为公开发布版 (GA)，否则该群集不会获得支持。
>
> 如果遇到预览版功能的问题，请[在 AKS GitHub 存储库中提交问题][aks-github]，并在 Bug 标题中填写预览版功能的名称。

## <a name="before-you-begin"></a>开始之前

API 服务器经过授权的 IP 范围只能用于新创建的 AKS 群集。 本文介绍如何创建 AKS 群集使用 Azure CLI。

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要配置 API 授权服务器的 IP 范围的 CLI 命令均位于*aks 预览版*CLI 扩展。 安装*aks 预览版*Azure CLI 扩展使用[az 扩展添加][ az-extension-add]命令，如下面的示例中所示：

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 如果您先前安装了*aks 预览版*扩展，安装任何可用更新使用`az extension update --name aks-preview`命令。

### <a name="register-feature-flag-for-your-subscription"></a>注册你的订阅的功能标志

若要使用 API 授权服务器的 IP 范围，请先启用你的订阅上的一个功能标志。 若要注册*APIServerSecurityPreview*功能标志，请使用[az 功能注册][ az-feature-register]命令，在下面的示例所示：

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

配置 API 授权服务器的 IP 范围时，应考虑以下限制：

* 当前不能使用 Azure 开发人员空格，如与 API 服务器的通信也会被阻止。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器的概述授权 IP 范围

Kubernetes API 服务器是如何公开底层 Kubernetes Api。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 提供了单租户群集主机，与专用的 API 服务器。 默认情况下，为 API 服务器分配一个公共 IP 地址，你应访问和控制使用基于角色的访问控制 (RBAC)。

保护对否则为可公开访问的 AKS 控制平面访问 / API 服务器，你可以启用和使用授权的 IP 范围。 这些授权的 IP 范围只允许定义的 IP 地址范围内与 API 服务器进行通信。 阻止通过不属于这些经过授权的 IP 范围的 IP 地址对 API 服务器所做的请求。 应继续使用 RBAC 然后，授权用户和他们请求的操作。

若要使用已授权的 IP 范围功能，公共 IP 地址公开节点池上通过部署一个基本的 NGINX 服务。 API 服务器进行通信与通过此授权的公共 IP 地址的节点池。 然后定义可以访问 API 服务器的其他 IP 地址范围。

有关 API 服务器和其他群集组件的详细信息，请参阅[Kubernetes 核心概念适用于 AKS][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

API 授权服务器 IP 范围只适用于新的 AKS 群集。 无法启用已授权的 IP 范围，因为群集的一部分创建操作。 如果你尝试启用授权作为群集的一部分的 IP 范围创建进程，群集节点不能在部署期间访问 API 服务器，如出口 IP 地址并不在该点定义。

首先，创建群集使用[az aks 创建][ az-aks-create]命令。 下面的示例创建名为的单节点群集*myAKSCluster*资源组中名为*myResourceGroup*。

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>创建出站防火墙规则的网关

若要确保到可以可靠地与 API 服务器通信在群集中的节点启用下一节中的授权的 IP 范围时，创建出站的网关用作 Azure 防火墙。 Azure 防火墙的 IP 地址然后添加到下一节中的授权 API 服务器 IP 地址的列表。

> [!WARNING]
> 使用 Azure 的防火墙可以每月计费周期内产生的巨额成本。 需要使用 Azure 防火墙只应在此初始预览版期间需要。 有关详细信息和成本规划，请参阅[Azure 防火墙定价][azure-firewall-costs]。

首先，获取*MC_* AKS 群集和虚拟网络的资源组名称。 然后，创建子网使用[az 网络 vnet 子网创建][ az-network-vnet-subnet-create]命令。 下面的示例创建名为的子网*AzureFirewallSubnet*使用的 CIDR 范围*10.200.0.0/16*:

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

若要创建 Azure 防火墙，请安装*azure 防火墙*CLI 扩展使用[az 扩展添加][ az-extension-add]命令。 然后，创建一个防火墙 using [az 网络防火墙创建][ az-network-firewall-create]命令。 下面的示例创建名为的 Azure 防火墙*myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure 防火墙出口流量将通过一个公共 IP 地址分配。 创建公共地址使用[az 网络公共 ip 创建][ az-network-public-ip-create]命令，然后创建一个 IP 配置防火墙使用[az 网络防火墙 ip 配置创建] [ az-network-firewall-ip-config-create]适用的公共 IP:

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

现在，创建到 Azure 防火墙网络规则*允许*所有*TCP*流量使用[az 网络防火墙网络规则创建][ az-network-firewall-network-rule-create]命令。 下面的示例创建一个名为的网络规则*AllowTCPOutbound*与任何源或目标地址的流量：

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

若要将 Azure 防火墙与网络路由相关联，获取现有的路由表信息、 在 Azure 防火墙的内部 IP 地址和 API 服务器的 IP 地址。 在下一部分，以控制如何将流量路由用于群集通信中指定这些 IP 地址。

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

最后，在现有 AKS 网络路由表中使用创建路由[az 网络路由表路由创建][ az-network-route-table-route-create]允许流量以使用 API 服务器的 Azure 防火墙设备的命令通信。

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

记下 Azure 防火墙设备的公共 IP 地址。 此地址将添加到的下一节中的 API 授权服务器 IP 范围列表。

## <a name="enable-authorized-ip-ranges"></a>启用经过授权的 IP 范围

若要启用 API 授权服务器的 IP 范围，你提供经过授权的 IP 地址范围的列表。 当指定的 CIDR 范围时，开始在范围内的第一个 IP 地址。 例如， *137.117.106.90/29*是一个有效的范围，但请确保你指定的第一个 IP 地址在范围内，如*137.117.106.88/29*。

使用[更新 az aks] [ az-aks-update]命令并指定 *-api 的服务器的授权的 ip 范围*允许。 这些 IP 地址范围通常是使用你的本地网络地址范围。 添加 Azure 防火墙如在上一步骤中获取的公共 IP 地址*20.42.25.196/32*。

下面的示例启用名为在群集上的 API 授权服务器 IP 范围*myAKSCluster*资源组中名为*myResourceGroup*。 若要授权的 IP 地址范围都*20.42.25.196/32* （Azure 防火墙公共 IP 地址），然后*172.0.0.10/16*并*168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>更新或禁用授权的 IP 范围

若要更新或禁用授权的 IP 范围，请再次使用[更新 az aks] [ az-aks-update]命令。 指定更新后的 CIDR 范围你想要允许，或指定一个空范围，若要禁用 API 服务器授权 IP 地址范围，如下面的示例中所示：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>后续步骤

在本文中，启用了 API 授权服务器的 IP 范围。 这种方法是如何运行安全的 AKS 群集的一部分。

有关详细信息，请参阅[应用程序和群集在 AKS 中的安全概念][ concepts-security]并[群集安全性和在 AKS 中的升级的最佳做法][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
