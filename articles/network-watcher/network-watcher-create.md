---
title: "创建 Azure 网络观察程序实例 | Microsoft 文档"
description: "此页提供了使用门户和 Azure REST API 创建网络观察程序实例的步骤"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: a39ce143796408f8e44b0d2c877e631e92473462
ms.lasthandoff: 03/28/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>创建 Azure 网络观察程序实例

网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 使用方案级别监视可以诊断端到端网络级别视图的问题。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="register-the-preview-capability"></a>注册预览版功能

网络观察程序目前处于预览状态，若要使用网络观察程序的功能，需要注册该功能。 为此，请运行以下 PowerShell 示例：

```powershell
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

若要验证注册是否成功，请运行以下 Powershell 示例：

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

如果该功能已正确注册，输出应匹配以下内容：

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

### <a name="instructions-for-cli-10"></a>有关 CLI 1.0 的说明

注册

```CLI
azure feature register  Microsoft.Network AllowNetworkWatcher
azure provider register Microsoft.Network
```

若要验证注册是否成功，请运行以下 CLI 命令：

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

如果该功能已正确注册，输出应匹配以下内容：

```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

> [!NOTE]
> 因为网络观察程序当前仅支持 CLI 1.0，所以创建新的网络观察程序实例的说明是针对 CLI 1.0 提供的。 若要注册 CLI 2.0 的提供程序，请使用 `az feature register -n AllowNetworkWatcher --namespace Microsoft.Network`。

## <a name="create-a-network-watcher-in-the-portal"></a>在门户中创建网络观察程序

导航到“更多服务” > “网络” > “网络观察程序”。 可以选择要为其启用网络观察程序的所有订阅。 此操作在每个可用的区域中创建网络观察程序。

![创建网络观察程序][1]

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












