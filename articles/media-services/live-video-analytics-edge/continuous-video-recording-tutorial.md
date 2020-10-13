---
title: 将视频连续录制到云中并从云中播放教程 - Azure
description: 在本教程中，你将了解如何使用 Azure IoT Edge 上的 Azure 实时视频分析将视频连续录制到云中并使用 Azure 媒体服务流式传输该视频的任何部分。
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 4333ceb9c02f39629e4bd06d3d9634b97bb2e2d7
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91774022"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>教程：将视频连续录制到云中并从云中播放

在本教程中，你将了解如何使用 Azure IoT Edge 上的 Azure 实时视频分析对云执行[连续视频录制](continuous-video-recording-concept.md) (CVR) 并使用 Azure 媒体服务流式传输该视频的任何部分。 此功能对于诸如安全性和合规性等方案很有用，因为这些方案需要将相机中的素材存档保存数天或数周。 

在本教程中，将：

> [!div class="checklist"]
> * 设置相关资源。
> * 检查执行 CVR 的代码。
> * 运行示例代码。
> * 检查结果并查看视频。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，请阅读以下文章：

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md) 
* [连续视频录制方案](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)，带有 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 扩展。

    > [!TIP]
    > 系统可能会提示你安装 Docker。 请忽略该提示。
* 开发计算机上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 完成[实时视频分析资源安装脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)。

完成这些步骤后，你将在 Azure 订阅中部署相关 Azure 资源：

* Azure IoT 中心
* Azure 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)

## <a name="concepts"></a>概念

如[媒体图概念](media-graph-concept.md)一文中所述，使用媒体图可以定义：

- 应从何处捕获媒体。
- 应如何处理媒体。
- 应将结果交付到何处。 
 
 若要完成 CVR，需要从支持 RTSP 的相机中捕获视频，并将其连续记录到 [Azure 媒体服务资产](terminology.md#asset)。 下图显示了该媒体图的图形表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="媒体图":::

在本教程中，你将通过使用 [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 生成的一个 Edge 模块来模拟 RTSP 相机。 在媒体图中，你将使用 [RTSP 源](media-graph-concept.md#rtsp-source)节点获取实时源，并将该视频发送到[资产接收器节点](media-graph-concept.md#asset-sink)，后者会将视频记录到资产中。

## <a name="set-up-your-development-environment"></a>设置开发环境

在开始之前，请检查是否满足[先决条件](#prerequisites)中的第三条。 资源设置脚本完成后，选择大括号，公开文件夹结构。 你将看到在 ~/clouddrive/lva-sample 目录下创建的几个文件。

![应用设置](./media/quickstarts/clouddrive.png)

本教程中涉及以下文件：

* ~/clouddrive/lva-sample/edge-deployment/.env：包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
* ~/clouddrive/lva-sample/appsettings.json：由 Visual Studio Code 用于运行示例代码。

以下步骤将需要这些文件：

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

## <a name="examine-the-sample-files"></a>检查示例文件

在 Visual Studio Code 打开“src/edge/deployment.template.json”。 该模板定义了要部署到边缘设备 (Azure Linux VM) 的边缘模块。 “模块”部分下有两个条目，其名称如下：

* lvaEdge：这是 IoT Edge 上的实时视频分析模块。
* rtspsim：这是 RTSP 模拟器。

接下来，浏览到 src/cloud-to-device-console-app 文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

* c2d-console-app.csproj：Visual Studio Code 的项目文件。
* operations.json：该文件列出了你将运行的不同操作。
* Program.cs：执行以下操作的示例程序代码：
    * 加载应用设置。
    * 在 IoT Edge 模块上调用实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
    * 暂停以检查“终端”窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件 。
    * 调用直接方法以清理资源。

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单 

部署清单定义要部署到边缘设备的模块以及这些模块的配置设置。 请按照以下步骤从模板文件生成清单，然后将其部署到边缘设备。

1. 启动 Visual Studio Code。
1. 选择左下角“AZURE IOT 中心”窗格旁边的“更多操作”图标，设置 IoT 中心连接字符串 。 从 src/cloud-to-device-console-app/appsettings.json 文件复制该字符串。 

    ![设置 IoT 中心连接字符串](./media/quickstarts/set-iotconnection-string.png)
1. 右键单击“src/edge/deployment.template.json”文件，然后选择“生成 IoT Edge 部署清单”。 Visual Studio Code 使用 .env 文件中的值来替换在部署模板文件中找到的变量。 此操作在 src/edge/config 文件夹中创建一个名为 deployment.amd64.json 的清单文件。

   ![生成 IoT Edge 部署清单](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. 右键单击“src/edge/config/deployment.amd64.json”文件，，然后选择“为单个设备创建部署”。

   ![为单个设备创建部署](./media/quickstarts/create-deployment-single-device.png)
1. 然后，系统会要求你“选择 IoT 中心设备”。 从下拉列表中选择 lva-sample-device。
1. 大约 30 秒后，在左下部分刷新 Azure IoT 中心。 应会看到边缘设备已部署以下模块：
    * IoT Edge 上的实时视频分析（模块名称为“lvaEdge”）
    * RTSP 模拟器（模块名称为“rtspsim”）
 
    ![IoT 中心](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>准备监视模块 

使用 IoT Edge 模块上的实时视频分析来记录实时视频流时，它会将事件发送到 IoT 中心。 若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击 lva-sample-device 文件，然后选择“开始监视内置事件终结点”。

    ![开始监视内置事件终结点](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>运行程序 

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="媒体图":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="媒体图"
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
1. 图形实例继续运行并录制视频。 RTSP 模拟器不断循环源视频。 若要停止录制，请返回到“终端”窗口并选择 Enter 。 接下来会使用以下方法执行一系列调用，以清理资源：

   * 调用 GraphInstanceDeactivate 以停用图形实例。
   * 调用 GraphInstanceDelete 以删除实例。
   * 调用 GraphTopologyDelete 以删除拓扑。
   * 最后调用 GraphTopologyList 以显示列表现在为空。

## <a name="interpret-the-results"></a>解释结果 

运行媒体图时，IoT Edge 上的实时视频分析模块会将某些诊断和操作事件发送到 IoT Edge 中心。 这些事件即你在 Visual Studio Code 的“输出”窗口中看到的消息。 这些消息包含 body 部分和 applicationProperties 部分。 要了解这些部分表示的内容，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。

## <a name="diagnostics-events"></a>诊断事件 

### <a name="mediasession-established-event"></a>MediaSession 建立的事件

激活图形实例后，RTSP 源节点会尝试连接到在 rtspsim 模块中运行的 RTSP 服务器。 如果成功，它将输出此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* 此消息是一个诊断事件 (MediaSessionEstablished)。 它指示 RTSP 源节点（使用者）建立了与 RTSP 模拟器的连接，并开始接收（模拟的）实时馈送。
* applicationProperties 中的 subject 部分引用生成消息的图形拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* applicationProperties 中的 eventType 部分指示这是诊断事件。
* eventTime 部分指示事件发生的时间。
* body 部分包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。

## <a name="operational-events"></a>操作事件 

### <a name="recordingstarted-event"></a>RecordingStarted 事件

当资产接收器节点开始录制视频时，将发出此事件，其类型为 Microsoft.Media.Graph.Operational.RecordingStarted：

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

applicationProperties 中的 subject 部分引用图形中的资产接收器节点，该节点生成此消息。

body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的 Azure 媒体服务资产的名称。 记下此值。

### <a name="recordingavailable-event"></a>RecordingAvailable 事件

顾名思义，RecordingStarted 事件在录制开始时发送，但视频数据可能尚未上传到资产。 资产接收器节点将视频数据上传到资产后，将发出类型为 Microsoft.Media.Graph.Operational.RecordingAvailable 的此事件：

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

此事件指示已向资产写入足够的数据，播放器或客户端可以开始播放视频。

applicationProperties 中的 subject 部分引用图形中的 AssetSink 节点，该节点生成此消息。

body 部分包含有关输出位置的信息。 在本例中，即要将视频录制到的 Azure 媒体服务资产的名称。

### <a name="recordingstopped-event"></a>RecordingStopped 事件

停用 Graph 实例后，资产接收器节点会停止将视频录制到资产。 它发出类型为 Microsoft.Media.Graph.Operational.RecordingStopped 的此事件：

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

此事件指示录制已停止。

applicationProperties 中的 subject 部分引用图形中的 AssetSink 节点，该节点生成此消息。

正文部分包含有关输出位置的信息，在本例中是用于录制视频的 Azure 媒体服务资产的名称。

## <a name="media-services-asset"></a>媒体服务资产  

可以通过登录到 Azure 门户并观看视频来检查媒体图创建的媒体服务资产。

1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。
1. 在订阅中的资源中找到媒体服务帐户，并打开“帐户”窗格。
1. 选择“媒体服务”列表中的“资产” 。

    ![媒体服务资产](./media/continuous-video-recording-tutorial/assets.png)
1. 将找到以名称 sampleAsset-CVRToAMSAsset-Sample-Graph-1 列出的资产。 这是在图形拓扑文件中选择的命名模式。
1. 选择资产。
1. 在“资产详细信息”页上，选择“流式处理 URL”文本框下的“新建” 。

    ![新建资产](./media/continuous-video-recording-tutorial/new-asset.png)

1. 在打开的向导中，接受默认选项，然后选择“添加”。 有关详细信息，请参阅[视频播放](video-playback-concept.md)。

    > [!TIP]
    > 请确保[流式处理终结点正在运行](../latest/streaming-endpoint-concept.md)。
1. 播放器应加载视频。 选择“播放”观看该视频。

> [!NOTE]
> 由于视频源是模拟相机源的容器，因此视频中的时间戳与激活图形实例的时间和停用该实例的时间有关。 若有了解如何浏览多天的录制并查看该存档的部分内容，请参阅[播放多天录制的内容](playback-multi-day-recordings-tutorial.md)教程。 在该教程中，还可以在屏幕上显示的视频中看到时间戳。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他教程，请保留创建的资源。 如果不想，请转到 Azure 门户，浏览到资源组，选择运行本教程时所用的资源组，然后删除该资源组。

## <a name="next-steps"></a>后续步骤

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 一致性产品页](https://www.onvif.org/conformant-products/)上查找符合配置文件 G、S 或 T 的设备来搜索支持 RTSP 的 IP 照相机。
* 使用 AMD64 或 X64 Linux 设备（与使用 Azure Linux VM 相比）。 此设备必须与 IP 相机位于同一网络中。 按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)快速入门中的说明进行操作，将设备注册到 Azure IoT 中心。
