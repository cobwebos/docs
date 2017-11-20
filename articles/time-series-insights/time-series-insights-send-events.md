---
title: "如何向 Azure 时序见解环境发送事件 | Microsoft Docs"
description: "本教程介绍如何创建和配置事件中心，并运行一个示例应用程序来推送要在 Azure 时序见解中显示的事件。"
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 543fafac63423ab874c6c8e40d91a1ce0f161987
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>通过事件中心向时序见解环境发送事件
本文介绍如何创建和配置事件中心并运行示例应用程序来推送事件。 如果你已经有了一个事件中心，其中的事件采用 JSON 格式，则可跳过本教程，在[时序见解](https://insights.timeseries.azure.com)中查看你的环境。

## <a name="configure-an-event-hub"></a>配置事件中心
1. 若要创建事件中心，请遵循事件中心[文档](../event-hubs/event-hubs-create.md)中的说明。

2. 在搜索栏中搜索“事件中心”。 在返回的列表中单击“事件中心”。

3. 单击事件中心的名称将其选中。

4. 在中间配置窗口中的“实体”下，再次单击“事件中心”。

5. 选择事件中心的名称对其进行配置。

  ![选择事件中心使用者组](media/send-events/consumer-group.png)

6. 在“实体”下，选择“使用者组”。
 
7. 请确保创建一个使用者组，由时序见解事件源独占使用。

   > [!IMPORTANT]
   > 请确保该使用者组没有被任何其他服务（例如流分析作业或其他时序见解环境）使用。 如果使用者组由其他服务使用，则此环境和其他服务的读取操作会受到负面影响。 如果使用“$Default”作为使用者组，则可能会被其他读取器重复使用。

8. 在“设置”标题下，选择“共享访问策略”。

9. 在事件中心创建 **MySendPolicy**，用于在 csharp 示例中发送事件。

  ![选择共享访问策略，并单击“添加”按钮](media/send-events/shared-access-policy.png)  

  ![添加新的共享访问策略](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>创建时序见解事件源
1. 如果尚未创建事件源，请按[这些说明](time-series-insights-how-to-add-an-event-source-eventhub.md)操作，以便创建事件源。

2. 指定 **deviceTimestamp** 作为时间戳属性名称 - 此属性在 C# 示例中用作实际时间戳。 时间戳属性名称区分大小写，值在作为 JSON 发送到事件中心时必须采用 yyyy-MM-ddTHH:mm:ss.FFFFFFFK 格式。 如果此属性不存在于事件中，则会使用事件中心的排队时间。

  ![创建事件源](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>用于推送事件的示例代码
1. 转到名为 **MySendPolicy** 的事件中心策略。 复制包含策略密钥的**连接字符串**。

  ![复制 MySendPolicy 连接字符串](media/send-events/sample-code-connection-string.png)

2. 运行以下代码，为三个设备中的每一个发送 600 个事件。 使用连接字符串更新 `eventHubConnectionString`。

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>支持的 JSON 形状
### <a name="sample-1"></a>示例 1

#### <a name="input"></a>输入

一个简单的 JSON 对象。

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>输出 - 1 个事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>示例 2

#### <a name="input"></a>输入
包含两个 JSON 对象的 JSON 数组。 每个 JSON 对象都将转换为事件。
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>输出 - 2 个事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>示例 3

#### <a name="input"></a>输入

具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象。
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>输出 - 2 个事件
请注意，“location”属性复制到每个事件。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>示例 4

#### <a name="input"></a>输入

具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象。 此输入表明复杂 JSON 对象可能表示全局属性。

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>输出 - 2 个事件

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|压强|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|振动|abs G|217.09|

## <a name="next-steps"></a>后续步骤
在[时序见解资源管理器](https://insights.timeseries.azure.com)中查看环境。
