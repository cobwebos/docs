---
title: "查看 Azure 网络观察程序拓扑 - REST API | Microsoft 文档"
description: "本文将介绍如何使用 REST API 查询网络拓扑。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 568f3060da372f4a08cec342e04359172522cb69
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---

# <a name="view-network-watcher-topology-with-rest-api"></a>使用 REST API 查看网络观察程序拓扑

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

网络观察程序的拓扑功能提供订阅中网络资源的可视表示形式。 在此门户中，将自动向你显示此可视化效果。 可以通过 PowerShell 检索此门户中拓扑视图背后的信息。
此功能使拓扑信息更为通用，因为这些数据可由其他工具用于构建出可视化效果。

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

在此方案中，检索拓扑信息。 ARMclient 用于使用 PowerShell 调用 REST API。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定资源组的拓扑响应。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

使用 Azure 凭据登录到 armclient。

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>检索拓扑

以下示例通过 REST API 请求拓扑。  该示例已参数化，以便灵活地创建示例。  替换所有值并用 \<\> 包围它们。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

以下响应是检索资源组的拓扑时返回的缩短响应的示例：

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>后续步骤

访问[使用 Power BI 可视化 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)，了解如何使用 Power BI 可视化 NSG 流日志


