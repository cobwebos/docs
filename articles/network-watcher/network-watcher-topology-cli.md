---
title: "查看 Azure 网络观察程序拓扑 - Azure CLI | Microsoft 文档"
description: "本文将介绍如何使用 Azure CLI 查询网络拓扑。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02add070405f3f00d3631362a2b139239d44c34d
ms.lasthandoff: 03/28/2017

---

# <a name="view-network-watcher-topology-with-azure-cli"></a>使用 Azure CLI 查看网络观察程序拓扑

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

网络观察程序的拓扑功能提供订阅中网络资源的可视表示形式。 在此门户中，将自动向你显示此可视化效果。 可以通过 PowerShell 检索此门户中拓扑视图背后的信息。
此功能使拓扑信息更为通用，因为这些数据可由其他工具用于构建出可视化效果。

本文使用适用于 Windows、Mac 和 Linux 的跨平台 Azure CLI 1.0。 对于 CLI 支持，网络观察程序当前使用 Azure CLI 1.0。

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

在此方案中，使用 `network watcher topology` cmdlet 检索拓扑信息。 还有一篇关于如何[使用 REST API 检索网络拓扑](network-watcher-topology-rest.md)的文章。

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定资源组的拓扑响应。

## <a name="retrieve-topology"></a>检索拓扑

`network watcher topology` cmdlet 检索给定资源组的拓扑。 添加参数“--json”以查看 json 格式的输出

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>结果

返回的结果具有属性名称“Resources”，其中包含 `network watcher topology` cmdlet 的 json 响应正文。  响应包含网络安全组中的资源及其关联（即 Contains、Associated）。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>后续步骤

请访问[安全组视图概述](network-watcher-security-group-view-overview.md)，了解有关应用于网络资源的安全规则的详细信息

