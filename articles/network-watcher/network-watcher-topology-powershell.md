---
title: "查看 Azure 网络观察程序拓扑 - PowerShell | Microsoft 文档"
description: "本文将介绍如何使用 PowerShell 查询网络拓扑。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: af1bb9618492827bfae5f55a30fd165dbd5be11e
ms.lasthandoff: 02/23/2017

---

# <a name="view-network-watcher-topology-with-powershell"></a>使用 PowerShell 查看网络观察程序拓扑

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

网络观察程序的拓扑功能提供订阅中网络资源的可视表示形式。 在此门户中，将自动向你显示此可视化效果。 可以通过 PowerShell 检索此门户中拓扑视图背后的信息。
此功能使拓扑信息更为通用，因为这些数据可由其他工具用于构建出可视化效果。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

在两个关系下为互连建模。

- **包含** - 示例：VNet 包含一个具有 NIC 的子网
- **已关联** - 示例：NIC 与 VM 相关联

以下列表是查询拓扑 REST API 时返回的属性。

* **name** - 资源的名称
* **id** - 资源的 URI。
* **location** - 资源所在的位置。
* **associations** - 引用对象的关联列表。
    * **name** - 引用资源的名称。
    * **resourceId** - resourceId 是关联中引用的资源的 URI。
    * **associationType** - 此值引用子对象和父级之间的关系。 有效值为 **Contains** 或 **Associated**。

## <a name="before-you-begin"></a>开始之前

在此方案中，使用 `Get-AzureRmNetworkWatcherTopology` cmdlet 检索拓扑信息。 还有一篇关于如何[使用 REST API 检索网络拓扑](network-watcher-topology-rest.md)的文章。

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定资源组的拓扑响应。

## <a name="retrieve-network-watcher"></a>检索网络观察程序

第一步是检索网络观察程序实例。 将 `$networkWatcher` 变量传递给 `Get-AzureRmNetworkWatcherTopology` cmdlet。

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>检索拓扑

`Get-AzureRmNetworkWatcherTopology` cmdlet 检索给定资源组的拓扑。

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>结果

返回的结果具有属性名称“Resources”，其中包含 `Get-AzureRmNetworkWatcherTopology` cmdlet 的 json 响应正文。  响应包含网络安全组中的资源及其关联（即 Contains、Associated）。

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>后续步骤

通过访问[使用 Power BI 直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)，了解如何使用 Power BI 直观地显示 NSG 流日志



