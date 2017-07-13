---
title: "向 Azure 时序见解环境发送事件 | Microsoft Docs"
description: "本教程介绍如何将事件推送到时序见解环境"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---
# 通过事件中心向时序见解环境发送事件
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

本教程介绍如何创建和配置事件中心并运行示例应用程序来推送事件。 如果你已经有了一个事件中心，其中的事件采用 JSON 格式，则可跳过本教程，在[时序见解](https://insights.timeseries.azure.com)中查看你的环境。

## 配置事件中心
<a id="configure-an-event-hub" class="xliff"></a>
1. 若要创建事件中心，请遵循事件中心[文档](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)中的说明。

2. 请确保创建一个使用者组，由时序见解事件源独占使用。

  > [!IMPORTANT]
  > 请确保该使用者组没有被任何其他服务（例如流分析作业或其他时序见解环境）使用。 如果使用者组由其他服务使用，则此环境和其他服务的读取操作会受到负面影响。 如果使用“$Default”作为使用者组，则可能会被其他读取器重复使用。

  ![选择事件中心使用者组](media/send-events/consumer-group.png)

3. 在事件中心创建“MySendPolicy”，用于在 csharp 示例中发送事件。

  ![选择共享访问策略，然后单击“添加”按钮](media/send-events/shared-access-policy.png)  

  ![添加新的共享访问策略](media/send-events/shared-access-policy-2.png)  

## 创建时序见解事件源
<a id="create-time-series-insights-event-source" class="xliff"></a>
1. 如果尚未创建事件源，请按[此处](time-series-insights-add-event-source.md)指定的说明操作，以便创建事件源。

2. 指定“deviceTimestamp”作为时间戳属性名称 - 此属性在 csharp 示例中用作实际时间戳。 时间戳属性名称区分大小写，值在作为 JSON 发送到事件中心时应采用 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ 格式。 如果此属性不存在于事件中，则会使用事件中心的排队时间。

  ![创建事件源](media/send-events/event-source-1.png)

## 用于推送事件的示例代码
<a id="sample-code-to-push-events" class="xliff"></a>
1. 转到事件中心策略“MySendPolicy”，复制带策略密钥的连接字符串。

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
## 支持的 JSON 形状
<a id="supported-json-shapes" class="xliff"></a>
### 示例 1
<a id="sample-1" class="xliff"></a>

#### 输入
<a id="input" class="xliff"></a>

一个简单的 JSON 对象。

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### 输出 - 1 个事件
<a id="output---1-event" class="xliff"></a>

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### 示例 2
<a id="sample-2" class="xliff"></a>

#### 输入
<a id="input" class="xliff"></a>
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
#### 输出 - 2 个事件
<a id="output---2-events" class="xliff"></a>

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
### 示例 3
<a id="sample-3" class="xliff"></a>

#### 输入
<a id="input" class="xliff"></a>

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
#### 输出 - 2 个事件
<a id="output---2-events" class="xliff"></a>
请注意，“location”属性复制到每个事件。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### 示例 4
<a id="sample-4" class="xliff"></a>

#### 输入
<a id="input" class="xliff"></a>

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
#### 输出 - 2 个事件
<a id="output---2-events" class="xliff"></a>

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|压强|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|振动|abs G|217.09|

## 后续步骤
<a id="next-steps" class="xliff"></a>

* 在[时序见解门户](https://insights.timeseries.azure.com)中查看环境

