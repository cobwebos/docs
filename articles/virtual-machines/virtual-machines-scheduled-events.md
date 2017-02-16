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
translationtype: Human Translation
ms.sourcegitcommit: c7f552825f3230a924da6e5e7285e8fa7fa42842
ms.openlocfilehash: 541709ca17b96f8334e67dbdbbd9a10eefffa06b


---
# <a name="azure-metadata-service---scheduled-events"></a>Azure 元数据服务 - 计划事件

使用 Azure 元数据服务可以发现有关 Azure 中托管的虚拟机的信息。 计划事件（已公开的类别之一）显示有关即将发生的事件（例如，重新启动）的信息，使应用程序可以为其做准备并限制中断。 它可用于所有 Azure 虚拟机类型（包括 PaaS 和 IaaS）。 该服务给虚拟机时间来执行预防性任务并最大限度地降低事件的影响。 例如，你的服务可能会耗尽会话、选择新的主管，或者在观察到实例已计划重新启动后复制数据以避免中断。



## <a name="introduction---why-scheduled-events"></a>简介 - 为什么要有计划事件？

有了计划事件，你可以了解（发现）可能会影响虚拟机的可用性的即将发生事件，并采取主动操作来限制对服务的影响。
使用复制技术保持状态的多实例工作负荷可能易受到跨多个实例频繁发生的服务中断的影响。 此类服务中断可能会导致开销高的任务（例如，重新生成索引）甚至副本丢失。
在很多其他情况下，使用正常关闭顺序可提高整体服务可用性。 例如，完成（或取消）正在提交的事务，将其他任务重新分配到群集中的其他 VM（手动故障转移），从负载均衡器池中删除虚拟机。
有时会通知管理员即将发生的事件，甚至仅仅记录此类事件也会帮助改进托管在云中的应用程序的可维护性。

Azure 元数据服务在以下用例中显示计划事件：
-   平台启动的有影响维护（例如，主机 OS 部署）
-   平台启动的无影响维护（例如，就地 VM 迁移）
-   交互式调用（例如，用户重启或重新部署 VM）



## <a name="scheduled-events---the-basics"></a>计划事件 - 基础知识  

Azure 元数据服务使用 REST 终结点从 VM 中公开有关正在运行的虚拟机的信息。 该信息通过不可路由的 IP 提供，因此不会在 VM 外部公开它。

### <a name="scope"></a>范围 
计划事件将显示到云服务中的所有虚拟机或可用性集中的所有虚拟机上。 因此，应查看事件中的“资源”字段以确定哪些 VM 将会受到影响。

### <a name="discover-the-endpoint"></a>发现终结点
在虚拟网络 (VNet) 中创建虚拟机的情况下，元数据服务可从不可路由 IP：169.254.169.254 获得

在虚拟机用于云服务 (PaaS) 的情况下，可以使用注册表发现元数据服务终结点。

    {HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure\DeploymentManagement}

### <a name="versioning"></a>版本控制 
元数据服务通过以下格式使用版本控制 API：http://{ip}/metadata/{version}/scheduledevents 建议你的服务使用以下网址提供的最新版本：http://{ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>使用标头
查询元数据服务时，必须提供以下标头 *Metadata: true*。 

### <a name="enable-scheduled-events"></a>启用计划事件
第一次调用计划事件时，Azure 会在虚拟机上隐式启用该功能。 因此，第一次调用时应该会延迟响应最多一分钟。 


## <a name="using-the-api"></a>使用 API

### <a name="query-for-events"></a>查询事件
只需进行以下调用即可查询计划事件

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

响应包含计划事件的数组。 空数组表示目前没有计划事件。
在有计划事件的情况下，响应包含事件数组： 

    {
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Pause",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType 捕获对虚拟机的预期影响，其中：
- 暂停：虚拟机将计划暂停几秒。 对内存、打开文件或网络连接没有影响
- 重新启动：虚拟机将计划重新启动（擦除内存）。
- 重新部署：虚拟机将计划移到另一个节点（临时磁盘将丢失）。 

计划事件后（状态=“已计划”），Azure 共享在其后可以启动事件的时间（在 NotBefore 字段中指定）。

### <a name="starting-an-event-expedite"></a>启动事件（加快）

了解即将发生的事件并完成正常关闭逻辑后，可以通过进行 **POST** 调用指示 Azure 更快地移动（如果可能） 
 

## <a name="powershell-sample"></a>PowerShell 示例 

以下示例从元数据服务器读取计划事件，并在应用程序事件日志中记录这些事件，然后再进行确认。

```PowerShell
$localHostIP = "169.254.169.254"
$ScheduledEventURI = "http://"+$localHostIP+"/metadata/latest/scheduledevents"

# Call Azure Metadata Service - Scheduled Events 
$scheduledEventsResponse =  Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $ScheduledEventURI -Method get 

if ($json.Events.Count -eq 0 )
{
    Write-Output "++No scheduled events were found"
}

for ($eventIdx=0; $eventIdx -lt $scheduledEventsResponse.Events.Length ; $eventIdx++)
{
    if ($scheduledEventsResponse.Events[$eventIdx].Resources[0].ToLower().substring(1) -eq $env:COMPUTERNAME.ToLower())
    {    
        # YOUR LOGIC HERE 
         pause "This Virtual Machine is scheduled for to "+ $scheduledEventsResponse.Events[$eventIdx].EventType

        # Acknoledge the event to expedite
        $jsonResp = "{""StartRequests"" : [{ ""EventId"": """+$scheduledEventsResponse.events[$eventIdx].EventId +"""}]}"
        $respbody = convertto-JSon $jsonResp
       
        Invoke-RestMethod -Uri $ScheduledEventURI  -Headers @{"Metadata"="true"} -Method POST -Body $jsonResp 
    }
}


``` 


## <a name="c-sample"></a>C\# 示例 
以下代码是公开用于与元数据服务进行通信的 API 的客户端代码
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
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
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
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
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
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

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
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
[Azure 中虚拟机的计划内维护](./virtual-machines-linux-planned-maintenance.md)



<!--HONumber=Jan17_HO1-->


