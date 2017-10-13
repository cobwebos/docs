---
title: "Azure 中 Linux VM 的计划事件 | Microsoft Docs"
description: "Linux 虚拟机上使用 Azure 元数据服务的计划事件。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure 元数据服务：适用于 Linux VM 的计划事件（预览）

> [!NOTE] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

计划事件是 Azure 元数据服务下的一个子服务。 该服务负责显示有关即将发生的事件（例如，重新启动）的信息，使应用程序可做好准备并限制中断。 它可用于所有 Azure 虚拟机类型（包括 PaaS 和 IaaS）。 计划事件给予虚拟机时间来执行预防性任务，以最大限度地降低事件的影响。 

计划事件可同时用于 Windows 和 Linux VM。 有关 Windows 上计划事件的信息，请参阅 [Windows VM 的计划事件](../windows/scheduled-events.md)。

## <a name="why-scheduled-events"></a>为何使用计划事件？

借助计划事件可采取相应步骤，限制由平台启用的维护或用户采取的操作对服务的影响。 

使用复制技术保持状态的多实例工作负荷可能易受到跨多个实例发生的服务中断的影响。 此类服务中断可能会导致开销高的任务（例如，重新生成索引）甚至副本丢失。 

在其他许多情况下，执行正常的关闭顺序可改善整体服务的可用性，例如完成（或取消）正在进行的事务、将任务重新分配到群集中的其他 VM（手动故障转移），或从网络负载均衡器池中删除虚拟机。 

有时会通知管理员即将发生的事件，或记录此类事件也会帮助改进托管在云中的应用程序的可维护性。

Azure 元数据服务在以下用例中显示计划事件：
-   平台启动的维护（例如，主机 OS 部署）
-   用户启动的调用（例如，用户重启或重新部署 VM）


## <a name="the-basics"></a>基础知识  

Azure 元数据服务使用可从 VM 内访问的 REST 终结点公开有关正在运行的虚拟机的信息。 该信息通过不可路由的 IP 提供，因此不会在 VM 外部公开。

### <a name="scope"></a>范围
计划事件会显示到云服务中的所有虚拟机或可用性集中的所有虚拟机上。 因此，应查看事件中的 `Resources` 字段，确定哪些 VM 将会受到影响。 

### <a name="discovering-the-endpoint"></a>发现终结点
在虚拟网络 (VNet) 中创建虚拟机的情况下，元数据服务可从不可路由的静态 IP：`169.254.169.254` 获得。
如果不是在虚拟网络中创建虚拟机（云服务和经典 VM 的默认情况），则需使用额外的逻辑以发现要使用的终结点。 请参阅此示例，了解如何[发现主机终结点](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)。

### <a name="versioning"></a>版本控制 
已对实例元数据服务进行了版本控制。 版本是必需的，当前版本为 `2017-03-01`。

> [!NOTE] 
> 支持的计划事件的早期预览版发布 {最新} 为 api-version。 此格式不再受支持，并且会在未来被弃用。

### <a name="using-headers"></a>使用标头
查询元数据服务时，必须提供标头 `Metadata: true`，以确保不会意外将请求重定向。

### <a name="enabling-scheduled-events"></a>启用计划事件
第一次请求计划事件时，Azure 会在虚拟机上隐式启用该功能。 因此，第一次调用时应该会延迟响应最多两分钟。

### <a name="user-initiated-maintenance"></a>用户启动的维护
用户通过 Azure 门户、API、CLI 或 PowerShell 启动的虚拟机维护会生成计划事件。 这样便可以在应用程序中测试维护准备逻辑，并可以通过应用程序准备用户启动的维护。

重新启动虚拟机会计划 `Reboot` 类型的事件。 重新部署虚拟机会计划 `Redeploy` 类型的事件。

> [!NOTE] 
> 目前，可以同时计划最多 10 个用户启动的维护操作。 在计划事件公开发布之前将放宽此限制。

> [!NOTE] 
> 目前，生成计划事件的用户启动的维护不可配置。 可配置性已计划在将来的版本中推出。

## <a name="using-the-api"></a>使用 API

### <a name="query-for-events"></a>查询事件
只需进行以下调用即可查询计划事件：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

响应包含计划事件的数组。 空数组表示目前没有计划事件。
在有计划事件的情况下，响应包含事件数组： 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>事件属性
|属性  |  说明 |
| - | - |
| EventId | 此事件的全局唯一标识符。 <br><br> 示例： <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 此事件造成的影响。 <br><br> 值： <br><ul><li> `Freeze`：计划将虚拟机暂停几秒。 暂停 CPU，但不会对内存、打开文件或网络连接造成影响。 <li>`Reboot`：计划重启虚拟机（非永久性内存丢失）。 <li>`Redeploy`：计划将虚拟机移到另一节点（临时磁盘丢失）。 |
| ResourceType | 此事件影响的资源的类型。 <br><br> 值： <ul><li>`VirtualMachine`|
| 资源| 此事件影响的资源的列表。 保证包含来自最多一个[更新域](manage-availability.md)的计算机，但可能不包含 UD 中的所有计算机。 <br><br> 示例： <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 事件状态 | 此事件的状态。 <br><br> 值： <ul><li>`Scheduled`：事件计划在 `NotBefore` 属性指定的时间之后启动。<li>`Started`：此事件已启动。</ul> 未提供 `Completed` 或相似状态；事件完成后，将不再返回。
| NotBefore| 一个时间，此事件可能会在该时间之后启动。 <br><br> 示例： <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>事件计划
将根据事件类型为每个事件计划将来的最小量时间。 此时间将反映在事件的 `NotBefore` 属性中。 

|EventType  | 最小值通知 |
| - | - |
| 冻结| 15 分钟 |
| 重新启动 | 15 分钟 |
| 重新部署 | 10 分钟 |

### <a name="starting-an-event"></a>启动事件 

了解即将发生的事件并完成正常关闭逻辑后，可通过使用 `EventId` 对元数据服务进行 `POST` 调用，审核未完成事件。 这表明 Azure 可缩短最短通知时间（如有可能）。 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> 确认事件后，即可允许事件针对事件中所有的 `Resources` 继续进行，而不仅仅是确认该事件的虚拟机。 因此，可选择一个领导来协调确认，这与 `Resources` 字段中的第一个计算机一样简单。




## <a name="python-sample"></a>Python 示例 

以下示例将查询计划事件的元数据服务并审核每个未完成事件。

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
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
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>后续步骤 

- 详细了解[实例元数据服务](instance-metadata-service.md)中可用的 API。
- 了解 [Azure 中 Linux 虚拟机的计划内维护](planned-maintenance.md)。
