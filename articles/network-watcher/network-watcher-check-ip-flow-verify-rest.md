---
title: 使用 Azure 网络观察程序“IP 流验证”功能验证流量 - REST | Microsoft 文档
description: 本文介绍如何检查是允许还是拒绝进出虚拟机的流量
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c3d38f776db63e777174b7dca8b09a0d19c387e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
ms.locfileid: "31809785"
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>使用 Azure 网络观察程序的组件（即“IP 流验证”功能）检查是允许还是拒绝流量

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


“IP 流验证”是网络观察程序的一项功能，用于验证是否允许流量进出虚拟机。 可以针对传入或传出流量运行验证。 此方案对于获取虚拟机是否可以与外部资源或后端通信的当前状态很有用。 可以使用“IP 流验证”功能验证网络安全组 (NSG) 规则是否正确配置，以及对 NSG 规则所阻止的流进行故障排除。 使用“IP 流验证”功能的另一个原因是确保要阻止的流量已由 NSG 正确阻止。

## <a name="before-you-begin"></a>开始之前

通过 PowerShell 调用 REST API 时，使用的是 ARMclient。 根据 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 中所述在 chocolatey 上找到 ARMClient

此方案假定已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>场景

此方案使用“IP 流验证”功能来验证虚拟机是否可以通过端口 443 与其他计算机通信。 如果流量被拒绝，此方案将返回拒绝该流量的安全规则。 若要了解有关 IP 流验证的详细信息，请访问 [IP 流验证概述](network-watcher-ip-flow-verify-overview.md)

本方案中的操作：

* 检索虚拟机
* 调用“IP 流验证”
* 验证结果

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>检索虚拟机

运行以下脚本返回虚拟机。 以下代码需要以下变量的值：

* **subscriptionId** - 要使用的订阅 Id。
* **resourceGroupName** - 包含虚拟机的资源组的名称。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

所需的信息是 `Microsoft.Compute/virtualMachines` 类型下的 id。 结果应如以下代码示例所示：

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>调用“IP 流验证”

以下示例创建了一个请求以验证指定虚拟机的流量。 响应返回是允许流量还是拒绝流量。 如果拒绝了流量，响应还返回哪个规则阻止了流量。

> [!NOTE]
> “IP 流验证”功能要求 VM 资源已分配。

该脚本需要虚拟机的资源 Id 和虚拟机上网络接口卡的资源 Id。 前面的输出提供了这些值。

> [!Important]
> 对于所有网络观察程序 REST 调用来说，请求 URI 中的资源组名称是包含网络观察程序实例的资源组，而不是要对其执行诊断操作的资源。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>了解结果

返回的响应指示是允许还是拒绝流量。 响应如以下示例之一所示：

**允许**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**拒绝**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>后续步骤

如果流量被阻止且不应被阻止，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)了解有关网络安全组的详细信息。












