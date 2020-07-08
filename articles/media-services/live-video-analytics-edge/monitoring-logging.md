---
title: 监视和日志记录-Azure
description: 本文概述了有关 IoT Edge 监视和日志记录的实时视频分析。
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: 807b0623159e0b50285b89da2835e9dd6cb037aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260570"
---
# <a name="monitoring-and-logging"></a>监视和日志记录

在本文中，你将了解如何从用于远程监视 IoT Edge 模块上的实时视频分析接收事件。 

你还将了解如何控制模块生成的日志。

## <a name="taxonomy-of-events"></a>事件的分类

IoT Edge 上的实时视频分析根据以下分类发出事件或遥测数据。

![IoT Edge 遥测架构上的实时视频分析](./media/telemetry-schema/taxonomy.png)

* 操作：作为用户执行的操作的一部分生成的事件，或在[媒体图形](media-graph-concept.md)执行过程中生成的事件。
   
   * 卷：应较低（一分钟内几次，甚至越低）。
   * 示例：

      记录已启动（如下），记录已停止
      
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

   * 卷：可能会很长（一分钟时间）。
   * 示例：
   
      RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)信息（如下所示），或传入视频源中的间隙。

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
* 分析：作为视频分析的一部分生成的事件。

   * 卷：可能很高（一分钟或更长时间）。
   * 示例：
      
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
模块发出的事件将发送到[IoT Edge 集线器](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)，然后可以将其路由到其他目标。 

## <a name="controlling-events"></a>控制事件

可以使用[模块克隆 JSON 架构](module-twin-configuration-schema.md)中所述的以下模块克隆属性，来控制 IoT Edge 模块上的实时视频分析所发布的操作和诊断事件。

`diagnosticsEventsOutputName`–包括并提供此属性的（any）值，以便从该模块获取诊断事件。 省略它，或将其保留为空以停止模块发布诊断事件。
   
`operationalEventsOutputName`–包括并提供此属性的（any）值，以便从模块中获取操作事件。 省略它，或将其保留为空，以使模块停止发布操作事件。
   
分析事件由节点（如运动检测处理器或 HTTP 扩展处理器）生成，IoT 中心接收器用于将其发送到 IoT Edge 集线器。 

你可以通过 $edgeHub 模块（在部署清单中）的所需属性控制[以上所有事件的路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)：

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

在上面的 lvaEdge 中，"" 是 IoT Edge 模块上的实时视频分析的名称，路由规则遵循 "[声明路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)" 中定义的架构。

> [!NOTE]
> 为了确保 analytics 事件到达 IoT Edge 中心，需要有任何运动检测处理器节点和/或任何 HTTP 扩展处理器节点的下游的 IoT 中心接收器节点。

## <a name="event-schema"></a>事件架构

事件源自边缘设备，并可在边缘或云中使用。 IoT Edge 上的实时视频分析所生成的事件符合 Azure IoT 中心建立的[流式消息传递模式](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)，其中包含系统属性、应用程序属性和正文。

### <a name="summary"></a>总结

通过 IoT 中心观察到的每个事件都将具有一组共同的属性，如下所述。

|Property   |属性类型| 数据类型   |说明|
|---|---|---|---|
|message-id |system |guid|  唯一的事件 ID。|
|主题| applicationProperty |字符串|    媒体服务帐户的 Azure 资源管理器路径。|
|subject|   applicationProperty |字符串|    发出事件的实体的子路径。|
|EventTime| applicationProperty|    字符串| 事件的生成时间。|
|eventType| applicationProperty |字符串|    事件类型标识符（见下文）。|
|body|body  |object|    特定事件数据。|
|dataVersion    |applicationProperty|   字符串  |{主}。微不足道|

### <a name="properties"></a>属性

#### <a name="message-id"></a>message-id

事件全局唯一标识符（GUID）

#### <a name="topic"></a>主题

表示与关系图关联的 Azure 媒体服务帐户。

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

发出事件的实体：

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Subject 属性允许将一般事件映射到其生成模块。 例如，如果 RTSP 用户名或密码无效，则生成的事件将出现 `Microsoft.Media.Graph.Diagnostics.ProtocolError` 在节点上 `/graphInstances/myGraph/sources/myRtspSource` 。

#### <a name="event-types"></a>事件类型

事件类型根据以下架构分配给命名空间：

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>事件类

|类名|描述|
|---|---|
|Analytics  |作为内容分析的一部分生成的事件。|
|诊断    |有助于诊断问题和性能的事件。|
|可运行    |作为资源操作的一部分生成的事件。|

事件类型特定于每个事件类。

示例：

* Microsoft. node.js .。。
* "Authorizationerror)"。
* GraphInstanceStarted 的操作。

### <a name="event-time"></a>事件时间

事件时间在 ISO8601 字符串和事件发生的时间中进行了描述。

## <a name="logging"></a>Logging

与其他 IoT Edge 模块一样，你还可以在边缘设备上[检查容器日志](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues)。 写入日志的信息可由[以下模块](module-twin-configuration-schema.md)克隆属性控制：

* logLevel

   * 允许的值包括详细、信息、警告、错误、无。
   * 默认值为 "信息" –日志将包含错误、警告和信息。 讯息.
   * 如果将值设置为 "警告"，则日志将包含错误消息和警告消息
   * 如果将值设置为 "错误"，则日志中将仅包含错误消息。
   * 如果将值设置为 "无"，则不会生成任何日志（不建议这样做）。
   * 如果需要与 Azure 支持人员共享日志来诊断问题，则只能使用 "详细"。
* logCategories

   * 以下一项或多项的逗号分隔列表： Application、Events、MediaPipeline。
   * 默认值： "应用程序"、"事件"。
   * 应用程序–这是模块中的高级信息，如模块启动消息、环境错误和直接方法调用。
   * 事件-这是本文前面介绍的所有事件。
   * MediaPipeline –这些是一些低级别的日志，可在解决问题时提供见解，如使用支持 RTSP 的相机建立连接时遇到的问题。
   
### <a name="generating-debug-logs"></a>生成调试日志

在某些情况下，可能需要生成比上述步骤更详细的日志，以帮助 Azure 支持人员解决问题。 完成此操作需要执行两个步骤。

首先，通过 createOptions 将[模块存储链接到设备存储](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module#link-module-storage-to-device-storage)。 如果你从快速入门中检查[部署清单模板](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json)，你将看到：

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

以上允许边缘模块将日志写入（设备）存储路径 "/var/local/mediaservices/"。 如果将以下所需属性添加到模块：

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

然后，该模块将以二进制格式将调试日志写入（设备）存储路径/var/local/mediaservices/debuglogs/，可与 Azure 支持人员共享。

## <a name="faq"></a>常见问题解答

[常见问题解答](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-tutorial.md)
