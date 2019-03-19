---
title: 监视 Azure IoT 中心的运行状况 | Microsoft Docs
description: 使用 Azure Monitor 和 Azure 资源运行状况监视 IoT 中心并快速诊断问题
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: kgremban
ms.openlocfilehash: 0a230ff1c4d5c6bb36003f07cc1c411f7e2c3629
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240994"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>监视 Azure IoT 中心的运行状况并快速诊断问题

实施 Azure IoT 中心的企业期望其资源具有可靠的性能。 为了帮助你密切监视对您的操作，IoT 中心与完全集成[Azure Monitor](../azure-monitor/index.yml)并[Azure 资源运行状况](../service-health/resource-health-overview.md)。 这两个服务工作为你提供所需保留 IoT 解决方案并运行处于正常状态的数据。 

Azure Monitor 是监视所有 Azure 服务并记录其日志的单一源。 有关自定义处理，可以将 Azure Monitor 生成的诊断日志发送到 Azure Monitor 日志、 事件中心或 Azure 存储。 借助 Azure Monitor 的指标和诊断设置，可以洞察资源的性能。 请继续阅读本文，了解如何对 IoT 中心[使用 Azure Monitor](#use-azure-monitor)。 

> [!IMPORTANT]
> IoT 中心服务使用 Azure Monitor 诊断日志发出的事件不保证可靠或有序。 某些事件可能会丢失或未按顺序传送。 诊断日志也不是实时的，可能需要几分钟的时间才能将事件记录到所选的目标。

Azure 资源运行状况可以帮助你在 Azure 问题影响资源时进行诊断和获取支持。 仪表板提供每个 IoT 中心的当前和过去的运行状态。 继续阅读到本文底部的部分，了解如何对 IoT 中心[使用 Azure 资源运行状况](#use-azure-resource-health)。 

IoT 中心还提供了其自己的指标，可使用这些指标了解 IoT 资源的状态。 若要了解详细信息，请参阅[了解 IoT 中心度量值](iot-hub-metrics.md)。

## <a name="use-azure-monitor"></a>使用 Azure Monitor

Azure Monitor 提供 Azure 资源的诊断信息，这意味着，你可以监视在 IoT 中心内部发生的操作。 

Azure Monitor 的诊断设置会取代 IoT 中心操作监视功能。 如果当前正在使用操作监视，应迁移工作流。 有关详细信息，请参阅[从操作监视的诊断设置迁移](iot-hub-migrate-to-diagnostics-settings.md)。

若要了解有关的特定指标和 Azure Monitor 监视的事件的详细信息，请参阅[支持的 Azure Monitor 指标](../azure-monitor/platform/metrics-supported.md)并[支持的 Azure 诊断日志服务、 架构和类别](../azure-monitor/platform/diagnostic-logs-schema.md)。

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>了解日志

Azure Monitor 跟踪 IoT 中心内发生的不同操作。 每个类别都有一个定义如何报告该类别中的事件的架构。 

#### <a name="connections"></a>连接

连接类别跟踪设备连接，并断开事件与 IoT 中心和错误的连接。 此类别用于识别未经授权的连接尝试或者在失去与设备的连接时发出警报。

> [!NOTE]
> 设备的可靠连接状态检查[设备检测信号](iot-hub-devguide-identity-registry.md#device-heartbeat)。


```json
{
    "records": 
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>云到设备的命令

云到设备的命令类别跟踪在 IoT 中心发生且与云到设备的消息管道相关的错误。 此类别包括在以下情况下发生的错误：

* 发送云到设备消息时（例如“未经授权的发件人”错误），
* 接收云到设备消息时（例如“超出传递计数”错误），以及
* 接收云到设备消息反馈时（例如“反馈已过期”错误）。 

此类别不捕捉当云到设备消息已成功传递但设备未正确进行处理时出现的错误。

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="device-identity-operations"></a>设备标识操作

设备标识操作类别跟踪你尝试在 IoT 中心的标识注册表中创建、更新或删除条目时所发生的错误。 预配方案就很适合跟踪此类别。

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="routes"></a>路由

消息路由类别跟踪消息路由评估期间发生的错误以及 IoT 中心感知到的终结点运行状况。 此类别包括诸如下列项的事件：

* 规则评估结果为“未定义”，
* IoT 中心将某个终结点标记为死终结点，或者
* 从终结点收到的任何错误。 

此类别不包含有关消息本身的特定错误（例如设备限制错误），这些错误在“设备遥测”类别下报告。

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>设备遥测

设备遥测类别跟踪在 IoT 中心发生的、与遥测管道相关的错误。 此类别包括发送遥测事件（例如限制）和接收遥测事件（例如未经授权的读取者）时发生的错误。 此类别无法捕捉设备本身运行的代码所造成的错误。

```json
{
    "records": 
    [
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
    ]
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
    "records": 
    [
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
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>云到设备孪生操作

云到设备孪生操作类别跟踪设备孪生上服务发起的事件。 这些操作可能获取孪生、更新或替换标记，以及更新或替换所需属性。 

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>设备到云孪生操作

设备到云孪生操作类别跟踪设备孪生上设备发起的事件。 这些操作可能包括获取孪生、更新报告属性和订阅所需属性。

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="twin-queries"></a>孪生查询

孪生查询类别报告在云中针对设备孪生发起的查询请求。 

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="jobs-operations"></a>作业操作

作业操作类别报告在多个设备上更新设备孪生或调用直接方法的作业请求。 这些请求在云中发起。 

```json
{
    "records": 
    [
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
    ]
}
```

#### <a name="direct-methods"></a>直接方法

直接方法类别跟踪发送到单个设备的请求-响应交互。 这些请求在云中发起。 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>分布式跟踪（预览版）

分布式跟踪类别跟踪执行跟踪上下文标头的消息的相关 ID。 若要完全启用这些日志，客户端代码必须更新按照[分析和诊断 IoT 应用程序端到端 IoT 中心分布式跟踪 （预览版） 与](iot-hub-distributed-tracing.md)。

请注意，`correlationId`符合[W3C 跟踪上下文](https://github.com/w3c/trace-context)方案，其中包含`trace-id`以及`span-id`。 

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT 中心 D2C（设备到云）日志

当包含有效跟踪属性的消息到达 IoT 中心时，IoT 中心会记录此日志。 

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}", 
            "location": "Resource location"
        }
    ]
}
```

此时不会计算 `durationMs`，因为 IoT 中心的时钟可能不会与设备时钟同步，所以持续时间计算可能产生误导。 我们建议使用 `properties` 部分中的时间戳编写逻辑，以捕获设备到云延迟的峰值。

| 属性 | Type | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | 以字节为单位的设备到云消息的大小 |
| **deviceId** | ASCII 7 位字母数字字符字符串 | 设备的标识 |
| **callerLocalTimeUtc** | UTC 时间戳 | 设备本地时钟报告的消息创建时间 |
| **calleeLocalTimeUtc** | UTC 时间戳 | IoT 中心服务端时钟报告的消息到达 IoT 中心网关的时间 |

##### <a name="iot-hub-ingress-logs"></a>IoT 中心流入日志

当包含有效跟踪属性的消息写入内部或内置事件中心时，IoT 中心会记录此日志。

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}", 
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 部分中，此日志包含有关消息流入的其他信息

| 属性 | Type | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | True 或 false，指示 IoT 中心是否启用了消息路由 |
| **parentSpanId** | String | 父消息的 [span-id](https://w3c.github.io/trace-context/#parent-id)，在这种情况下为 D2C 消息跟踪 |

##### <a name="iot-hub-egress-logs"></a>IoT 中心流出日志

当[路由](iot-hub-devguide-messages-d2c.md)已启用且消息写入到[终结点](iot-hub-devguide-endpoints.md)时，IoT 中心会记录此日志。 如果未启用路由，IoT 中心不会记录此日志。

```json
{
    "records": 
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}", 
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 部分中，此日志包含有关消息流入的其他信息

| 属性 | Type | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | 路由终结点的名称 |
| **endpointType** | String | 路由终结点的类型 |
| **parentSpanId** | String | 父消息的 [span-id](https://w3c.github.io/trace-context/#parent-id)，在这种情况下为 IoT 中心流入消息跟踪 |


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
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds; 
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

Azure IoT 中心指示区域级别的运行状况。 如果区域性服务中断影响了你的 IoT 中心，则运行状态显示为“未知”。 若要了解详细信息，请参阅[Azure 资源运行状况中的资源类型和运行状况检查](../service-health/resource-health-checks-resource-types.md)。

若要检查 IoT 中心的运行状况，请遵循以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到“服务运行状况” > “资源运行状况”。
1. 从下拉列表框中，选择你的订阅，然后选择“IoT 中心”作为资源类型。

若要了解有关如何解释运行状况数据的详细信息，请参阅[Azure 资源运行状况概述](../service-health/resource-health-overview.md)。

## <a name="next-steps"></a>后续步骤

- [了解 IoT 中心指标](iot-hub-metrics.md)
- [IoT 远程监视和使用 Azure 逻辑应用通过连接 IoT 中心和邮箱的通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

