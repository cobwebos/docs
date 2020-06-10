---
title: 检测运动并发出事件 - Azure
description: 本快速入门介绍如何以编程方式调用直接方法，从而使用 IoT Edge 上的实时视频分析来检测运动和发出事件。
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261583"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>快速入门：检测运动并发出事件

本快速入门将引导你完成开始使用 IoT Edge 上的实时视频分析的步骤。 它将 Azure VM 用作 IoT Edge 设备和模拟的实时视频流。 完成设置步骤后，你将能通过媒体图运行模拟实时视频流，该媒体图可检测和报告该流中的任何运动。 下图显示了该媒体图的图形表示形式。

![基于运动检测的实时视频分析](./media/analyze-live-video/motion-detection.png) 

本文基于用 C# 编写的[示例代码](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp)。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 计算机上的 [Visual Studio Code](https://code.visualstudio.com/) 具有以下扩展：
    1. [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* 系统上安装了 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)

> [!TIP]
> 安装 Azure IoT Tools 扩展时，系统可能会提示你安装 Docker。 可以忽略此提示。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

本教程需要以下 Azure 资源。

* IoT 中心
* 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

在快速入门中，我们建议你使用[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)在 Azure 订阅中部署上述 Azure 资源。 为此，请执行以下步骤：

1. 浏览到 https://shell.azure.com 。
1. 如果这是你首次使用 Cloud Shell，系统将提示你选择一个订阅以创建存储帐户和 Microsoft Azure 文件存储共享。 选择“创建存储”，创建用于存储 Cloud Shell 会话信息的存储帐户。 此存储帐户不同于脚本将要创建的与 Azure 媒体服务帐户配合使用的帐户。
1. 在 shell 窗口左侧的下拉列表中选择“Bash”作为环境。

    ![环境选择器](./media/quickstarts/env-selector.png)

1. 运行以下命令

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    如果脚本成功完成，你应该可在订阅中看到上述所有资源。

1. 脚本完成后，单击大括号即可公开文件夹结构。 你将看到在 ~/clouddrive/lva-sample 目录下创建的几个文件。 与本快速入门相关的文件如下：

     * ~/clouddrive/lva-sample/edge-deployment/.env - 包含 Visual Studio Code 用于将模块部署到边缘设备的属性
     * ~/clouddrive/lva-sample/appsetting.json - Visual Studio Code 将其用于运行示例代码
     
在快速入门的后续部分中，将需要这些文件来更新 Visual Studio Code 中的文件。 现在需要将它们复制到本地文件中。


 ![应用设置](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>设置开发环境

1. 从此处克隆存储库： https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp 。
1. 启动 Visual Studio Code，然后打开下载的存储库所在的文件夹。
1. 在 Visual Studio Code 中，浏览到“src/cloud-to-device-console-app”文件夹，然后创建一个名为“appsettings.json”的文件。 该文件将包含运行程序所需的设置。
1. 复制上一部分中生成的 ~/clouddrive/lva-sample/appsettings.json 文件（请参阅步骤 5）中的内容

    文本应如下所示：

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. 接下来，浏览到“src/edge”文件夹并创建一个名为“.env”的文件。
1. 复制“/clouddrive/lva-sample/edge-deployment/.env”文件中的内容。 文本应如下所示：

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>检查示例文件

1. 在 Visual Studio Code 中，浏览到“src/edge”。 你将看到创建的 .env 文件以及一些部署模板文件。

    部署模板是指具有某些占位符值的边缘设备的部署清单。 该 .env 文件具有这些变量的值。
1. 接下来，浏览到“src/cloud-to-device-console-app”文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 此文件列出了你想要程序运行的不同操作。
    * Program.cs - 可执行以下操作的示例程序代码：
    
        * 加载应用设置
        * 在 IoT Edge 模块上调用实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流 
        * 暂停以检查 TERMINAL 窗口中程序的输出以及 OUTPUT 窗口中模块生成的事件
        * 调用直接方法以清理资源   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单

部署清单定义要部署到边缘设备的模块以及这些模块的配置设置。 请按照以下步骤从模板文件生成此类清单，然后将其部署到边缘设备。

1. 打开 Visual Studio Code
1. 单击左下角 AZURE IOT 中心窗格旁边的“更多操作”图标，设置 IoTHub 连接字符串。 可以从 src/cloud-to-device-console-app/appsettings.json 文件复制该字符串。 

    ![设置 IoT 连接字符串](./media/quickstarts/set-iotconnection-string.png)
1. 接下来，右键单击“src/edge/deployment.template.json”文件并单击“生成 IoT Edge 部署清单”。
    ![生成 IoT Edge 部署清单](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    这应在 src/edge/config 文件夹中创建一个名为“deployment.amd64.json”的清单文件。
1. 右键单击“src/edge/config/deployment.amd64.json”，单击“为单个设备创建部署”，然后选择边缘设备的名称。

    ![为单个设备创建部署](./media/quickstarts/create-deployment-single-device.png)
1. 然后，系统会要求你“选择 IoT 中心设备”。 从下拉列表中选择 lva-sample-device。
1. 在约 30 秒后，刷新左下部分的 Azure IoT 中心，应会看到边缘设备已部署以下模块：

    * IoT Edge 上的实时视频分析（模块名称为“lvaEdge”）
    * RTSP 模拟器（模块名称为“rtspsim”）

RTSP 模拟器模块使用存储的视频文件模拟实时视频流，该文件已在运行[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)时复制到边缘设备。 在此阶段，你已部署模块，但没有媒体图处于活动状态。

## <a name="prepare-for-monitoring-events"></a>准备监视事件

你将在 IoT Edge 模块上使用实时视频分析来检测传入的实时视频流中的动作并将事件发送到 IoT 中心。 若要查看这些事件，请执行以下步骤：

1. 在 Visual Studio Code 中打开“资源管理器”窗格，然后在左下角查找“Azure IoT 中心”。
1. 展开“设备”节点。
1. 右键单击“lva-sample-device”，然后选择“开始监视内置事件监视”选项。

    ![开始监视内置事件终结点](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>运行示例程序

请按照以下步骤运行示例代码。
1. 在 Visual Studio Code 中，导航到“src/cloud-to-device-console-app/operations.json”。
1. 在节点 GraphTopologySet 下，确保以下内容：

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. 接下来，在节点 GraphInstanceSet 和 GraphTopologyDelete 下，确保 topologyName 的值与上述图形拓扑中的“name”属性的值匹配：

    `"topologyName" : "MotionDetection"`
    
1. 启动调试会话（点击 F5）。 在 TERMINAL 窗口中，你将开始看到一些输出的消息。
1. operations.json 首先调用 GraphTopologyList 和 GraphInstanceList。 如果已在学完先前的快速入门后清理了资源，这会返回空列表，然后会暂停以便你能够点击 Enter。

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
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "MotionDetection",
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
     
     * 调用 GraphInstanceActivate 以启动图形实例，并启动视频流。
     * 再次调用 GraphInstanceList 以显示图形实例确实处于运行状态。
1. TERMINAL 窗口中的输出将在出现“按 Enter 继续”提示时暂停。 此时请勿点击“Enter”。 可以向上滚动，查看调用的直接方法的 JSON 响应有效负载
1. 如果现在切换到 Visual Studio Code 中的“输出”窗口，则会看到 IoT Edge 上的实时视频分析模块发送到 IoT 中心的消息。
     * 以下各部分将介绍这些消息
1. 媒体图将继续运行，并打印结果，而 RTSP 模拟器将继续循环播放源视频。 为了停止媒体图，请返回到 TERMINAL 窗口并按“Enter”。 接下来会执行一系列调用，以清理资源：
     * 调用 GraphInstanceDeactivate 以停用图形实例
     * 调用 GraphInstanceDelete 以删除实例
     * 调用 GraphTopologyDelete 以删除拓扑
     * 最后调用 GraphTopologyList 以显示列表现在为空

## <a name="interpret-results"></a>解释结果

运行媒体图时，来自运动检测器处理器节点的结果将通过 IoT 中心接收器节点发送到 IoT 中心。 Visual Studio Code 的 OUTPUT 窗口中显示的消息包含“body”部分和“applicationProperties”部分。 若要理解这些部分的内容，请阅读[本文](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。

## <a name="mediasession-established-event"></a>MediaSession 建立的事件

实例化媒体图后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果成功，它将输出此事件：

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

* 该消息是一个诊断事件 (MediaSessionEstablished)，指示 RTSP 源节点 (subject) 能够与 RTSP 模拟器建立连接，并开始接收（模拟的）实时源。
* applicationProperties 中的“subject”引用生成消息的图形拓扑中的节点。 在本例中，该消息来自 RTSP 源节点。
* applicationProperties 中的“eventType”指示这是诊断事件。
* “eventTime”指示事件发生的时间。
* “body”包含有关诊断事件的数据，在本例中是 [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 的详细信息。


## <a name="motion-detection-event"></a>运动检测事件

当检测到动作时，实时视频分析 Edge 模块将发送推理事件。 将类型设置为“运动”，指示它是运动检测处理器的结果，eventTime 则指示运动发生的时间 (UTC)。 下面是一个示例：

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* applicationProperties 中的“subject”引用生成消息的媒体图中的节点。 在本例中，该消息来自运动检测处理器节点。
* applicationProperties 中的“eventType”指示这是分析事件。
* “eventTime”指示事件发生的时间。
“body”包含有关分析事件的数据。 在本例中，该事件是推理事件，因此“body”包含“timestamp”和“inferences”数据。
* “inferences”数据表示“type”为“motion”，并且具有与“motion”事件有关的其他数据。
* “box”部分包含移动对象周围的边界框的坐标。 值按视频的宽度和高度（以像素为单位）进行规范化（例如 宽度为 1920，高度为 1080）。

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>清理资源

如果想学习其他快速入门，请保留创建的资源。 否则，请转到 Azure 门户，浏览到资源组，选择运行本快速入门所用的资源组，并删除所有资源。

## <a name="next-steps"></a>后续步骤

运行其他快速入门，如检测实时视频源中的对象。        
