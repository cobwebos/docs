---
title: "配置 Azure 虚拟网络服务终结点 | Microsoft Docs"
description: "了解如何从虚拟网络启用和禁用服务终结点"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: e2242851d51dee56679231b9f34c8b474ba6578d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>配置虚拟网络服务终结点

使用虚拟网络 (VNet) 服务终结点可在 Azure 虚拟网络中保护 Azure 服务资源，完全消除从 Internet 对这些资源进行访问。 服务终结点提供从虚拟网络到 Azure 服务的直接连接，让我们使用自己 VNet 的专用地址空间来访问 Azure 服务。 通过服务终结点发往 Azure 服务的流量始终保留在 Microsoft Azure 主干网络上。 详细了解[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)

本文提供启用和禁用服务终结点的步骤。 在子网上为 Azure 服务启用终结点后，可在虚拟网络中保护特定的服务资源。

可以使用 [Azure 门户](#azure-portal)、[Azure PowerShell](#azure-powershell)、[Azure 命令行接口](#azure-cli)或 Azure 资源管理器[模板](#resource-manager-template)配置服务终结点。

>[!NOTE]
在预览期，只有特定的区域才支持 VNet 服务终结点功能。 有关受支持区域的列表，请参阅 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/?product=virtual-network)页。

## <a name="service-endpoint-configuration-overview"></a>服务终结点配置概述

- 只能在通过 Azure 资源管理器部署模型部署的 VNet 上配置服务终结点。

- 服务终结点在 VNet 的每个子网上设置。

- 对于子网，可以只为一个服务配置一个服务终结点。 可为不同的服务（例如 Azure 存储和 Azure SQL）配置多个服务终结点。

- 可在新的或现有的子网上启用终结点。

- 系统会自动为终结点配置位置。 默认情况下，服务终结点配置为 VNet 所在的区域。 对于 Azure 存储，为了支持区域故障转移方案，终结点会自动配置为 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)。

  >[!NOTE]
  根据 VNet/子网的大小，启用服务终结点的过程可能需要一段时间才能完成。 启用服务终结点时，请确保没有任何关键任务正在进行。 服务终结点会在子网中的每个 NIC 上切换路由，并可能会终止任何打开的 TCP 连接。 

- 在发往子网中所有 NIC 上的服务的流量流已切换到 VNet 专用 IP 地址后，服务终结点调用返回“succeeded”。

- 用于验证终结点配置的有效路由：

   为了验证是否正确配置了服务终结点，子网中任一 NIC 上的“有效路由”会按服务和区域显示 nextHopType 为“VirtualNetworkServiceEndpoint”的新“默认”路由。 详细了解如何[排查有效路由问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   仅当已配置一个或多个网络接口 (NIC) 并将其关联到子网中运行的虚拟机时，才能查看有效路由。

## <a name="azure-portal"></a>Azure 门户

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>创建 VNet 期间在子网上设置服务终结点

1. 打开 [Azure 门户](https://portal.azure.com/)。
使用 Azure 帐户登录到 Azure。 如果没有 Azure 帐户，可以注册免费试用版。 该帐户必须拥有创建虚拟网络和服务终结点的必要[权限](#provisioning)。
2. 单击“+新建”>“网络”>“虚拟网络”>“+添加”。
3. 在“创建虚拟网络”中输入以下值，单击“创建”：

设置 | 值
------- | -----
名称    | myVnet
地址空间 | 10.0.0.0/16
子网名称|mySubnet
子网地址范围|10.0.0.0/24
资源组|保留选中“新建”，输入名称。
Location|任何受支持的区域，例如“澳大利亚东部”
订阅|选择订阅。
__ServiceEndpoints__|已启用
__服务__ | 选择一个或所有可用服务。 支持的服务：__“Microsoft.Storage”、“Microsoft.Sql”__。

选择终结点的服务：![选择服务终结点服务](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. 验证所有设置是否正确，单击“创建”。

![设置服务终结点](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. 若要完成在 VNet 中保护 Azure 服务资源的过程，请单击[后续步骤](#Next%20Steps)中的服务文档。


### <a name="validating-service-endpoint-configuration"></a>验证服务终结点配置

使用以下步骤确认是否配置了服务终结点：

- 在“资源”中，单击“虚拟网络”。 搜索 VNet。
- 单击 VNet 名称，导航到“服务终结点”
- 配置的终结点显示为“成功”。 此外，还能看到自动配置的位置

![确认服务终结点配置](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>用于验证终结点配置的有效路由

若要查看子网中网络接口 (NIC) 上的有效路由，请单击该子网中的任一 NIC。 在“支持 + 故障排除”下，单击“有效路由”。 如果配置了终结点，将会看到服务地址前缀与目标相同的、nextHopType 为“VirtualNetworkServiceEndpoint”的新“默认”路由。

![服务终结点的有效路由](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>为 VNet 中的现有子网设置服务终结点

1. 在“资源”中单击“虚拟网络”，并搜索任何现有 VNet
2. 单击 VNet 名称，导航到“服务终结点”
3. 单击“添加”。 选择“服务”。 每次只能为一个服务创建终结点。 
4. 选择要在其中应用该终结点的所有子网。 单击“添加”

![子网服务终结点配置](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>删除服务终结点
1. 在“资源”中，单击“虚拟网络”。 按 VNet 名称进行筛选，搜索现有的 VNet。
2. 单击 VNet 名称，导航到“服务终结点”
3. 单击服务名称，并右键单击服务终结点条目
4. 选择“删除”

![服务终结点删除](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure Powershell

设置先决条件：

- 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 若要启动 PowerShell 会话，请转到“开始”，输入“powershell”，然后单击“PowerShell”。
- 在 PowerShell 中，输入 `login-azurermaccount` 命令登录到 Azure。 该帐户必须拥有创建虚拟网络和服务终结点的必要[权限](#provisioning)。

### <a name="get-available-service-endpoints-for-azure-region"></a>获取 Azure 区域的可用服务终结点

使用以下命令获取 Azure 区域中的终结点支持的服务列表。
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

输出： 
名称 | ID | Type
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>创建虚拟网络 *myVNet* 时，将 Azure 存储服务终结点添加到子网 *mySubnet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
可以使用逗号分隔的服务名称列表来启用多个服务。
示例："Microsoft.Storage", "Microsoft.Sql"

预期输出：
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

若要完成在 VNet 中保护 Azure 服务资源的过程，请单击[后续步骤](#Next%20Steps)中的服务文档。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>将多个服务终结点添加到现有子网

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

预期输出： 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>查看在子网上配置的服务终结点

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
输出：
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>删除子网上的服务终结点
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

设置先决条件：
- 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 有关登录的详细信息，请参阅 [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。
 - 该帐户必须拥有创建虚拟网络和服务终结点的必要[权限](#provisioning)。

 有关针对虚拟网络的完整命令列表，请参阅 [Azure CLI 虚拟网络命令](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>获取 Azure 区域的可用服务终结点

使用以下命令获取 Azure 区域（例如“EastUS”）中的终结点支持的服务列表。
```azure-cli
az network vnet list-endpoint-services -l eastus
```
输出：
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>创建虚拟网络 *myVNet* 时，将 Azure 存储服务终结点添加到子网 *mySubnet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

添加多个终结点：--service-endpoints Microsoft.Storage Microsoft.Sql

输出：
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

若要完成在 VNet 中保护 Azure 服务资源的过程，请单击[后续步骤](#Next%20Steps)中的服务文档。

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>将多个服务终结点添加到现有子网

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

预期输出：
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>查看在子网上配置的服务终结点

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>删除子网上的服务终结点
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

输出： 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager 模板

### <a name="securing-azure-service-resources-to-vnets"></a>在 VNet 中保护 Azure 服务资源

可以通过服务终结点在虚拟网络中保护特定的 Azure 资源。

下载用于在 VNet 中的子网内保护存储帐户的示例[资源管理器模板](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)。

该模板创建包含 2 个子网的 VNet，其中每个子网中包含一个带有 NIC 的 VM。 在一个子网上启用终结点，在该子网中保护存储帐户。

可以下载该模板，并根据自己的方案修改其部分内容。

此文提供了相关说明，以及用于通过 Azure 门户、PowerShell 或 Azure CLI 部署模板的模板。 确保拥有设置终结点和保护帐户的所需[权限](#provisioning)。

在子网中保护 Azure 资源：

- 应在该子网上配置服务终结点。
- 应该通过在资源上添加虚拟网络规则，在 VNet 中保护该资源。

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>删除其资源在子网中受保护的服务终结点
如果在子网中保护 Azure 服务资源并删除了服务终结点，将不再能够从该子网访问资源。
仅仅是重新启用终结点并不会恢复对以前在子网中受保护的资源的访问。

若要再次在该子网中保护服务资源，需要：

- 再次启用终结点
- 删除资源上的旧 VNet 规则
- 添加新规则用于在子网中保护资源

## <a name="provisioning"></a>设置

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/JoinServicetoaSubnet”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)以及将特定的权限分配到[自定义角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果两者位于不同的订阅中，资源应在相同的 Active Directory (AD) 租户下。

## <a name="next-steps"></a>后续步骤

有关在 VNet 中保护服务资源的更多说明，请参阅以下链接：

[在虚拟网络中保护 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[在虚拟网络中保护 Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
