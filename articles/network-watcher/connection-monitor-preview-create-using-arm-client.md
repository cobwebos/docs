---
title: 创建连接监视器预览版-ARMClient
titleSuffix: Azure Network Watcher
description: 了解如何使用 ARMClient 创建 (预览) 的连接监视器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567875"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>使用 ARMClient 创建连接监视器 (预览) 

了解如何使用 ARMClient 创建连接监视器 (预览) 来监视资源之间的通信。 它支持混合和 Azure 云部署。

## <a name="before-you-begin"></a>开始之前 

在连接监视器（预览版）中创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器（预览版）包含以下实体：

* **连接监视器资源** – 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点** – 参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置** – 针对测试的特定于协议的配置。 根据选定协议，可以定义端口、阈值、测试频率和其他参数。
* **测试组** – 包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试** – 将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

    ![显示连接监视器的示意图（该图定义了测试组和测试之间的关系）](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>用示例 ARM 模板创建的步骤

使用以下代码通过 ARMClient 创建连接监视器。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

下面是部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>属性说明

* connectionMonitorName-连接监视器资源的名称

* 要在其中创建连接监视器的订阅的子订阅 ID

* NW-将在其中创建 CM 的网络观察程序资源 ID 

* location-将在其中创建连接监视器的区域

* 终结点
    * 名称–每个终结点的唯一名称
    * resourceId –对于 Azure 终结点，资源 ID 是指虚拟机的 Azure 资源管理器 (ARM) 资源 ID。对于非 Azure 终结点，资源 ID 是指链接到非 Azure 代理的 Log Analytics 工作区的 ARM 资源 ID。
    * address-仅当未指定资源 ID 或资源 ID Log Analytics 工作区时适用。 如果与 Log Analytics 资源 ID 一起使用，则是指可用于监视的代理的 FQDN。 如果在没有资源 ID 的情况下使用，则可以是任何公共终结点的 URL 或 IP。
    * 筛选器–对于非 Azure 终结点，请使用筛选器从将用于监视连接监视器资源的 Log Analytics 工作区中选择 "代理"。 如果未设置筛选器，则属于 Log Analytics 工作区的所有代理均可用于监视
        * 类型–将类型设置为 "代理地址"
        * address –将 address 设置为本地代理的 FQDN

* 测试组
    * 名称-命名测试组。
    * testConfigurations-基于哪些源终结点连接到目标终结点的测试配置
    * 源-从上面创建的终结点中进行选择。 基于 azure 的源终结点需要安装 Azure 网络观察程序扩展，并且 nonAzure 的源终结点需要 haveAzure Log Analytics 代理。 若要为源安装代理，请参阅[安装监视代理](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)。
    * 目标-从上面创建的终结点中进行选择。 可以通过将 Azure Vm 或任何终结点指定为目标来监视其连接性)  (公共 IP、URL 或 FQDN。 单个测试组中可以添加 Azure VM、Office 365 URL、Dynamics 365 URL 和自定义终结点。
    * 禁用-使用此字段对测试组指定的所有源和目标禁用监视。

* 测试配置
    * 名称-测试配置的名称。
    * testFrequencySec-指定源对指定协议和端口上的目标进行 ping 操作的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源将根据所选的值来测试与目标的连接。 例如，如果选择 30 秒，则源将在 30 秒的时间段内至少检查一次与目标的连接。
    * 协议-可以选择 TCP、ICMP、HTTP 或 HTTPS。 根据协议，你可以执行一些特定于协议的配置
        * preferHTTPS-指定是否通过 HTTP 使用 HTTPS
        * 端口-指定所选的目标端口。
        * disableTraceRoute-适用于其协议为 TCP 或 ICMP 的测试组。 它阻止源发现拓扑和逐跃点的 RTT。
    * successThreshold-可以在以下网络参数上设置阈值：
        * checksFailedPercent-设置在源使用指定的条件检查到目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
        * roundTripTimeMs-设置源通过测试配置连接到目标所需的持续时间（以毫秒为单位）。

## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试配置数： 20 via ARMClient

## <a name="next-steps"></a>后续步骤

* 了解[如何分析监视数据并设置警报](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* 了解[如何诊断网络中的问题](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
