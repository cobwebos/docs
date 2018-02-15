---
title: "使用 Azure 网络观察程序排查连接问题 - Azure REST API | Microsoft Docs"
description: "了解如何通过 Azure REST API 使用 Azure 网络观察程序的排查连接问题功能。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: jdial
ms.openlocfilehash: fc0392e8a6bc8662c7b664710b7073ae09c49a7c
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-rest-api"></a>通过 Azure REST API 使用 Azure 网络观察程序排查连接问题

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用排查连接问题来验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有以下资源：

* 要排查连接问题的区域中的网络观察程序实例。
* 用以排查连接问题的虚拟机。

> [!IMPORTANT]
> 排查连接问题需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

使用 Azure 凭据登录到 armclient。

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>检索虚拟机

运行以下脚本返回虚拟机。 运行连接时需要此信息。 

以下代码需要以下变量的值：

- subscriptionId - 要使用的订阅 ID。
- **resourceGroupName** - 包含虚拟机的资源组的名称。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

从以下输出看，在以下示例中使用了虚拟机的 ID：

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

## <a name="check-connectivity-to-a-virtual-machine"></a>检查与虚拟机的连接

此示例通过端口 80 检查与目标虚拟机的连接。

### <a name="example"></a>示例

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$sourceResourceId = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/MultiTierApp0"
$destinationAddress = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/Database0"
$destinationPort = "0"
$requestBody = @"
{
  'source': {
    'resourceId': '${sourceResourceId}',
    'port': 0
  },
  'destination': {
    'resourceId': '${destinationAddress}',
    'port': ${destinationPort}
  }
}
"@

$response = armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/connectivityCheck?api-version=2017-03-01" $requestBody
```

由于此操作会长时间运行，因此会在响应标头中返回结果的 URI，如以下响应中所示：

**重要值**

* **Location** - 此属性包含完成操作时结果所在的 URI

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: f09b55fe-1d3a-4df7-817f-bceb8d2a94c8
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/f09b55fe-1d3a-4df7-817f-bceb8d2a94c8?api-version=2017-03-01
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 367a91aa-7142-436a-867d-d3a36f80bc54
x-ms-routing-request-id: WESTUS2:20170602T202117Z:367a91aa-7142-436a-867d-d3a36f80bc54
Date: Fri, 02 Jun 2017 20:21:16 GMT

null
```

### <a name="response"></a>响应

以下响应来自前面的示例。  在此响应中，`ConnectionStatus` 为“不可访问”。 可以看到所有探测都发送失败。 由于用户配置的名为 **UserRule_Port80** 的 `NetworkSecurityRule` 已配置为阻止端口 80 上的传入流量，虚拟设备上的连接失败。 可以使用此信息来了解连接问题。

```json
{
  "hops": [
    {
      "type": "Source",
      "id": "0cb75c91-7ebf-4df8-8424-15594d6fb51c",
      "address": "10.1.1.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "06dee00a-9c4a-4fb1-b2ea-fa0a539ca684"
      ],
      "issues": []
    },
    {
      "type": "VirtualAppliance",
      "id": "06dee00a-9c4a-4fb1-b2ea-fa0a539ca684",
      "address": "10.1.2.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "75e0cfa5-f9d2-48d8-b705-2c7016f81570"
      ],
      "issues": []
    },
    {
      "type": "VirtualAppliance",
      "id": "75e0cfa5-f9d2-48d8-b705-2c7016f81570",
      "address": "10.1.3.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "86caf6aa-33b0-48a1-b4da-f3c9ce785072"
      ],
      "issues": [
        {
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule",
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ]
        }
      ]
    },
    {
      "type": "VnetLocal",
      "id": "86caf6aa-33b0-48a1-b4da-f3c9ce785072",
      "address": "10.1.4.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurations/ipconfig1",
      "nextHopIds": [],
      "issues": []
    }
  ],
  "connectionStatus": "Unreachable",
  "probesSent": 100,
  "probesFailed": 100
}
```

## <a name="validate-routing-issues"></a>验证路由问题

该示例检查虚拟机与远程终结点之间的连接。

### <a name="example"></a>示例

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$sourceResourceId = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/MultiTierApp0"
$destinationAddress = "13.107.21.200"
$destinationPort = "80"
$requestBody = @"
{
  'source': {
    'resourceId': '${sourceResourceId}',
    'port': 0
  },
  'destination': {
    'address': '${destinationAddress}',
    'port': ${destinationPort}
  }
}
"@

$response = armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/connectivityCheck?api-version=2017-03-01" $requestBody
```

由于此操作会长时间运行，因此会在响应标头中返回结果的 URI，如以下响应中所示：

**重要值**

* **Location** - 此属性包含完成操作时结果所在的 URI

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 15eeeb69-fcef-41db-bc4a-e2adcf2658e0
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/15eeeb69-fcef-41db-bc4a-e2adcf2658e0?api-version=2017-03-01
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4370b798-cd8b-4d3e-ba28-22232bc81dc5
x-ms-routing-request-id: WESTUS:20170602T202606Z:4370b798-cd8b-4d3e-ba28-22232bc81dc5
Date: Fri, 02 Jun 2017 20:26:05 GMT

null
```

### <a name="response"></a>响应

在以下示例中，`connectionStatus` 显示为“不可访问”。 在 `hops` 详细信息中，可以在 `issues` 下看到由于 `UserDefinedRoute` 流量已被阻止。

```json
{
  "hops": [
    {
      "type": "Source",
      "id": "5528055a-b393-4751-97bc-353d8c0aaeff",
      "address": "10.1.1.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "66eefa79-5bfe-48b2-b6ca-eec8247457a3"
      ],
      "issues": [
        {
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute",
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ]
        }
      ]
    },
    {
      "type": "Destination",
      "id": "66eefa79-5bfe-48b2-b6ca-eec8247457a3",
      "address": "13.107.21.200",
      "resourceId": "Unknown",
      "nextHopIds": [],
      "issues": []
    }
  ],
  "connectionStatus": "Unreachable",
  "probesSent": 100,
  "probesFailed": 100
}
```

## <a name="check-website-latency"></a>检查网站延迟

以下示例检查与网站的连接。

### <a name="example"></a>示例

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$sourceResourceId = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/MultiTierApp0"
$destinationAddress = "http://bing.com"
$destinationPort = "0"
$requestBody = @"
{
  'source': {
    'resourceId': '${sourceResourceId}',
    'port': 0
  },
  'destination': {
    'address': '${destinationAddress}',
    'port': ${destinationPort}
  }
}
"@

$response = armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/connectivityCheck?api-version=2017-03-01" $requestBody
```

由于此操作会长时间运行，因此会在响应标头中返回结果的 URI，如以下响应中所示：

**重要值**

* **Location** - 此属性包含完成操作时结果所在的 URI

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: e49b12c7-c232-472c-b6d2-6c257ce80fa5
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/e49b12c7-c232-472c-b6d2-6c257ce80fa5?api-version=2017-03-01
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: c3d9744f-5683-427d-bdd1-636b68ab01b6
x-ms-routing-request-id: WESTUS:20170602T203101Z:c3d9744f-5683-427d-bdd1-636b68ab01b6
Date: Fri, 02 Jun 2017 20:31:00 GMT

null
```

### <a name="response"></a>响应

在以下响应中，可以看到 `connectionStatus` 显示为“可以访问”。 连接成功后，提供了延迟值。

```json
{
  "hops": [
    {
      "type": "Source",
      "id": "6adc0fe1-e384-4220-b1b1-f0d181220072",
      "address": "10.1.1.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "b50b7076-9ff2-4782-b40e-0b89cf758f74"
      ],
      "issues": []
    },
    {
      "type": "Internet",
      "id": "b50b7076-9ff2-4782-b40e-0b89cf758f74",
      "address": "204.79.197.200",
      "resourceId": "Internet",
      "nextHopIds": [],
      "issues": []
    }
  ],
  "connectionStatus": "Reachable",
  "avgLatencyInMs": 1,
  "minLatencyInMs": 0,
  "maxLatencyInMs": 7,
  "probesSent": 100,
  "probesFailed": 0
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>检查与存储终结点的连接

以下示例检查从虚拟机到博客存储帐户的连接。

### <a name="example"></a>示例

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$sourceResourceId = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/MultiTierApp0"
$destinationAddress = "https://build2017nwdiag360.blob.core.windows.net/"
$destinationPort = "0"
$requestBody = @"
{
  'source': {
    'resourceId': '${sourceResourceId}',
    'port': 0
  },
  'destination': {
    'address': '${destinationAddress}',
    'port': ${destinationPort}
  }
}
"@

$response = armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/connectivityCheck?api-version=2017-03-01" $requestBody
```

由于此操作会长时间运行，因此会在响应标头中返回结果的 URI，如以下响应中所示：

**重要值**

* **Location** - 此属性包含完成操作时结果所在的 URI

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: c4ed3806-61ea-4a6b-abc1-9d6f2afc79c2
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/c4ed3806-61ea-4a6b-abc1-9d6f2afc79c2?api-version=2017-03-01
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 93bf5af0-fef5-4b7a-bb9e-9976ba5cdb95
x-ms-routing-request-id: WESTUS2:20170602T200504Z:93bf5af0-fef5-4b7a-bb9e-9976ba5cdb95
Date: Fri, 02 Jun 2017 20:05:03 GMT

null
```

### <a name="response"></a>响应

下面是运行前面 API 调用的响应示例。 由于此检查成功，`connectionStatus` 属性显示为“可以访问”。  提供了有关到达存储 Blob 所需的跃点数和延迟的详细信息。

```json
{
  "hops": [
    {
      "type": "Source",
      "id": "6adc0fe1-e384-4220-b1b1-f0d181220072",
      "address": "10.1.1.4",
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "nextHopIds": [
        "b50b7076-9ff2-4782-b40e-0b89cf758f74"
      ],
      "issues": []
    },
    {
      "type": "Internet",
      "id": "b50b7076-9ff2-4782-b40e-0b89cf758f74",
      "address": "13.71.200.248",
      "resourceId": "Internet",
      "nextHopIds": [],
      "issues": []
    }
  ],
  "connectionStatus": "Reachable",
  "avgLatencyInMs": 1,
  "minLatencyInMs": 0,
  "maxLatencyInMs": 7,
  "probesSent": 100,
  "probesFailed": 0
}
```

## <a name="next-steps"></a>后续步骤

查看[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何利用虚拟机警报自动执行数据包捕获。

访问[查看“IP 流验证”](network-watcher-check-ip-flow-verify-portal.md)，了解是否允许某些流量传入和传出 VM。














