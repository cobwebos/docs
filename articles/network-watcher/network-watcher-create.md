---
title: "创建 Azure 网络观察程序实例 | Microsoft 文档"
description: "此页提供了使用门户和 Azure REST API 创建网络观察程序实例的步骤"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 2aeaffdd5ab552e18677cbd1a24a748dd14bf172
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>创建 Azure 网络观察程序实例

网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

> [!NOTE]
> 因为网络观察程序当前仅支持 CLI 1.0，所以创建新的网络观察程序实例的说明是针对 CLI 1.0 提供的。

## <a name="create-a-network-watcher-in-the-portal"></a>在门户中创建网络观察程序

导航到“更多服务” > “网络” > “网络观察程序”。 可以选择要为其启用网络观察程序的所有订阅。 此操作在每个可用的区域中创建网络观察程序。

![创建网络观察程序][1]

使用门户启用网络观察程序时，网络观察程序实例的名称会自动设置为 NetworkWatcher_region_name，其中，region_name 对应启用实例的 Azure 区域。  例如，在美国中西部区域启用的网络观察程序的名称为 NetworkWatcher_westcentralus

此外，网络观察程序实例会自动添加到名为 NetworkWatcherRG 的资源组。  如果资源组不存在，则要进行创建。

若想自定义网络观察程序实例的名称和放置该实例的资源组名称，可使用下方介绍的 Powershell、REST API 或 ARMClient 方法。  在每个选项中，必须先有资源组，然后才可将网络观察程序放在其中。  

## <a name="create-a-network-watcher-with-powershell"></a>使用 PowerShell 创建网络观察程序

若要创建网络观察程序的实例，请运行以下示例：

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>使用 REST API 创建网络观察程序

ARMclient 用于使用 PowerShell 调用 REST API。 按照 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 在 chocolatey 上找到 ARMClient

### <a name="log-in-with-armclient"></a>使用 ARMClient 登录

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>创建网络观察程序

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>后续步骤

现在，你已有网络观察程序实例，请了解可用功能：

* [拓扑](network-watcher-topology-overview.md)
* [数据包捕获](network-watcher-packet-capture-overview.md)
* [IP 流验证](network-watcher-ip-flow-verify-overview.md)
* [下一跃点](network-watcher-next-hop-overview.md)
* [安全组视图](network-watcher-security-group-view-overview.md)
* [NSG 流日志记录](network-watcher-nsg-flow-logging-overview.md)
* [虚拟网络网关故障排除](network-watcher-troubleshoot-overview.md)

创建网络观察程序实例后，可以按照[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)一文配置包捕获

[1]: ./media/network-watcher-create/figure1.png












