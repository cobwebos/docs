---
title: "Azure 元数据服务的计划事件 | Microsoft 文档"
description: "在有影响的事件发生之前，在虚拟机上对其采取应对措施。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 627aa117ded0aaa519052d4ea1a1995ba2e363ee
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Azure 元数据服务 - 计划事件（预览）

> [!NOTE] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [针对 Microsoft Azure 预览版的 Microsoft Azure 补充使用条款。] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)
>

计划事件是 Azure 元数据服务下面的子服务之一，显示有关即将发生的事件（例如，重新启动）的信息，使应用程序可以为其做准备并限制中断。 它可用于所有 Azure 虚拟机类型（包括 PaaS 和 IaaS）。 计划事件给虚拟机时间来执行预防性任务并最大限度地降低事件的影响。 


## <a name="introduction---why-scheduled-events"></a>简介 - 为什么要有计划事件？

使用计划事件，用户客户采取相应步骤来限制对服务的影响。 使用复制技术保持状态的多实例工作负荷可能易受到跨多个实例频繁发生的服务中断的影响。 此类服务中断可能会导致开销高的任务（例如，重新生成索引）甚至副本丢失。 在很多其他情况下，使用正常关闭顺序可提高整体服务可用性。 例如，完成（或取消）正在提交的事务，将其他任务重新分配到群集中的其他 VM（手动故障转移），从负载均衡器池中删除虚拟机。 有时会通知管理员即将发生的事件，甚至仅仅记录此类事件也会帮助改进托管在云中的应用程序的可维护性。
Azure 元数据服务在以下用例中显示计划事件：
-    平台启动的维护（例如，主机 OS 部署）
-    用户启动的调用（例如，用户重启或重新部署 VM）


## <a name="scheduled-events---the-basics"></a>计划事件 - 基础知识  

Azure 元数据服务使用 REST 终结点从 VM 中公开有关正在运行的虚拟机的信息。 该信息通过不可路由的 IP 提供，因此不会在 VM 外部公开它。

### <a name="scope"></a>范围
计划事件将显示到云服务中的所有虚拟机或可用性集中的所有虚拟机上。 因此，应查看事件中的“资源”字段以确定哪些 VM 将会受到影响。 

### <a name="discover-the-endpoint"></a>发现终结点
当虚拟机是在虚拟网络 (VNet) 中创建时，可以从不可路由的 IP 169.254.169.254 使用元数据服务。否则，在适用于云服务和经典 VM 的默认情况下，需要通过其他逻辑来发现要使用的终结点。 请参阅此示例，了解如何[发现主机终结点] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>版本控制 
已对实例元数据服务进行了版本控制。 版本是必需的，当前版本为 2017-03-01

> [!NOTE] 
> 支持的计划事件的早期预览版发布 {最新} 为 api-version。 此格式不再受支持，并且将在未来被弃用。
>


### <a name="using-headers"></a>使用标头
查询元数据服务时，必须提供以下标头 *Metadata: true*。 

### <a name="enable-scheduled-events"></a>启用计划事件
第一次调用计划事件时，Azure 会在虚拟机上隐式启用该功能。 因此，第一次调用时应该会延迟响应最多两分钟。

### <a name="testing-your-logic-with-user-initiated-operations"></a>通过用户启动的操作对逻辑进行测试
若要测试逻辑，可以使用 Azure 门户、API、CLI 或 PowerShell 启动生成计划事件的操作。 重新启动虚拟机会生成事件类型为“重新启动”的计划事件。 重新部署虚拟机会生成事件类型为“重新部署”的计划事件。
用户启动的操作在这两种情况下都需要更长的时间来完成，因为计划事件为应用程序留出更多的时间来正常关闭。 

## <a name="using-the-api"></a>使用 API

### <a name="query-for-events"></a>查询事件
只需进行以下调用即可查询计划事件：

    curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01


响应包含计划事件的数组。 空数组表示目前没有计划事件。
在有计划事件的情况下，响应包含事件数组： 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }
    
### <a name="event-properties"></a>事件属性
|属性  |  说明 |
| - | - |
| EventId |事件的全局唯一标识符。 <br><br> 示例： <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 事件造成的影响。 <br><br> 值： <br><ul><li> <i>冻结</i>：虚拟机将计划暂停几秒。 对内存、打开文件或网络连接没有影响。 <li> <i>重新启动</i>：虚拟机将计划重新启动（擦除内存）。<li> <i>重新部署</i>：虚拟机将计划移到另一个节点（临时磁盘将丢失）。 |
| ResourceType | 事件影响的资源的类型。 <br><br> 值： <ul><li>VirtualMachine|
| 资源| 事件影响的资源的列表。 <br><br> 示例： <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 事件状态 | 事件的状态。 <br><br> 值： <ul><li><i>已计划：</i>事件计划在 <i>NotBefore</i> 属性指定的时间之后启动。<li><i>已启动</i>：事件已启动。</i>
| NotBefore| 事件可能会在之后启动的时间。 <br><br> 示例： <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>事件计划
将根据事件类型为每个事件计划将来的最小量时间。 此时间将反映在事件的 <i>NotBefore</i> 属性。 

|EventType  | 最小值通知 |
| - | - |
| 冻结| 15 分钟 |
| 重新启动 | 15 分钟 |
| 重新部署 | 10 分钟 |

### <a name="starting-an-event-expedite"></a>启动事件（加快）

了解即将发生的事件并完成正常关闭逻辑后，可以通过进行 **POST** 调用指示 Azure 更快地移动（如果可能） 
 

## <a name="powershell-sample"></a>PowerShell 示例 

以下示例将读取计划事件的元数据服务器并审核事件。

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 


# Get the document
$scheduledEvents = GetScheduledEvents $scheduledEventURI


# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents


# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
    ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>C\# 示例 
以下示例是公开用于与元数据服务进行通信的 API 的客户端代码
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
无法使用以下数据结构分析计划事件 

```csharp
    public class ScheduledEventsDocument
    {
        public string DocumentIncarnation;
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime? NotBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public string DocumentIncarnation;
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

使用客户端检索、处理以及确认事件的示例程序：   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
        {
            DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
        };
        
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Python 示例 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>后续步骤 
[Azure 中虚拟机的计划内维护](linux/planned-maintenance.md)
[实例元数据服务](virtual-machines-instancemetadataservice-overview.md)

