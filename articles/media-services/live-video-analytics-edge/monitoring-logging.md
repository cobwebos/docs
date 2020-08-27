---
title: 监视和日志记录 - Azure
description: 本文概述了 IoT Edge 上实时视频分析的监视和日志记录。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e1f31c6bb3ea344286ad9af89417ca9f8fd59527
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934287"
---
# <a name="monitoring-and-logging"></a>监视和日志记录

在本文中，你将了解如何从 IoT Edge 模块上的实时视频分析接收事件，以进行远程监视。 

你还将了解如何控制模块生成的日志。

## <a name="taxonomy-of-events"></a>事件的分类

IoT Edge 上的实时视频分析根据以下分类发出事件或遥测数据。

![IoT Edge 上的实时视频分析遥测架构](./media/telemetry-schema/taxonomy.png)

* 可操作：事件是用户执行的操作的一部分，或者是在执行[媒体图](media-graph-concept.md)期间生成的。
   
   * 数量：应较低（每分钟几次，甚至更低的速率）。
   * 示例:

      开始记录（如下），停止记录
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 诊断：有助于诊断问题和/或性能问题的事件。

   * 数量：可能很高（每分钟几次）。
   * 示例:
   
      RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 信息（如下），或传入视频源中的间隔。

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 分析：视频分析过程中生成的事件。

   * 数量：可能很高（每分钟几次或频率更高）。
   * 示例:
      
      检测到的动作（如下所示），推理结果。
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
模块发出的事件将发送到 [IoT Edge 中心](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)，然后可以从那里将其路由到其他目标。 

### <a name="timestamps-in-analytic-events"></a>分析事件中的时间戳
如上所示，作为视频分析的一部分生成的事件具有与之关联的时间戳。 如果您将 [实时视频记录](video-recording-concept.md) 为图形拓扑的一部分，则此时间戳可帮助您找到记录的视频中发生特定事件的位置。 下面是有关如何将分析事件中的时间戳映射到记录到 [Azure 媒体服务资产](terminology.md#asset)的视频时间线的指导原则。

首先，提取 `eventTime` 值。 在 [时间范围筛选器](playback-recordings-how-to.md#time-range-filters) 中使用此值可检索记录的合适部分。 例如，你可能想要提取在之前30秒开始的视频 `eventTime` 并在30秒后结束。 在上面的示例中，其中， `eventTime` 为 2020-05-12T23：33： 09.381 z，为 +/30 秒窗口的 HLS 清单请求将如下所示：
```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```
上述 URL 将返回所谓的 [主播放列表](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)，其中包含用于媒体播放列表的 url。 媒体播放列表包含如下所示的条目：

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
在上面的条目中，会报告可从时间戳值开始的视频片段 `143039375031270` 。 `timestamp`分析事件中的值使用与媒体播放列表相同的时间刻度，可用于标识相关视频片段，并查找正确的帧。

有关详细信息，可以阅读有关 HLS 中的帧准确查找的众多 [文章](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 之一。

## <a name="controlling-events"></a>控制事件

你可以使用[模块孪生 JSON 架构](module-twin-configuration-schema.md)中所述的以下模块孪生属性，控制由 IoT Edge 模块上的实时视频分析发布的操作和诊断事件。

`diagnosticsEventsOutputName` - 包括并提供此属性的（任何）值，以便从模块中获取诊断事件。 忽略它或将它留空可阻止模块发布诊断事件。
   
`operationalEventsOutputName` - 包括并提供此属性的（任何）值，以便从模块中获取操作事件。 忽略它或将它留空可阻止模块发布操作事件。
   
分析事件由动作检测处理器或 HTTP 扩展处理器等节点生成，并且 IoT 中心接收器用于将它们发送到 IoT Edge 中心。 

可以通过 $edgeHub 模块孪生的所需属性（位于部署清单中）控制[以上所有事件的路由](../../iot-edge/module-composition.md#declare-routes)：

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

在上文中，lvaEdge 是 IoT Edge 模块上实时视频分析的名称，并且路由规则遵循[声明路由](../../iot-edge/module-composition.md#declare-routes)中定义的架构。

> [!NOTE]
> 为了确保分析事件到达 IoT Edge 中心，在任何动作检测处理器节点和/或任何 HTTP 扩展处理器节点的下游都需要有一个 IoT 中心接收器节点。

## <a name="event-schema"></a>事件架构

事件来自 Edge 设备，你可以在 Edge 或云中使用它。 由 IoT Edge 上的实时视频分析生成的事件遵循由 Azure IoT 中心建立的[流式处理消息传递模式](../../iot-hub/iot-hub-devguide-messages-construct.md)，并且具有系统属性、应用程序属性和正文。

### <a name="summary"></a>摘要

通过 IoT 中心观察到的每个事件都将具有如下所述的一组共同属性。

|属性   |属性类型| 数据类型   |说明|
|---|---|---|---|
|message-id |system |GUID|  唯一的事件 ID。|
|主题| applicationProperty |string|    媒体服务帐户的 Azure 资源管理器路径。|
|subject|   applicationProperty |string|    发出事件的实体的子路径。|
|EventTime| applicationProperty|    string| 生成事件的时间。|
|eventType| applicationProperty |string|    事件类型标识符（见下文）。|
|body|body  |object|    特定事件数据。|
|dataVersion    |applicationProperty|   string  |{Major}.{Minor}|

### <a name="properties"></a>属性

#### <a name="message-id"></a>message-id

事件全局唯一标识符 (GUID)

#### <a name="topic"></a>主题

表示与关系图关联的 Azure 媒体服务帐户。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

发出事件的实体：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

subject 属性允许将一般事件映射到其生成模块。 例如，如果 RTSP 用户名或密码无效，则生成的事件将是 `/graphInstances/myGraph/sources/myRtspSource` 节点上的 `Microsoft.Media.Graph.Diagnostics.ProtocolError`。

#### <a name="event-types"></a>事件类型

事件类型根据以下架构分配给命名空间：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件类

|类名|说明|
|---|---|
|分析  |在内容分析过程中生成的事件。|
|诊断    |有助于诊断问题和性能的事件。|
|可运行    |在资源操作过程中生成的事件。|

事件类型特定于每个事件类。

示例:

* Microsoft.Media.Graph.Analytics.Inference
* Microsoft.Media.Graph.Diagnostics.AuthorizationError
* Microsoft.Media.Graph.Operational.GraphInstanceStarted

### <a name="event-time"></a>事件时间

ISO8601 字符串中介绍了事件时间，它是事件发生的时间。

## <a name="logging"></a>日志记录

与其他 IoT Edge 模块一样，你也可以[检查 Edge 设备上的容器日志](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)。 可以通过[以下模块孪生](module-twin-configuration-schema.md)属性来控制写入日志的信息：

* logLevel

   * 允许的值为“详细”、“信息”、“警告”、“错误”、“无”。
   * 默认值为“信息”- 日志将包含错误、警告和信息。 消息。
   * 如果将值设置为“警告”，则日志将包含错误和警告消息
   * 如果将值设置为“错误”，则日志将仅包含错误消息。
   * 如果将值设置为“无”，则不会生成任何日志（不建议这样做）。
   * 仅在需要与 Azure 支持共享日志以诊断问题时，才应使用“详细”。
* logCategories

   * 以逗号分隔的以下一项或多项的列表：应用程序、事件和 MediaPipeline。
   * 默认值：应用程序和事件。
   * 应用程序 - 这是来自模块的概要信息，例如模块启动消息、环境错误和直接方法调用。
   * 事件 - 这些是本文前面介绍的所有事件。
   * MediaPipeline - 这些是一些低级别的日志，可在对问题（例如，与支持 RTSP 的相机建立连接时遇到困难）进行故障排除时提供见解。
   
### <a name="generating-debug-logs"></a>生成调试日志

在某些情况下，可能需要生成比上述日志更详细的日志才能帮助 Azure 支持解决问题。 可以通过两个步骤完成此操作。

首先，通过 createOptions [将模块存储链接到设备存储](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage)。 如果你从快速入门检查[部署清单模板](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)，则会看到以下内容：

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

上述内容允许 Edge 模块将日志写入（设备）存储路径“/var/local/mediaservices/”。 如果将以下所需属性添加到模块中：

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

然后，该模块将以二进制格式将调试日志写入（设备）存储路径 /var/local/mediaservices/debuglogs/，你可以与 Azure 支持共享该路径。

## <a name="faq"></a>常见问题

[常见问题](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-tutorial.md)
