---
title: 使用自己的模型分析实时视频 - Azure
description: 在本快速入门中，你将应用计算机视觉来分析来自（模拟）IP 相机的实时视频源。
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261484"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>快速入门：使用自己的模型分析实时视频

本快速入门介绍了如何通过应用用于检测对象的计算机视觉模型，在 IoT Edge 上使用实时视频分析来分析（模拟）IP 相机中的实时视频源。 实时视频源中的一部分帧被发送到推理服务，其结果被发送到 IoT Edge 中心。 它将 Azure VM 用作 IoT Edge 设备和模拟的实时视频流。 本文基于用 C# 编写的示例代码。

本文内容基于[此](detect-motion-emit-events-quickstart.md)快速入门。 

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 计算机上安装了 [Visual Studio Code](https://code.visualstudio.com/) 且具有以下扩展：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* 系统上安装了 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* 如果之前未完成[此](detect-motion-emit-events-quickstart.md)快速入门，请完成以下步骤：
     * [设置 Azure 资源](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> 在安装 Azure IoT Tools 时，系统可能会提示安装 Docker。 可以忽略它。

## <a name="review-the-sample-video"></a>观看示例视频
作为上述 Azure 资源设置步骤的一部分，一个高速公路车流量（短）视频将被复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 本教程将使用此视频文件模拟实时流。

可以使用 [VLC Player](https://www.videolan.org/vlc/) 等应用程序，启动它，按 Control+N，然后将[此](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)链接粘贴到视频中并开始播放。 你将看到一个视频，其中显示高速公路上的车流量，有许多车辆在公路上行驶。

完成下面的步骤后，你将在 IoT Edge 上使用实时视频分析来检测对象（例如车辆、人员等），并将相关的推理事件发布到 IoT Edge 中心。

## <a name="overview"></a>概述

![概述](./media/quickstarts/overview-qs5.png)

上图显示本快速入门中信号的流动方式。 Edge 模块（[此处](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)进行了详细介绍）模拟托管 RTSP 服务器的 IP 相机。 [RTSP 源](media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)节点。 该处理器会限制到达 [HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)节点的视频流的帧速率。 

HTTP 扩展节点扮演代理的角色，方法是将视频帧转换为指定的图像类型，并将图像通过 REST 中继到另一个在 HTTP 终结点后面运行 AI 模型的 Edge 模块。 在此示例中，使用 [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型构建该 Edge 模块，它能够检测多种类型的对象。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](media-graph-concept.md#iot-hub-message-sink )节点，然后将这些事件发送到 [IoT Edge 中心](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本快速入门中，请执行以下操作：

1. 创建并部署媒体图。
1. 解释结果。
1. 清理资源。



## <a name="create-and-deploy-the-media-graph"></a>创建和部署媒体图
    
### <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

作为前提条件的一部分，需要将示例代码下载到一个文件夹中。 启动 Visual Studio Code，然后打开文件夹。

1. 在 Visual Studio Code 中，浏览到“src/edge”。 你将看到创建的 .env 文件以及一些部署模板文件。

    * 部署模板是指含有某些占位符值的边缘设备的部署清单。 .env 文件含有这些变量的值。
1. 接下来，浏览到“src/cloud-to-device-console-app”文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 此文件列出希望程序运行的各种不同操作。
    * Program.cs - 它是执行以下操作的示例程序代码：

        * 加载应用设置。
        *  调用 IoT Edge 模块上的实时视频分析所公开的直接方法。 可以通过调用该模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流 
        * 会暂停并等待你检查 TERMINAL 窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件
        * 调用直接方法来清理资源   


1. 对 operations.json 文件进行以下编辑
    * 将链接更改为图拓扑：`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * 在 GraphInstanceSet 下，编辑图拓扑的名称，使其与上面链接中的值 `"topologyName" : "InferencingWithHttpExtension"` 匹配
    * 在 GraphTopologyDelete 下，编辑名称 `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单

1. 右键单击“src/edge/ deployment.yolov3.template.json”文件并单击“生成 IoT Edge 部署清单”。

    ![生成 IoT Edge 部署清单](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. 这应在 src/edge/config 文件夹中创建一个名为“deployment.yolov3.amd64.json”的清单文件。
1. 如果之前已完成[快速入门](detect-motion-emit-events-quickstart.md)，则跳过此步骤。 否则，通过单击左下角 AZURE IOT 中心窗格旁边的“更多操作”图标，设置 IoTHub 连接字符串。 可以从 appsettings.json 文件中复制字符串。 （还有一种建议的方法，可确保通过[选择 IoT 中心命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)在 Visual Studio Code 中配置正确的 IoT 中心）。
    
    ![IoTHub 连接字符串](./media/quickstarts/set-iotconnection-string.png)
1. 接下来，右键单击“src/edge/config/ deployment.yolov3.amd64.json”，再单击“为单个设备创建部署”。 

    ![为单个设备创建部署](./media/quickstarts/create-deployment-single-device.png)
1. 然后，系统会提示“选择 IoT 中心设备”。 从下拉列表中选择 lva-sample-device。
1. 在大约 30 秒内，刷新左下部分的 Azure IoT 中心，应可看到边缘设备已部署以下模块：

    1. 实时视频分析模块，名为“lvaEdge”。
    1. 名为“rtspsim”的模块，可模拟 RTSP 服务器，充当实时视频源的源。
    1. 名为“yolov3”的模块，顾名思义，是 YOLOv3 对象检测模型，该模型将计算机视觉应用于图像并返回对象类型的多个类。
 
        ![YOLOv3 对象检测模型](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>准备监视事件

右键单击实时视频分析设备，再单击“开始监视内置事件终结点”。 若要监视 IoT 中心事件并在 Visual Studio Code 的“输出”窗口中查看该事件，需要执行此步骤。 

![开始监视](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>运行示例程序

1. 启动调试会话（按 F5）。 在 TERMINAL 窗口中，你将开始看到一些输出的消息。
1. perations.json 首先开始调用直接方法 GraphTopologyList 和 GraphInstanceList。 如果在学完先前的快速入门后清理了资源，这会返回空列表，然后会暂停以便你能够按 Enter
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. 在 TERMINAL 窗口中按“Enter”键后，会执行下一组直接方法调用
     * 使用上述 topologyUrl 调用 GraphTopologySet
     * 使用以下正文调用 GraphInstanceSet
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * 调用 GraphInstanceActivate 以启动图形实例，并启动视频流
     * 第二次调用 GraphInstanceList 以显示图形实例确实处于运行状态
1. TERMINAL 窗口中的输出将在“按 Enter 继续”提示出现时暂停。 此时请勿按“Enter”。 可以向上滚动，查看调用的直接方法的 JSON 响应有效负载
1. 如果现在切换到 Visual Studio Code 中的 OUTPUT 窗口，则会看到 IoT Edge 模块上的实时视频分析发送到 IoT 中心的消息。
     * 以下部分将介绍这些消息
1. 媒体图将继续运行，并打印结果，而 RTSP 模拟器将继续循环播放源视频。 为了停止媒体图，请返回到 TERMINAL 窗口并按“Enter”。 接下来会执行一系列调用，以清理资源：
     * 调用 GraphInstanceDeactivate 以停用图形实例
     * 调用 GraphInstanceDelete 以删除实例
     * 调用 GraphTopologyDelete 以删除拓扑
     * 最后调用 GraphTopologyList 以显示列表现在为空

## <a name="interpret-results"></a>解释结果

运行媒体图时，来自 Http Extension 处理器节点的结果将通过 IoT 中心接收器节点发送到 IoT 中心。 Visual Studio Code 的 OUTPUT 窗口中显示的消息包含“正文”部分和“applicationProperties”部分。 若要理解这些部分的内容，请阅读[本文](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。 



### <a name="mediasession-established-event"></a>MediaSession 建立的事件

实例化媒体图后，RTSP 源节点尝试连接到在 rtspsim-live55 容器上运行的 RTSP 服务器。 如果成功，它会输出此事件。 事件类型为 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

请注意上述消息中的以下内容：
* 消息是一个诊断事件 (MediaSessionEstablished)，指示 RTSP 源节点（使用者）能够与 RTSP 模拟器建立连接，并开始接收（模拟的）实时源。
* applicationProperties 中的“subject”指示消息是从媒体图中的 RTSP 源节点生成的。
* applicationProperties 中的“eventType”指示这是诊断事件。
* “eventTime”指示事件发生的时间。
* “正文”包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从 yolov3 模块接收推理结果，并通过 IoT 中心接收器节点将其作为推理事件发出。 在这些事件中，类型设置为“实体”，用于指示它是实体，如汽车或卡车等，而 eventTime 则指示检测到该对象的时间 (UTC)。 下面是一个示例，显示检测到的两辆汽车在同一视频帧中具有不同置信度。

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

请注意上述消息中的以下内容：

* applicationProperties 中的“subject”引用生成消息的图形拓扑中的节点。 
* applicationProperties 中的“eventType”指示这是分析事件。
* “eventTime”指示事件发生的时间。
* “body”包含有关分析事件的数据。 在这种情况下，该事件是推理事件，因此正文包含“inferences”数据。
* “inferences”部分表示“type”为“entity”，并且具有与“entity”有关的其他数据。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他快速入门，请保留创建的资源。 如果不想，请转到 Azure 门户，浏览到资源组，选择运行本快速入门时所用的资源组，并删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不使用 RTSP 模拟器。 可以在 [ONVIF 一致性](https://www.onvif.org/conformant-products/)产品页上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)中的说明进行操作，然后按照此[将首个 IoT Edge 模块部署到虚拟 Linux 设备](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。

