---
title: Azure 中适用于 Linux VM 的 Scheduled Events
description: 使用 Azure 元数据服务为 Linux 虚拟机计划事件。
author: EricRadzikowskiMSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: fee57efb3517131049f986c743125f17573fdc34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816722"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure 元数据服务：适用于 Linux VM 的计划事件

计划事件是一个 Azure 元数据服务，可提供应用程序时间用于准备虚拟机 (VM) 维护。 它提供有关即将发生的维护事件的信息（例如重新启动），使应用程序可以为其准备并限制中断。 它可用于 Windows 和 Linux 上的所有 Azure 虚拟机类型（包括 PaaS 和 IaaS）。 

有关 Windows 上的计划事件的信息，请参阅[适用于 Windows VM 的计划事件](../windows/scheduled-events.md)。

> [!Note] 
> 计划事件在所有 Azure 区域中正式发布。 有关最新版本信息，请参阅[版本和区域可用性](#version-and-region-availability)。

## <a name="why-use-scheduled-events"></a>为何使用计划事件？

许多应用程序都可以受益于时间来准备 VM 维护。 时间可以用于执行应用程序的特定任务的提高可用性、可靠性和可维护性，包括： 

- 检查点和还原。
- 连接清空。
- 主要副本故障转移。
- 从负载均衡器池删除。
- 事件日志记录。
- 正常关闭。

使用计划事件，应用程序可以发现维护的发生，并触发任务以限制其影响。  

预定事件提供以下用例中的事件：

- [平台启动的维护](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json)（例如，VM 重新启动、实时迁移或主机的内存保留更新）
- 虚拟机正在根据预测很快会出现故障的[降级主机硬件](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events)上运行
- 用户启动的维护（例如，用户重启或重新部署 VM）
- [现成 VM](../spot-vms.md) 和[现成规模集](../../virtual-machine-scale-sets/use-spot.md)实例逐出。

## <a name="the-basics"></a>基础知识  

  元数据服务公开在 VM 中使用可访问的 REST 终结点运行 VM 的相关信息。 该信息通过不可路由的 IP 提供，因此不会在 VM 外部公开。

### <a name="scope"></a>范围
计划的事件传送到：

- 独立虚拟机。
- 云服务中的所有 VM。
- 可用性集中的所有 VM。
- 可用性区域中的所有 Vm。 
- 规模集位置组中的所有 VM。 

> [!NOTE]
> 特定于可用性区域中的 Vm，计划事件会在区域中的单个 Vm 上执行。
> 例如，如果某个可用性集中有100个 Vm，并且其中一个虚拟机有一个更新，则计划的事件将转到全部100，而如果一个区域中有100个单个 Vm，则事件只会转到受影响的 VM。

因此，检查事件中的 `Resources` 字段可确定哪些 VM 受到了影响。

### <a name="endpoint-discovery"></a>终结点发现
对于启用了 VNET 的 VM，元数据服务可通过不可路由的静态 IP (`169.254.169.254`) 使用。 最新版本的计划事件的完整终结点是： 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

如果不是在虚拟网络中创建 VM（云服务和经典 VM 的默认情况），则需使用额外的逻辑以发现要使用的 IP 地址。 若要了解如何[发现主机终结点](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)，请参阅此示例。

### <a name="version-and-region-availability"></a>版本和区域可用性
计划事件服务受版本控制。 版本是必需的，当前版本为 `2019-01-01`。

| 版本 | 发布类型 | 区域 | 发行说明 | 
| - | - | - | - | 
| 2019-08-01 | 正式版 | 全部 | <li> 添加了对 EventSource 的支持 |
| 2019-04-01 | 正式版 | 全部 | <li> 添加了对事件说明的支持 |
| 2019-01-01 | 正式版 | All | <li> 已添加对虚拟机规模集 EventType“Terminate”的支持 |
| 2017-11-01 | 正式版 | All | <li> 已添加对现成 VM 逐出 EventType“Preempt”的支持<br> | 
| 2017-08-01 | 正式版 | All | <li> 已从 IaaS VM 的资源名称中删除前置下划线<br><li>针对所有请求强制执行元数据标头要求 | 
| 2017-03-01 | 预览 | All | <li>初始版本 |


> [!NOTE] 
> 支持的计划事件的前一预览版 {latest} 发布为 api-version。 此格式不再受支持，并且将在未来弃用。

### <a name="enabling-and-disabling-scheduled-events"></a>启用和禁用计划事件
首次为事件发出请求时，为服务启用了计划事件。 首次调用时应该会延迟响应最多两分钟。

如果 24 小时未发出请求，将为服务禁用计划事件。

### <a name="user-initiated-maintenance"></a>用户启动的维护
用户通过 Azure 门户、API、CLI 或 PowerShell 启动的 VM 维护会生成计划事件。 然后，可以在应用程序中测试维护准备逻辑，并可以通过应用程序准备用户启动的维护。

如果重启 VM，将计划 `Reboot` 类型的事件。 如果重新部署 VM，将计划 `Redeploy` 类型的事件。

## <a name="use-the-api"></a>使用 API

### <a name="headers"></a>头文件
查询元数据服务时，必须提供标头 `Metadata:true` 以确保不会在无意中重定向该请求。 `Metadata:true` 标头对于所有预定事件请求是必需的。 不在请求中包含标头会导致元数据服务发出的“错误的请求”响应。

### <a name="query-for-events"></a>查询事件
只需进行以下调用即可查询计划事件：

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

响应包含计划事件的数组。 数组为空意味着目前没有计划事件。
如果有计划事件，响应会包含事件的数组。 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>事件属性
|属性  |  说明 |
| - | - |
| EventId | 此事件的全局唯一标识符。 <br><br> 示例： <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 此事件造成的影响。 <br><br> 值： <br><ul><li> `Freeze`：虚拟机计划暂停数秒。 CPU 和网络连接可能会暂停，但对内存或打开的文件没有影响。<li>`Reboot`：计划重启虚拟机（非永久性内存丢失）。 <li>`Redeploy`：计划将虚拟机移到另一节点（临时磁盘将丢失）。 <li>`Preempt`：正在删除现成虚拟机（临时磁盘将丢失）。 <li> `Terminate`：计划将删除虚拟机。 |
| ResourceType | 此事件影响的资源类型。 <br><br> 值： <ul><li>`VirtualMachine`|
| 资源| 此事件影响的资源列表。 它保证最多只能包含一个[更新域](manage-availability.md)的计算机，但可能不包含该更新域中的所有计算机。 <br><br> 示例： <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | 此事件的状态。 <br><br> 值： <ul><li>`Scheduled`：此事件计划在 `NotBefore` 属性指定的时间之后启动。<li>`Started`：此事件已启动。</ul> 不提供 `Completed` 或类似状态。 事件完成后，将不再返回该事件。
| NotBefore| 在可以启动此事件之前所要经过的时间。 <br><br> 示例： <br><ul><li> 2016 年 9 月 19 日星期一 18:29:47 GMT  |
| 说明 | 此事件的说明。 <br><br> 示例： <br><ul><li> 主机服务器正在维护中。 |
| EventSource | 事件的发起者。 <br><br> 示例： <br><ul><li> `Platform`：此事件是由平台发起的。 <li>`User`：此事件是由用户发起的。 |

### <a name="event-scheduling"></a>事件计划
将根据事件类型为每个事件计划将来的最小量时间。 此时间反映在某个事件的 `NotBefore` 属性上。 

|EventType  | 最小通知 |
| - | - |
| 冻结| 15 分钟 |
| 重新启动 | 15 分钟 |
| 重新部署 | 10 分钟 |
| Preempt | 30 秒 |
| Terminate | [用户可配置](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)：5 - 15 分钟 |

> [!NOTE] 
> 在某些情况下，由于硬件降级，Azure 能够预测主机故障，并会尝试通过对迁移进行计划来缓解服务中断。 受影响的虚拟机会收到计划事件，该事件的 `NotBefore` 通常是将来几天的时间。 实际时间因预测的故障风险评估而异。 Azure 会尽可能提前 7 天发出通知，但实际时间可能会有变化，如果预测硬件即将发生故障的可能性很大，则实际时间可能更早。 为了在系统启动迁移之前硬件出现故障时将服务风险降至最低，我们建议你尽快自行重新部署虚拟机。

### <a name="start-an-event"></a>启动事件 

了解即将发生的事件并完成正常关闭逻辑后，可以通过使用 `EventId` 对元数据服务进行 `POST` 调用来批准未完成的事件。 此调用指示 Azure 可以缩短最小通知时间（如可能）。 

下面是 `POST` 请求正文中所需的 JSON 示例。 请求应包含 `StartRequests` 列表。 每个 `StartRequest` 包含想要加速的事件的 `EventId`：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash 示例
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> 确认事件后，即可允许事件针对事件中所有的 `Resources` 继续进行，而不仅仅是确认该事件的 VM。 因此，可以选择一个指挥计算机来协调该确认，为简单起见，可选择 `Resources` 字段中的第一个计算机。

## <a name="python-sample"></a>Python 示例 

下例将查询计划事件的元数据服务器并审核所有未完成的事件。

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>后续步骤 
- 观看 [Azure Friday 上的计划事件](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)以查看演示。 
- 在 [Azure 实例元数据计划事件 GitHub 存储库](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)中查看计划事件代码示例。
- 详细了解[实例元数据服务](instance-metadata-service.md)中提供的 API。
- [Azure 中 Linux 虚拟机的计划内维护](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json)。
