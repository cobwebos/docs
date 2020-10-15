---
title: CLI：使用 Azure CLI 部署 Web 应用的专用终结点
description: 了解如何使用 Azure CLI 部署 Web 应用的专用终结点
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 08e1878adab680329a33bc44020b49dce38de4d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88959658"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>使用 Azure CLI 创建应用服务应用并部署专用终结点

本示例脚本使用其相关资源，在应用服务中创建应用，然后部署专用终结点。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 Azure CLI，本快速入门要求使用 Azure CLI 2.0.28 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

创建任何资源之前，必须创建一个资源组以托管 Web 应用、虚拟网络和其他网络组件。 使用 [az group create](/cli/azure/group) 创建资源组。 此示例在 francecentral 位置创建名为 myResourceGroup 的资源组 ：

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>创建应用服务计划

需要创建应用服务计划来托管 Web 应用。
使用 [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 创建应用服务计划。
此示例在 francecentral 位置创建名为 myAppServicePlan 的应用服务计划，该计划包含 P1V2 SKU 并仅有一个辅助角色  ： 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>创建 Web 应用

拥有应用服务计划后，即可部署 Web 应用。
使用 [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create 创建 Web 应用。
此示例在名为 myAppServicePlan 的计划中创建一个名为 mySiteName 的 Web 应用 

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>创建 VNet

使用 [az network vnet create](/cli/azure/network/vnet) 创建虚拟网络。 此示例创建一个名为 myVNet 的默认虚拟网络，其中包含一个名为 mySubnet 的子网 ：

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>配置子网 

需要更新子网以禁用专用终结点网络策略。 使用 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) 更新名为 *mySubnet* 的子网配置：

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>创建专用终结点

使用 [az network Private-Endpoint create](/cli/azure/network/private-endpoint) 为 Web 应用创建专用终结点。 本示例在子网 mySubnet 的 VNet myVNet 中创建一个名为 myPrivateEndpoint 的专用终结点，该终结点使用名为 myConnectionName 的连接，连接到我的 Web 应用 (/subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp) 的资源 ID，组参数是 Web 应用类型的站点    。 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>配置专用区域

最后，需要创建一个名为 privatelink.azurewebsites.net 的专用 DNS 区域，该区域链接到 VNet 以解析 Web 应用的 DNS 名称。


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>后续步骤

- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
- 可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。