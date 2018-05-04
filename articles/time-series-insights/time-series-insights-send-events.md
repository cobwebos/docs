---
title: 如何向 Azure 时序见解环境发送事件 | Microsoft Docs
description: 本教程介绍如何创建和配置事件中心，并运行一个示例应用程序来推送要在 Azure 时序见解中显示的事件。
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
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>通过事件中心向时序见解环境发送事件
本文介绍如何创建和配置事件中心并运行示例应用程序来推送事件。 如果你已经有了一个事件中心，其中的事件采用 JSON 格式，则可跳过本教程，在[时序见解](https://insights.timeseries.azure.com)中查看你的环境。

## <a name="configure-an-event-hub"></a>配置事件中心
1. 若要创建事件中心，请遵循事件中心[文档](../event-hubs/event-hubs-create.md)中的说明。

2. 在搜索栏中搜索“事件中心”。 在返回的列表中单击“事件中心”。

3. 单击事件中心的名称将其选中。

4. 在中间的配置窗口中，在“实体”下，再次单击“事件中心”。

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

## <a name="add-time-series-insights-reference-data-set"></a>添加时序见解引用数据集 
使用 TSI 中的参考数据将遥测数据上下文化。  该上下文在数据中添加含义，并使其更易于筛选和聚合。  TSI 在引入时联接参考数据，且无法以追溯方式联接此数据。  因此，在添加包含数据的事件源之前添加参考数据至关重要。  位置或传感器类型等数据是有用的维度，可将其联接到设备/标记/传感器 ID，以方便切片和筛选。  

> [!IMPORTANT]
> 上传历史数据时配置参考数据集非常关键。

将历史数据批量上传到 TSI 时，请务必准备好参考数据。  请记住，如果事件源包含数据，则 TSI 会立即开始从联接的事件源读取数据。  在准备好参考数据之前等待将事件源联接到 TSI 非常有用，尤其是该事件源包含数据时。 或者，可以在准备好参考数据集之前，等待将数据推送到该事件源。

若要管理参考数据，可以使用 TSI 资源管理器中基于 Web 的用户界面，或使用编程 C# API。 TSI 资源管理器提供视觉用户体验用于上传文件，或者以 JSON 或 CSV 格式粘贴现有的参考数据集。 借助 API，可以根据需要生成自定义应用。

有关在时序见解中管理参考数据的详细信息，请参阅[参考数据文章](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

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
#### <a name="output---one-event"></a>输出 - 一个事件

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
#### <a name="output---two-events"></a>输出 - 两个事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>示例 3

#### <a name="input"></a>输入

具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象：
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
#### <a name="output---two-events"></a>输出 - 两个事件
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
#### <a name="output---two-events"></a>输出 - 两个事件

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|压强|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|振动|abs G|217.09|

### <a name="json-shaping-strategies"></a>JSON 塑型策略
让我们使用以下事件示例作为起点，然后探讨其相关问题和缓解这些问题的策略。

#### <a name="payload-1"></a>有效负载 1：
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

如果此事件数组作为有效负载推送到 TSI，此数组将会根据每个测量值存储为一个事件。 这可能会创建过多的事件，因此可能不是理想状态。 请注意，可以使用 TSI 中的参考数据将有意义的名称添加为属性。  例如，可以使用键属性 chId 创建参考数据集：  

chId  Measure               Unit 24    Engine Oil Pressure   PSI 25    CALC Pump Rate        bbl/min

有关在时序见解中管理参考数据的详细信息，请参阅[参考数据文章](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

第一个有效负载的另一个问题是时间戳以毫秒为单位。 TSI 仅接受 ISO 格式的时间戳。 解决方法之一是保留 TSI 中的默认时间戳行为，即使用排队时间戳。

作为上述有效负载的替代项，让我们看看另一个示例。  

#### <a name="payload-2"></a>有效负载 2：
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

与有效负载 1 一样，TSI 会将每个测量值存储为唯一事件。  显著的差别在于，此处 TSI 将会正确读取 ISO 格式的时间戳。  

如果需要减少发送的事件数，可按如下所示发送信息。  

#### <a name="payload-3"></a>有效负载 3：
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
最终的建议如下。

#### <a name="payload-4"></a>有效负载 4：
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

此示例演示平展 JSON 后的输出：

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

我们可以在每个通道的自身 JSON 对象中为该通道定义不同的属性，同时仍将事件计数保持在较低水平。 此平展方法确实会占用更多的空间，必须考虑到这一点。 TSI 容量基于事件数和大小，以先达到的限制为准。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [在时序见解资源管理器中查看环境](https://insights.timeseries.azure.com)。
