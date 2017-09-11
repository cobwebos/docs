---
title: "使用 Azure 网络观察程序“下一跃点”功能查找下一跃点 - REST | Microsoft 文档"
description: "本文将介绍如何使用 Azure REST API 通过“下一跃点”功能查找下一跃点类型和 ip 地址"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 644713d365191bf5e51517d0cc565efbc2abc144
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---

# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>使用 Azure REST API 通过 Azure 网络观察程序中的“下一跃点”功能找到下一跃点类型

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

“下一跃点”是网络观察程序的一项功能，提供基于指定的虚拟机获取下一跃点类型和 IP 地址的功能。 此功能对于确定离开虚拟机的流量是否通过网关、Internet 或虚拟网络到达其目标很有用。

## <a name="before-you-begin"></a>开始之前

ARMclient 用于使用 PowerShell 调用 REST API。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案使用“下一跃点”（网络观察程序的一项功能）找出资源的下一跃点类型和 IP 地址。 若要了解有关下一跃点的详细信息，请访问[下一跃点概述](network-watcher-next-hop-overview.md)。

在此方案中，你将：

* 检索虚拟机的下一跃点。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

使用 Azure 凭据登录到 armclient。

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>检索虚拟机

运行以下脚本返回虚拟机。 运行下一跃点时需要此信息。

以下代码需要以下变量的值：

- **subscriptionId** - 要使用的订阅 Id。
- **resourceGroupName** - 包含虚拟机的资源组的名称。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

从以下输出看，在以下示例中使用虚拟机的 id：

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>获取下一跃点

创建授权标头后，可以从虚拟机检索下一跃点。 要使代码示例正常工作，必须替换以下值。

> [!Important]
> 对于网络观察程序 REST API 调用来说，请求 URI 中的资源组名称是包含网络观察程序的资源组，而不是要对其执行诊断操作的资源。

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> “下一跃点”功能要求 VM 资源已分配，从而可以运行。

## <a name="results"></a>结果

以下代码片段是收到的输出的示例。 结果包含以下值：

* **nextHopType** - 此值是以下值之一：Internet、VirtualAppliance、VirtualNetworkGateway、VnetLocal、HyperNetGateway 或 None。
* **nextHopIpAddress** - 下一跃点的 IP 地址。
* **routeTableId** - 该值为与路由关联的路由表的 URI，如果未定义用户定义的路由，则返回*系统路由*的值。

下面是 json 格式的结果。

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>后续步骤

能够找出虚拟机的下一跃点后，可以通过访问[安全视图概述](network-watcher-security-group-view-overview.md)查看网络资源的安全性















