---
title: 将基于事件的视频录制到云中并从云播放教程 - Azure
description: 在本教程中，你将了解如何使用 Azure IoT Edge 上的 Azure 实时视频分析将基于事件的视频录制到云中并从云中播放。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 938bae28b1a523e23ea9f8f1ba79bbe6c487d5db
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765193"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>教程：将基于事件的视频录制到云中并从云中播放

在本教程中，你将了解如何使用 Azure IoT Edge 上的 Azure 实时视频分析，选择性地将实时视频源的部分内容录制到云中的 Azure 媒体服务。 此用例在本教程中称为[基于事件的视频录制](event-based-video-recording-concept.md) (EVR)。 要录制实时视频的部分内容，你将使用对象检测 AI 模型在视频中查找对象，并仅在检测到某种类型的对象时录制视频剪辑。 你还将了解如何使用媒体服务播放录制的视频剪辑。 对于需要保留所需视频剪辑的各种场景，此功能非常有用。 

在本教程中，将：

> [!div class="checklist"]
> * 设置相关资源。
> * 检查执行 EVR 的代码。
> * 运行示例代码。
> * 检查结果并查看视频。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，请阅读以下文章：

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md) 
* [基于事件的视频录制](event-based-video-recording-concept.md)
* [教程：开发 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [如何编辑 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* 关于[如何在 IoT Edge 部署清单中声明路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)的部分

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)，带有 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 扩展。

    > [!TIP]
    > 系统可能会提示你安装 Docker。 请忽略该提示。
* 开发计算机上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 完成[实时视频分析资源安装脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)并[设置环境](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

完成这些步骤后，你将在 Azure 订阅中部署相关 Azure 资源：

* Azure IoT 中心
* Azure 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>概念

基于事件的视频录制是指由事件触发的视频录制过程。 可以从以下内容生成该事件：
- 视频信号本身的处理，例如在视频中检测到移动的对象时。
- 独立源，例如打开门。 

或者，可以仅在推理服务检测到特定事件发生时触发录制。 本教程将使用在高速公路上移动的车辆的视频，并在检测到卡车时录制视频剪辑。

![媒体图](./media/event-based-video-recording-tutorial/overview.png)

该图以图画形式呈现了[媒体图](media-graph-concept.md)以及用于完成所需方案的其他模块。 共涉及四个 IoT Edge 模块：

* IoT Edge 上的实时视频分析模块。
* Edge 模块，它在 HTTP 终结点后面运行 AI 模型。 此 AI 模块使用 [YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) 模型，该模型能够检测许多类型的对象。
* 一个用于筛选对象并对其进行计数的自定义模块，该模块在图中称为对象计数器。 在本教程中，你将生成一个对象计数器并对其进行部署。
* [RTSP 模拟器模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)用于模拟 RTSP 摄像机。
    
如图所示，你将使用媒体图中的 [RTSP 源](media-graph-concept.md#rtsp-source)节点捕获模拟的实时视频（高速公路上的交通流视频），并将该视频发送到两条路径：

* 第一条路径的目的地是[帧速率筛选处理器](media-graph-concept.md#frame-rate-filter-processor)节点，该节点以指定（降低的）帧速率输出视频帧。 这些视频帧将发送到 HTTP 扩展节点。 然后，该节点将这些帧作为图像转发到 AI 模块 YOLO v3（一个对象检测器）。 该节点接收结果，这些结果是模型检测到的对象（交通流中的车辆）。 然后，HTTP 扩展节点会通过 IoT 中心消息接收器节点向 IoT Edge 中心发布结果。
* 设置了 objectCounter 模块，目的是从 IoT Edge 中心接收消息，其中包括对象检测结果（交通流中的车辆）。 该模块将检查这些消息，并查找通过设置进行配置的特定类型的对象。 当找到此类对象时，此模块将向 IoT Edge 中心发送消息。 然后系统将这些“找到对象”消息路由到媒体图的 IoT 中心源节点。 接收到此类消息后，媒体图中的 IoT 中心源节点会触发[信号入口处理器](media-graph-concept.md#signal-gate-processor)节点。 然后，信号入口处理器节点会在配置的时间内处于开启状态。 在此持续时间内，视频流会经过入口到达资产接收器节点。 然后，实时流的这一部分将通过[资产接收器](media-graph-concept.md#asset-sink)节点被记录到 Azure 媒体服务帐户中的[资产](terminology.md#asset)内。

## <a name="set-up-your-development-environment"></a>设置开发环境

在开始之前，请检查是否满足[先决条件](#prerequisites)中的第三条。 资源设置脚本完成后，选择大括号，公开文件夹结构。 你将看到在 ~/clouddrive/lva-sample 目录下创建的几个文件。

![应用设置](./media/quickstarts/clouddrive.png)

本教程中涉及以下文件：

* ~/clouddrive/lva-sample/edge-deployment/.env：包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
* ~/clouddrive/lva-sample/appsetting.json：由 Visual Studio Code 用于运行示例代码。

以下步骤将需要这些文件。

1. 从 GitHub 链接 https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 克隆存储库。
1. 启动 Visual Studio Code，然后打开将存储库下载到的文件夹。
1. 在 Visual Studio Code 中，浏览到 src/cloud-to-device-console-app 文件夹，然后创建一个名为 appsettings.json 的文件。 该文件包含运行程序所需的设置。
1. 复制 ~/clouddrive/lva-sample/appsettings.json 文件中的内容。 文本应如下所示：

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    借助 IoT 中心连接字符串，可以使用 Visual Studio Code 通过 Azure IoT 中心将命令发送到 Edge 模块。
    
1. 接下来，浏览到 src/edge 文件夹并创建一个名为 .env 的文件。
1. 复制 ~/clouddrive/lva-sample/.env 文件中的内容。 文本应如下所示：

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>检查模板文件 

在上一步中，你启动了 Visual Studio Code 并打开了包含示例代码的文件夹。

在 Visual Studio Code 中，浏览到 src/edge。 你将看到创建的 .env 文件以及一些部署模板文件。 该模板定义了要部署到边缘设备 (Azure Linux VM) 的边缘模块。 .env 文件包含这些模板中所用变量的值，例如媒体服务凭据。

打开 src/edge/deployment.objectCounter.template.json。 “模块”部分下有四个条目，对应于上面“概念”部分中列出的项目：

* lvaEdge：这是 IoT Edge 上的实时视频分析模块。
* yolov3：这是使用 YOLO v3 模型构建的 AI 模块。
* rtspsim：这是 RTSP 模拟器。
* objectCounter：这是在来自 yolov3 的结果中查找特定对象的模块。

对于 objectCounter 模块，请参阅用于“image”值的字符串 (${MODULES.objectCounter})。 其基础是有关开发 IoT Edge 模块的[教程](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)。 Visual Studio Code 将自动发现 objectCounter 模块的代码位于 src/edge/modules/objectCounter 下。 

阅读[本部分](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)，了解如何在 IoT Edge 部署清单中声明路由。 然后检查模板 JSON 文件中的路由。 请注意：

* LVAToObjectCounter 用于将特定事件发送到 objectCounter 模块中的特定终结点。
* ObjectCounterToLVA 用于将触发器事件发送到 lvaEdge 模块中的特定终结点（该终结点应为 IoT 中心源节点）。
* objectCounterToIoTHub 作为调试工具使用，在运行本教程时，可借助它查看 objectCounter 的输出。

> [!NOTE]
> 检查 objectCounter 模块的所需属性，这些属性设置为查找标记为“卡车”的对象，并且可信度至少为 50%。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单 

部署清单定义要部署到边缘设备的模块以及这些模块的配置设置。 请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

使用 Visual Studio Code，按照[以下说明](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution)登录到 Docker。 然后选择“生成并推送 IoT Edge 解决方案”。 在此步骤中使用 src/edge/deployment.objectCounter.template.json。

![生成并推送 IoT Edge 解决方案](./media/event-based-video-recording-tutorial/build-push.png)

此操作会生成用于对象计数的 objectCounter 模块，并将图像推送到 Azure 容器注册表。

* 检查确认已在 .env 文件中定义 CONTAINER_REGISTRY_USERNAME_myacr 和 CONTAINER_REGISTRY_PASSWORD_myacr 环境变量。

此步骤会在 src/edge/config/deployment.objectCounter.amd64.json 创建 IoT Edge 部署清单。 右键单击该文件，然后选择“为单个设备创建部署”。

![为单个设备创建部署](./media/quickstarts/create-deployment-single-device.png)

如果这是你第一次接触有关 IoT Edge 上的实时视频分析的教程，Visual Studio Code 将提示你输入 IoT 中心连接字符串。 可以从 appsettings.json 文件中复制字符串。

接下来，Visual Studio Code 会要求你选择 IoT 中心设备。 选择 IoT Edge 设备（应为 lva-sample-device）。

在此阶段，将启动将边缘模块部署到 IoT Edge 设备的过程。
大约 30 秒后，在 Visual Studio Code 的左下部分刷新 Azure IoT 中心。 你应该会看到已部署四个模块，它们分别名为 lvaEdge、rtspsim、yolov3 和 objectCounter。

![部署的 4 个模块](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>准备监视事件

若要查看来自 objectCounter 模块和 IoT Edge 上的实时视频分析模块的事件，请执行以下步骤：

1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击 lva-sample-device 文件，然后选择“开始监视内置事件终结点”。

   ![开始监视内置事件终结点](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>运行程序

1. 在 Visual Studio Code 中，转到 src/cloud-to-device-console-app/operations.json。

1. 在 GraphTopologySet 节点下，编辑以下内容：

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. 接下来，在 GraphInstanceSet 和 GraphTopologyDelete 节点下，编辑 ：

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. 选择 F5 以启动调试会话。 在“终端”窗口中，你将看到一些输出的消息。

1. operations.json 文件首先调用 GraphTopologyList 和 GraphInstanceList。 如果在先前的快速入门或教程后清理了资源，此操作会返回空列表，然后暂停以便你能够选择 Enter，如下所示：

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

1. 在“终端”窗口中选择 Enter 后，会执行下一组直接方法调用 ：
   * 使用上述 topologyUrl 调用 GraphTopologySet
   * 使用以下正文调用 GraphInstanceSet
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
   * 再次调用 GraphInstanceList 以显示图形实例处于运行状态
     
1. “终端”窗口中的输出现在会在出现“按 Enter 继续”提示时暂停 。 此时请勿选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。

1. 如果现在切换到 Visual Studio Code 中的“输出”窗口，则将看到 IoT Edge 上的实时视频分析模块向 IoT 中心发送消息。

   下节中讨论了这些消息。
     
1. 图形实例继续运行并录制视频。 RTSP 模拟器不断循环源视频。 根据下一部分中的介绍审阅这些消息。 然后，若要停止实例，请返回“终端”窗口，并选择 Enter 。 接下来会使用以下方法执行一系列调用，以清理资源：

   * 调用 GraphInstanceDeactivate 以停用图形实例。
   * 调用 GraphInstanceDelete 以删除实例。
   * 调用 GraphTopologyDelete 以删除拓扑。
   * 最后调用 GraphTopologyList 以显示列表现在为空。

## <a name="interpret-the-results"></a>解释结果 

运行媒体图时，IoT Edge 上的实时视频分析模块会将某些诊断和操作事件发送到 IoT Edge 中心。 这些事件即你在 Visual Studio Code 的“输出”窗口中看到的消息。 这些消息包含 body 部分和 applicationProperties 部分。 要了解这些部分表示的内容，请参阅[创建和读取 IoT 中心消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。

## <a name="diagnostics-events"></a>诊断事件

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件 

实例化媒体图后，RTSP 源节点尝试连接到在 RTSP 模拟器容器上运行的 RTSP 服务器。 如果成功，它将输出此事件。 事件类型为 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished。

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* 此消息是一个诊断事件 (MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
* applicationProperties 中的 subject 部分引用生成消息的图形拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* applicationProperties 中的 eventType 部分指示这是诊断事件。
* eventTime 部分指示事件发生的时间。 这是交通流视频（MKV 文件）以实时流的形式开始进入模块的时间。
* body 部分包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。


## <a name="operational-events"></a>操作事件

媒体图运行一段时间后，最终将从 objectCounter 模块获取事件。 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties 部分包含事件时间。 这是 objectCounter 模块观察到来自 yolov3 模块的结果包含相关对象（卡车）的时间。

随着在视频中检测到其他卡车，你会看到显示出更多的此类事件。

### <a name="recordingstarted-event"></a>RecordingStarted 事件

对象计数器发送事件后，你几乎立即会看到一个类型为 Microsoft.Media.Graph.Operational.RecordingStarted 的事件：

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

applicationProperties 中的 subject 部分引用图形中的资产接收器节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的 Azure 媒体服务资产的名称。 记下此值。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

资产接收器节点将视频数据上传到资产后，将发出类型为 Microsoft.Media.Graph.Operational.RecordingAvailable 的此事件：

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

此事件指示已向资产写入足够的数据，播放器或客户端可以开始播放视频。 applicationProperties 中的 subject 部分引用图形中的 AssetSink 节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的 Azure 媒体服务资产的名称。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

如果检查[拓扑](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)中信号入口处理器节点的激活设置 (maximumActivationTime)，将会看到“入口”设置为在 30 秒视频流经过后关闭入口。 RecordingStarted 事件后的大约 30 秒，应会看到一个类型为 Microsoft.Media.Graph.Operational.RecordingStopped 的事件。 该事件指示资产接收器节点已停止将视频录制到资产。

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

此事件指示录制已停止。 applicationProperties 中的 subject 部分引用图形中的 AssetSink 节点，该节点生成此消息。 body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的 Azure 媒体服务资产的名称。

## <a name="media-services-asset"></a>媒体服务资产  

可以通过登录到 Azure 门户并观看视频来检查图创建的媒体服务资产。

1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅中的资源中找到媒体服务帐户。 打开帐户窗格。
1. 选择“媒体服务”列表中的“资产” 。

    ![资产](./media/continuous-video-recording-tutorial/assets.png)
1. 将找到以名称 sampleAssetFromEVR-LVAEdge-{DateTime} 列出的资产。 这是 RecordingStarted 事件的 outputLocation 属性中提供的名称。 拓扑中的 assetNamePattern 决定了此名称的生成方式。
1. 选择资产。
1. 在“资产详细信息”页上，选择“流式处理 URL”文本框下的“新建” 。

    ![新建资产](./media/continuous-video-recording-tutorial/new-asset.png)

1. 在打开的向导中，接受默认选项，然后选择“添加”。 有关详细信息，请参阅[视频播放](video-playback-concept.md)。

    > [!TIP]
    > 请确保[流式处理终结点正在运行](../latest/streaming-endpoint-concept.md)。
1. 播放器应加载视频。 选择“播放”观看该视频。

> [!NOTE]
> 由于视频源是模拟相机源的容器，因此视频中的时间戳与激活图形实例的时间和停用该实例的时间有关。 如果使用[播放多天的录制内容](playback-multi-day-recordings-tutorial.md)教程中内置的播放控件，可以看到屏幕上显示视频中的时间戳。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他教程，请保留创建的资源。 如果不想，请转到 Azure 门户，浏览到资源组，选择运行本教程时所用的资源组，然后删除该资源组。

## <a name="next-steps"></a>后续步骤

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性产品页](https://www.onvif.org/conformant-products/)上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 照相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。
