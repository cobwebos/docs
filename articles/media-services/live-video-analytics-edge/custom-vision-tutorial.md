---
title: 使用 IoT Edge 上的实时视频分析和 Azure 自定义视觉分析实时视频
description: 了解如何使用自定义视觉构建可检测玩具卡车的一个容器化模型，并使用 IoT Edge 上的实时视频分析 (LVA) 的 AI 扩展功能在边缘上部署该模型，以便从实时视频流中检测玩具卡车。
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 5da3186e64dd369dc57a0d5d1b635fc082158765
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804140"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>教程：使用 IoT Edge 上的实时视频分析和 Azure 自定义视觉分析实时视频

在本教程中，你将了解如何使用[自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)构建可检测玩具卡车的一个容器化模型，并使用 IoT Edge 上的实时视频分析的 [AI 扩展功能](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)在边缘上部署该模型，以便从实时视频流中检测玩具卡车。

我们将向你展示如何将自定义视觉的功能（只需上传并标记一些图像，就能构建和训练计算机视觉模型，无需了解数据科学、ML 或 AI）与实时视频分析的功能结合使用，以轻松地将自定义模型部署为边缘上的容器并分析模拟的实时视频源。 本教程使用 Azure VM 作为 IoT Edge 设备，它基于用 C# 编写的示例代码，并以[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门为基础。

本教程介绍如何：

> [!div class="checklist"]
> * 设置相关资源。
> * 在云中构建用于检测玩具卡车的自定义视觉模型，并将其部署到边缘
> * 创建以 http 为扩展名的媒体图并将其部署到自定义视觉模型
> * 运行示例代码。
> * 查看并解释结果。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>建议的读前准备  

在开始之前，建议通读以下文章： 

* [IoT Edge 上的实时视频分析概述](overview.md)
* [Azure 自定义视觉概述](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [IoT Edge 上的实时视频分析术语](terminology.md)
* [媒体图概念](media-graph-concept.md)
* [在不录制视频的情况下进行实时视频分析](analyze-live-video-concept.md)
* [使用自己的模型运行实时视频分析](use-your-model-quickstart.md)
* [教程：开发 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [如何编辑 deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>先决条件

本教程的先决条件如下：

* 开发计算机上的 [Visual Studio Code](https://code.visualstudio.com/)，带有 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 和 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 扩展。

    > [!TIP]
    > 系统可能会提示你安装 Docker。 请忽略该提示。
* 开发计算机上的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer)。
* 请确保你已执行以下操作：
    
    * [设置 Azure 资源](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [设置开发环境](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>观看示例视频

本教程使用[玩具汽车推理视频](https://lvamedia.blob.core.windows.net/public/t2.mkv/)文件来模拟实时流。 可以通过某个应用程序（例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)）来观看视频。 选择“Ctrl+N”，然后粘贴[玩具汽车推理视频](https://lvamedia.blob.core.windows.net/public/t2.mkv)的链接开始播放。 观看视频时，请注意，在 36 秒标记处，玩具卡车出现在视频中。 自定义模型已经过训练，可以检测到这一特定玩具卡车。 在本教程中，你将使用 IoT Edge 上的实时视频分析来检测此类玩具卡车并将关联的推理事件发布到 IoT Edge 中心。

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="自定义视觉概述":::

此图显示了本教程中的信号如何流动。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)节点。 该处理器会限制到达 [HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)节点的视频流的帧速率。
HTTP 扩展节点扮演代理的角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过 REST 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行一个 AI 模型。 在此示例中，该 Edge 模块是使用自定义视觉构建的玩具卡车检测器模型。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](media-graph-concept.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub)。

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>构建并部署自定义视觉玩具检测模型 

顾名思义，你可以使用自定义视觉在云中构建你自己的自定义对象检测器或分类器。 它提供了一个简单易用的直观界面，用于构建可通过容器部署在云或边缘上的自定义视觉模型。 

为了构建玩具卡车检测器，我们建议你遵循此自定义视觉的“通过 Web 门户构建对象探测器”[快速入门文章](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector)。

其他说明：
 
* 对于本教程，请不要使用快速入门文章的[先决条件部分](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)提供的示例图像。 我们利用了某个图像集来构建玩具检测器自定义视觉模型，当快速入门中要求你[选择训练图像](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images)时，建议使用[这些图像](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)。
* 在快速入门的“标记图像”部分中，请确保使用标记“delivery truck”来标记图片中看到的玩具卡车。

在完成后，如果模型已根据你的要求准备就绪，则可以使用“性能”选项卡中的“导出”按钮将其导出到 Docker 容器。请确保选择 Linux 作为容器平台类型。 这是供容器在其上运行的平台。 你下载容器的计算机既可以是 Windows，也可以是 Linux。 以下说明基于下载到 Windows 计算机上的容器文件。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="自定义视觉概述"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            此命令在本地计算机上测试容器，如果图像具有与用来训练模型的送货卡车相同的送货卡车，则输出应如下所示，表示检测到送货卡车的概率为 90.12%。
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>检查示例文件

1. 在 VSCode 中，浏览到“src/edge”。 你将看到创建的 .env 文件以及一些部署模板文件。

    部署模板是指具有某些占位符值的边缘设备的部署清单。 该 .env 文件具有这些变量的值。
1. 接下来，浏览到“src/cloud-to-device-console-app”文件夹。 在这里，你将看到创建的 appsettings.json 文件以及其他一些文件：

    * c2d-console-app.csproj - 这是 VSCode 的项目文件。
    * operations.json - 此文件列出希望程序运行的各种不同操作。
    * Program.cs - 这是执行以下操作的示例程序代码：

        * 加载应用设置。
        * 调用 IoT Edge 上的实时视频分析模块的直接方法来创建拓扑，实例化图形并激活图形。
        * 暂停以在“终端”窗口中检查图形输出，并在“输出”窗口中检查发送到 IoT 中心的事件。
        * 停用图形实例，删除图形实例，并删除图形拓扑。
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>生成并部署部署清单

1. 在 VSCode 中，导航到“src/cloud-to-device-console-app/operations.json”

1. 在 GraphTopologySet 下，确保以下项属实：<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. 在 GraphInstanceSet 下，确保： 
    1. “topologyName”：“InferencingWithHttpExtension”
    1. 在 parameters 数组的顶部，添加以下内容：`{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. 将 rtspUrl 参数值更改为“rtsp://rtspsim:554/media/t2.mkv”    
1. 在 GraphTopologyDelete 下，确保“name”为：“InferencingWithHttpExtension”
1. 右键单击“src/edge/ deployment.customvision.template.json”文件并单击“生成 IoT Edge 部署清单”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="自定义视觉概述":::
  
    这应当会在 src/edge/config 文件夹中创建一个名为“deployment.customvision.amd64.json”的清单文件。
1. 打开“src/edge/ deployment.customvision.template.json”文件并找到 registryCredentials json 块。 在此块中，你会找到 Azure 容器注册表的地址及其用户名和密码。
1. 在命令行上执行命令，将本地自定义视觉容器推送到你的 Azure 容器注册表中。

    1. 通过执行以下命令登录到注册表：
    
        `docker login <address>`
    
        如果要求进行身份验证，请键入用户名和密码。 
        
        > [!NOTE]
        > 密码在命令行上不可见。
    1. 使用以下内容标记你的图像：<br/>`docker tag cvtruck   <address>/cvtruck`
    1. 使用以下内容推送你的图像：<br/>`docker push <address>/cvtruck`

        如果成功，你应该会在命令行上看到“已推送”以及该图像的 SHA。 
    1. 还可以通过在 Azure 门户上检查 Azure 容器注册表进行确认。 此处会显示存储库的名称以及标记。 
1. 单击左下角 AZURE IOT 中心窗格旁边的“更多操作”图标，设置 IoTHub 连接字符串。 可以从 appsettings.json 文件中复制字符串。 （还有一种建议的方法，可确保通过[选择 IoT 中心命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)在 VSCode 中配置正确的 IoT 中心）。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="自定义视觉概述":::
1. 接下来，右键单击“src/edge/config/ deployment.customvision.amd64.json”，再单击“为单个设备创建部署”。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="自定义视觉概述":::
1. 然后，系统会提示“选择 IoT 中心设备”。 从下拉列表中选择 lva-sample-device。
1. 在大约 30 秒内，刷新左下部分的 Azure IoT 中心，应可看到边缘设备已部署以下模块：

    * IoT Edge 上的实时视频分析模块，名称为“lvaEdge”。
    * 一个名为 `rtspsim` 的模块，它模拟 RTSP 服务器，充当实时视频源的来源。
    * 一个名为 `cv` 的模块。顾名思义，这是自定义视觉玩具卡车检测模型，它向图像应用自定义视觉，并返回多个标记类型。 （我们的模型仅使用一个标记（即“delivery truck”）进行了训练）。

## <a name="prepare-for-monitoring-events"></a>准备监视事件

右键单击实时视频分析设备，并选择“开始监视内置事件终结点”。 需要执行此步骤，以在 Visual Studio Code 的“输出”窗口中监视 IoT 中心事件。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="自定义视觉概述":::

## <a name="run-the-sample-program"></a>运行示例程序

如果在浏览器中打开本教程的图形拓扑，你会看到 inferencingUrl 的值已经设置为 http://cv:80/image 。这意味着，在实时视频中检测到玩具卡车后推理服务器会返回结果。

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="自定义视觉概述":::
1. 搜索并启用“显示详细信息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="自定义视觉概述"
              }
            ]
          }
        }
   ```
    
   * 对 GraphInstanceActivate 的调用，它启动图形实例和视频流。
   * 对 GraphInstanceList 的第二个调用，它显示图形实例处于运行状态。
    
1. “终端”窗口中的输出暂停并显示“按 Enter 继续”提示。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到 IoT Edge 模块上的实时视频分析正发送到 IoT 中心的消息。 本教程的以下部分将讨论这些消息。
1. 媒体图将继续运行并打印结果。 RTSP 模拟器不断循环源视频。 若要停止媒体图，请返回“终端”窗口，并选择 Enter。
接下来会执行一系列调用，以清理资源：
    
   * 调用 GraphInstanceDeactivate 以停用图形实例。
   * 调用 GraphInstanceDelete 以删除实例。
   * 调用 GraphTopologyDelete 以删除拓扑。
   * 最后调用 GraphTopologyList，显示列表为空。
    
## <a name="interpret-the-results"></a>解释结果

运行媒体图时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心接收器节点传递到 IoT 中心。 “输出”窗口中显示的消息包含正文部分和 applicationProperties 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

对媒体图进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。 事件类型为 Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished。

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

在此消息中，请注意以下详细信息：

* 消息为诊断事件。 MediaSessionEstablished 指示 RTSP 源节点（使用者）已与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
* 在 applicationProperties 中，subject 指示消息是从媒体图中的 RTSP 源节点生成的。
* 在 applicationProperties 中，eventType 指示此事件是一个诊断事件。
* eventTime 指示事件的发生时间。
* 正文包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从自定义视觉容器接收推理结果，并通过 IoT 中心接收器节点将其作为推理事件发出。

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

请注意上述消息中的以下内容：

* applicationProperties 中的 subject 引用已从其生成消息的 MediaGraph 中的节点。 在本例中，该消息源自 Http 扩展处理器。
* applicationProperties 中的 eventType 指示这是一个分析推理事件。
* eventTime 指示事件的发生时间。
* “body”包含有关分析事件的数据。 在本例中，该事件是推理事件，因此，body 包含一个名为“predictions”的推理数组。
* “predictions”部分包含了在帧中找到玩具送货卡车的预测列表（标记 = delivery truck）。 你可以回忆一下，delivery truck 是你提供给玩具卡车的自定义已训练模型的自定义标记，该模型推理出输入视频中的玩具卡车并使用不同的概率置信度分数对其进行标识。

## <a name="clean-up-resources"></a>清理资源

如果想学习其他教程或快速入门，请保留创建的资源。 如果不想，请转到 Azure 门户，浏览到资源组，选择在本教程中使用的资源组，然后删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
* 使用 AMD64 或 X64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)中的说明进行操作。 

然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)中的说明，将设备注册到 Azure IoT 中心。

