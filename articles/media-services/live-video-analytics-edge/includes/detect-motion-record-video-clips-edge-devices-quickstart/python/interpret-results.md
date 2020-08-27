---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682086"
---
运行媒体图时，来自运动检测器处理器节点的结果将通过 IoT 中心接收器节点发送到 IoT 中心。 Visual Studio Code 的“输出”窗口中显示的消息包含 `body` 部分和 `applicationProperties` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../../../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

对媒体图进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

在以上脚本中： 

* 消息为诊断事件 `MediaSessionEstablished`。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
* `applicationProperties` 中的 `subject` 引用生成消息的图形拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* `applicationProperties` 中的 `eventType` 指示此事件是诊断事件。
* `eventTime` 值为事件发生的时间。
* `body` 部分包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="recordingstarted-event"></a>RecordingStarted 事件

检测到运动时，将激活信号入口处理器节点，媒体图中的文件接收器节点将开始写入 MP4 文件。 文件接收器节点发送操作事件。 将 `type` 设置为 `motion`，指示它是运动检测处理器的结果。 `eventTime` 值是运动发生的 UTC 时间。 有关该过程的详细信息，请参阅本快速入门中的[概述](#overview)部分。

以下是此消息的示例：

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

在上面的消息中： 

* `applicationProperties` 中的 `subject` 引用生成消息的媒体图中的节点。 在本例中，该消息来自文件接收器节点。
* `applicationProperties` 中的 `eventType` 指示此事件是操作事件。
* `eventTime` 值为事件发生的时间。 此时间为 `MediaSessionEstablished` 后视频开始流动后的 5 到 6 秒。 该时间对应于[汽车开始驶入](#review-the-sample-video)停车场时的第 5-6 秒标记处。
* `body` 部分包含有关操作事件的数据。 在本例中，数据包括 `outputType` 和 `outputLocation`。
* `outputType` 变量表示此信息与文件路径有关。
* `outputLocation` 值是 MP4 文件在边缘模块中的位置。

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped 和 RecordingAvailable 事件

如果在[图形拓扑](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)中检查信号入口处理器节点的属性，你会看到激活时间设置为 5 秒。 因此，在收到 `RecordingStarted` 事件后约 5 秒，你将获得：

* `RecordingStopped` 事件，指示录制已停止。
* `RecordingAvailable` 事件，指示现在可查看 MP4 文件。

这两个事件的发出时间通常彼此相隔数秒。
