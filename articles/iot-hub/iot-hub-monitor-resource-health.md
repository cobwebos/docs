---
title: 监视 Azure IoT 中心的运行状况 | Microsoft Docs
description: 使用 Azure Monitor 和 Azure 资源运行状况监视 IoT 中心并快速诊断问题
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: bf6202b002aaf6d89a30c7c653fdcee00cb50290
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202214"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>监视 Azure IoT 中心的运行状况并快速诊断问题

实施 Azure IoT 中心的企业期望其资源具有可靠的性能。 为了帮助你密切监视自己的操作，IoT 中心与 [Azure Monitor][lnk-AM] 和 [Azure 资源运行状况][lnk-ARH]完全集成。 这两个服务相辅相成，提供所需的数据来让 IoT 解决方案保持正常的运行。 

Azure Monitor 是监视所有 Azure 服务并记录其日志的单一源。 可将 Azure Monitor 生成的日志发送到 Log Analytics、事件中心或 Azure 存储进行自定义处理。 借助 Azure Monitor 的指标和诊断设置，可以实时洞察资源的性能。 请继续阅读本文，了解如何对 IoT 中心[使用 Azure Monitor](#use-azure-monitor)。 

Azure 资源运行状况有助于在 Azure 问题影响资源时进行诊断和获取支持。 个性化的仪表板提供 IoT 中心的当前和过去运行状态。 请继续阅读本文，了解如何对 IoT 中心[使用 Azure 资源运行状况](#use-azure-resource-health)。 

除了与这两个服务集成以外，IoT 中心还提供自身的指标，可使用这些指标了解 IoT 资源的状态。 有关详细信息，请参阅[了解 IoT 中心指标][lnk-metrics]。

## <a name="use-azure-monitor"></a>使用 Azure Monitor

Azure Monitor 提供资源级诊断信息，这意味着，可以监视在 IoT 中心内部发生的操作。 

Azure Monitor 的诊断设置会取代 IoT 中心操作监视功能。 如果当前正在使用操作监视，应迁移工作流。 有关详细信息，请参阅[从操作监视迁移到诊断设置][lnk-migrate]。

若要详细了解 Azure Monitor 监视的具体指标和事件，请参阅 [Azure Monitor 支持的指标][lnk-AM-metrics]和 [Azure 诊断日志支持的服务、架构和类别][lnk-AM-schemas]。

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>了解日志

Azure Monitor 跟踪 IoT 中心内发生的不同操作。 每个类别都有一个定义如何报告该类别中的事件的架构。 

#### <a name="connections"></a>连接

连接类别跟踪设备与 IoT 中心连接或断开连接时发生的错误。 若要识别未经授权的连接尝试，以及在连接质量不佳的区域中的设备断开连接时进行跟踪，就很适合跟踪此类别。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>云到设备的命令

云到设备的命令类别跟踪在 IoT 中心发生且与云到设备的消息管道相关的错误。 此类别包括下述情况下发生的错误：发送云到设备的消息（例如未经授权的发送者）、接收云到设备的消息（例如超过传递计数），以及接收云到设备的消息反馈（例如反馈已过期）。 此类别不捕捉未正确处理云到设备的消息但却将其成功传递的设备所发生的错误。

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>设备标识操作

设备标识操作类别跟踪你尝试在 IoT 中心的标识注册表中创建、更新或删除条目时所发生的错误。 预配方案就很适合跟踪此类别。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>路由

消息路由类别跟踪消息路由评估期间发生的错误以及 IoT 中心感知到的终结点运行状况。 此类别包括以下事件：规则评估为“未定义”、IoT 中心将终结点标记为“已停用”，以及从终结点中收到的任何其他错误。 此类别不包含有关消息本身的特定错误（如设备限制错误），这些错误在“设备遥测”类别下报告。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>设备遥测

设备遥测类别跟踪在 IoT 中心发生的、与遥测管道相关的错误。 此类别包括发送遥测事件（例如限制）和接收遥测事件（例如未经授权的读取者）时发生的错误。 此类别无法捕捉设备本身运行的代码所造成的错误。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>文件上传操作

文件上传类别跟踪在 IoT 中心发生的、与文件上传功能相关的错误。 此类别包括：

* SAS URI 发生的错误，例如，它在设备向中心通知某个完成的上传前失效。
* 由设备报告的失败上传。
* 创建 IoT 中心通知消息期间在存储中找不到文件时发生的错误。

此类别不能捕获在设备将文件上传到存储时直接发生的错误。

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>云到设备孪生操作

云到设备孪生操作类别跟踪设备孪生上服务发起的事件。 这些操作可能包括获取孪生、更新报告属性和订阅所需属性

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>设备到云孪生操作

设备到云孪生操作类别跟踪设备孪生上设备发起的事件。 这些操作可能获取孪生、更新或替换标记，以及更新或替换所需属性。 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>孪生查询

孪生查询类别报告在云中针对设备孪生发起的查询请求。 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>作业操作

作业操作类别报告在多个设备上更新设备孪生或调用直接方法的作业请求。 这些请求在云中发起。 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>直接方法

直接方法类别跟踪发送到单个设备的请求-响应交互。 这些请求在云中发起。 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>从 Azure 事件中心读取日志

通过诊断设置设置事件日志记录后，可以创建应用程序用于读出日志，以便可以根据日志中的信息采取措施。 以下示例代码从事件中心检索日志：

```csharp
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>使用 Azure 资源运行状况

使用 Azure 资源运行状况可以监视 IoT 中心是否已启动并正在运行。 此外，还可以了解是否发生了影响 IoT 中心运行状况的区域性服务中断。 若要了解有关 Azure IoT 中心运行状态的具体详细信息，我们建议[使用 Azure Monitor](#use-azure-monitor)。 

Azure IoT 中心指示区域级别的运行状况。 如果发生了影响 IoT 中心的区域性服务中断，运行状态显示为“未知”。 若要详细了解 Azure 资源运行状况执行的具体运行状况检查，请参阅[Azure 资源运行状况中的资源类型和运行状况检查][lnk-ARH-checks]。

若要检查 IoT 中心的运行状况，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到“服务运行状况” > “资源运行状况”。
1. 从下拉列表中选择自己的订阅和“IoT 中心”。

若要详细了解如何解释运行状况数据，请参阅 [Azure 资源运行状况概述][lnk-ARH]

## <a name="next-steps"></a>后续步骤

- [了解 IoT 中心指标][lnk-metrics]
- [通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
