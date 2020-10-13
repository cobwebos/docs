---
title: 使用 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）来分析实时视频
description: 在本教程中，将使用由 Intel 提供的 AI 模型服务器来分析（模拟的） IP 相机中的实时视频源。
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: d03737f43ee719b72860e7ffeff076e3f156cade
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776334"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>教程：使用 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）来分析实时视频 

本教程介绍如何使用 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）来分析（模拟）IP 相机中的实时视频源。 你将了解此推理服务器如何允许你访问用于检测物体（人、车辆或自行车）的模型以及用于车辆分类的模型。 实时视频源中的一部分帧会被发送到此推理服务器，并且结果会被发送到 IoT Edge 中心。

本教程将 Azure VM 用作 IoT Edge 设备，并使用模拟的实时视频流。 它基于用 C# 编写的示例代码，并以[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门为基础。

> [!NOTE]
> 本教程要求使用 x86-64 计算机作为你的 Edge 设备。

## <a name="prerequisites"></a>先决条件

* 包含活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* 如果未完成[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门，请确保完成[设置 Azure 资源](detect-motion-emit-events-quickstart.md#set-up-azure-resources)的步骤。

> [!TIP]
> 在安装 Azure IoT Tools 时，系统可能会提示安装 Docker。 可以忽略该提示。

## <a name="review-the-sample-video"></a>观看示例视频

设置 Azure 资源时，会将停车场的短视频复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

打开一个应用程序，例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)。 选择“Ctrl+N”，然后粘贴[视频](https://lvamedia.blob.core.windows.net/public/lots_015.mkv)的链接以开始播放。 可以看到停车场中车辆的视频片段，大多数是停着的，只有一辆在移动。

在此快速入门中，你将使用 IoT Edge 上的实时视频分析和 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）来检测车辆等物体，或对它们进行分类。 将生成的推理事件发布到 IoT Edge 中心。

## <a name="overview"></a>概述

> [!div class="mx-imgBorder"]
> 概述

此图显示本快速入门中信号的流动方式。 [Edge 模块](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)模拟托管实时流式处理协议 (RTSP) 服务器的 IP 相机。 [RTSP 源](media-graph-concept.md#rtsp-source)节点从该服务器拉取视频源，并将视频帧发送到[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)节点。 该处理器会限制到达 [HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)节点的视频流的帧速率。 

HTTP 扩展节点扮演代理的角色。 它将视频帧转换为指定的图像类型。 然后，它将图像通过 REST 转发到另一个 Edge 模块，该模块在 HTTP 终结点后运行 AI 模型。 在本例中，该 Edge 模块是 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）。 HTTP 扩展处理器节点收集检测结果并将事件发布到 [IoT 中心接收器](media-graph-concept.md#iot-hub-message-sink)节点。 然后该节点将这些事件发送到 [IoT Edge 中心](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)。

在本教程中，将：

1. 创建和部署媒体图，并进行修改。
1. 解释结果。
1. 清理资源。

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>关于 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）

Intel® 分发版 [OpenVINO™ 工具套件](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html)（开放式视觉推理和神经网络优化）是一个免费的软件包，可帮助开发人员和数据科学家提高计算机视觉工作负载、简化深度学习推理和部署，以及实现从边缘到云的跨 Intel® 平台的简单异类执行。 它包括配备了模型优化器和推理引擎的 Intel® 深度学习部署工具套件以及具有超过 40 个经过优化的预训练模型的 [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) 存储库。

为了构建复杂、高性能的实时视频分析解决方案，IoT Edge 模块上的实时视频分析应与功能强大的推理引擎配合使用，以利用边缘的规模。 本教程会将推理请求发送到 [OpenVINO™ Model Server（由 Intel 提供的 AI 扩展）](https://aka.ms/lva-intel-ovms)，这是一种设计用于 IoT Edge 上的实时视频分析的 Edge 模块。 此推理服务器模块包含 OpenVINO™ Model Server (OVMS)，这是一种由 OpenVINO™ 工具套件提供支持的推理服务器，它针对计算机视觉工作负载进行了高度优化，并针对 Intel® 体系结构进行了开发。 已将扩展添加到 OVMS，以便在推理服务器与 IoT Edge 模块上的实时视频分析之间轻松交换视频帧和推理结果，从而使你能够运行任何 OpenVINO™ 工具包支持的模型（可通过修改[代码](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)来自定义推理服务器模块）。 可进一步选择 Intel® 硬件提供的各种加速机制。 这些包括 CPU（Atom、Core、Xeon）、FPGA、VPU。

在此推理服务器的初始版本中，你可以访问以下[模型](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)：

- 车辆检测（推理 URL： http://{module-name}:4000/vehicleDetection）
- 人/车辆/自行车检测（推理 URL： http://{module-name}:4000/personVehicleBikeDetection）
- 车辆分类（推理 URL： http://{module-name}:4000/vehicleClassification）
- 人脸检测（推理 URL： http://{module-name}:4000/faceDetection）

> [!NOTE]
> 通过下载和使用 Edge 模块：OpenVINO™ Model Server（由 Intel 提供的 AI 扩展），以及包含的软件，即表示你同意[许可协议](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)下的条款和条件。
> Intel 致力于尊重人权，避免参与任何侵犯人权的行为。 阅读 [Intel 的全球人权原则](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html)。 Intel 的产品和软件仅适用于不导致或不构成侵犯国际公认人权的应用程序。

## <a name="create-and-deploy-the-media-graph"></a>创建和部署媒体图

### <a name="examine-and-edit-the-sample-files"></a>检查和编辑示例文件

作为先决条件的一部分，请将示例代码下载到一个文件夹中。 按照以下步骤检查并编辑示例文件。

1. 在 Visual Studio Code 中，转到 src/edge。 你可看到 .env 文件以及一些部署模板文件。

    部署模板是指边缘设备的部署清单。 它包含一些占位符值。 该 .env 文件包含这些变量的值。

1. 转到 src/cloud-to-device-console-app 文件夹。 你可在此处看到 appsettings.json 文件和一些其他文件：

    * c2d-console-app.csproj - Visual Studio Code 的项目文件。
    * operations.json - 希望程序运行的操作的列表。
    * Program.cs - 示例程序代码。 此代码：

        * 加载应用设置。
        * 调用 IoT Edge 模块上的实时视频分析公开的直接方法。 可以通过调用模块的[直接方法](direct-methods.md)来使用该模块分析实时视频流。
        * 暂停以检查“终端”窗口中程序的输出，并检查“输出”窗口中模块生成的事件 。
        * 调用直接方法以清理资源。


1. 编辑 operations.json 文件：
    * 将链接更改为图拓扑：

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json"`

    * 在 `GraphInstanceSet` 下，编辑图拓扑的名称，使其与上一个链接中的值匹配：

      `"topologyName" : "InferencingWithOpenVINO"`

    * 在 `GraphTopologyDelete` 下，编辑名称：

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>生成并部署 IoT Edge 部署清单

1. 右键单击 src/edge/ deployment.openvino.template.json 文件，然后选择“生成 IoT Edge 部署清单”。

    ![生成 IoT Edge 部署清单](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    随即将在 src/edge/config 文件夹中创建一个清单文件 deployment.yolov3.amd64.json 。

1. 如果已完成[检测运动并发出事件](detect-motion-emit-events-quickstart.md)快速入门，则跳过此步骤。 

    否则，请在左下角“AZURE IOT 中心”窗格附近选择“更多操作”图标，然后选择“设置 IoT 中心连接字符串”  。 可以从 appsettings.json 文件中复制字符串。 或者，为确保在 Visual Studio Code 中配置了正确的 IoT 中心，请使用[选择 IoT 中心命令](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)。
    
    ![设置 IoT 中心连接字符串](./media/quickstarts/set-iotconnection-string.png)

1. 右键单击 src/edge/config/deployment.openvino.amd64.json，然后选择“为单个设备创建部署”。 

    ![为单个设备创建部署](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. 如果系统提示你选择 IoT 中心设备，请选择“lva-sample-device”。
1. 大约 30 秒后，在该窗口的左下角刷新 Azure IoT 中心。 边缘设备现在显示以下已部署的模块：

    * 实时视频分析模块，名为“lvaEdge”
    * rtspsim 模块，可模拟 RTSP 服务器，充当实时视频源的源
    * openvino 模块，即 OpenVINO™ Model Server（由 Intel 提供的 AI 扩展） 

### <a name="prepare-to-monitor-events"></a>准备监视事件

右键单击实时视频分析设备，并选择“开始监视内置事件终结点”。 需要执行此步骤，以在 Visual Studio Code 的“输出”窗口中监视 IoT 中心事件。 

![开始监视](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>运行示例程序以检测车辆
如果在浏览器中打开本教程的[图形拓扑](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)，你将看到 `inferencingUrl` 的值已经设置为 `http://openvino:4000/vehicleDetection`，这意味着在实时视频中检测到车辆后（若有），推理服务器将返回结果。

1. 在 Visual Studio Code 中，打开“扩展”选项卡（或按 Ctrl+Shift+X），然后搜索“Azure IoT 中心”。
1. 右键单击并选择“扩展设置”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="扩展设置":::
1. 搜索并启用“显示详细消息”。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="扩展设置"
               }
             ]
           }
         }
         ```

     * 对 `GraphInstanceActivate` 的调用，用于启动图形实例和视频流
     * 对 `GraphInstanceList` 的第二次调用，显示图形实例处于运行状态
1. “终端”窗口中的输出会在出现 `Press Enter to continue` 提示时暂停。 暂时不要选择 Enter。 向上滚动，查看调用的直接方法的 JSON 响应有效负载。
1. 切换到 Visual Studio Code 中的“输出”窗口。 可看到 IoT Edge 模块上的实时视频分析正发送到 IoT 中心的消息。 本快速入门中的以下部分将讨论这些消息。
1. 媒体图将继续运行并打印结果。 RTSP 模拟器不断循环源视频。 若要停止媒体图，请返回“终端”窗口，并选择 Enter。 

    接下来会执行一系列调用，以清理资源：
      * 调用 `GraphInstanceDeactivate` 停用图形实例。
      * 调用 `GraphInstanceDelete` 删除该实例。
      * 调用 `GraphTopologyDelete` 删除拓扑。
      * 对 `GraphTopologyList` 的最后一次调用显示该列表为空。

## <a name="interpret-results"></a>解释结果

运行媒体图时，来自 HTTP 扩展处理器节点的结果将通过 IoT 中心接收器节点传递到 IoT 中心。 在“输出”窗口中看到的消息包含 `body` 和 `applicationProperties` 部分。 有关详细信息，请参阅[创建和读取 IoT 中心消息](../../iot-hub/iot-hub-devguide-messages-construct.md)。

在下面的消息中，实时视频分析模块定义了应用程序属性和正文内容。 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 事件

对媒体图进行实例化后，RTSP 源节点尝试连接到在 rtspsim-live555 容器上运行的 RTSP 服务器。 如果连接成功，则打印以下事件。 事件类型为 `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`。

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

在此消息中，请注意以下详细信息：

* 消息为诊断事件。 `MediaSessionEstablished` 指示 RTSP 源节点（使用者）与 RTSP 模拟器连接，并已开始接收（模拟的）实时馈送。
* `applicationProperties` 中的 `subject` 指示消息是从媒体图中的 RTSP 源节点生成的。
* `applicationProperties` 中的 `eventType` 指示此事件是诊断事件。
* `eventTime` 指示事件发生的时间。
* `body` 包含有关诊断事件的数据。 在本例中，数据包含[会话描述协议 (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 详细信息。

### <a name="inference-event"></a>推理事件

HTTP 扩展处理器节点从 OpenVINO™ Model Server（AI 扩展模块）接收推理结果。 然后它通过 IoT 中心接收器节点将结果作为推理事件发出。 

在这些事件中，类型设置为 `entity`，用于指示它是实体，如汽车或卡车等。 `eventTime` 值为检测到对象时的 UTC 时间。 

在以下示例中，检测到两个置信度值高于 0.9 的车辆。

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

在消息中，请注意以下详细信息：

* `applicationProperties` 中的 `subject` 引用生成消息的图形拓扑中的节点。 
* `applicationProperties` 中的 `eventType` 指示此事件是分析事件。
* `eventTime` 值为事件发生的时间。
* `body` 部分包含有关分析事件的数据。 在本例中，该事件是推理事件，因此正文包含 `inferences` 数据。
* `inferences` 部分指示 `type` 为 `entity`。 本部分包含有关实体的其他数据。

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>运行实例程序以检测人、车辆或自行车
若要使用不同的模型，则需要修改图形拓扑和 `operations.json` 文件。

将[图形拓扑](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json)复制到本地文件，如 `C:\TEMP\topology.json`。 打开该副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/personVehicleBikeDetection`。

下一步，在 Visual Studio Code 中，转到 src/cloud-to-device-console-app 文件夹并打开 `operations.json` 文件。 将包含 `topologyUrl` 的行编辑为：

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
现在可以重复上述步骤，使用新拓扑再次运行示例程序。 推理结果将与车辆检测模型的结果相似（在架构中），只是将 `subtype` 设置为 `personVehicleBikeDetection`。

## <a name="run-the-sample-program-to-classify-vehicles"></a>运行示例程序将车辆分类
在 Visual Studio Code 中，打开上一步中 `topology.json` 的本地副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/vehicleClassification`。 如果已运行前面的示例来检测人、车辆或自行车，则无需再次修改 `operations.json` 文件。

现在可以重复上述步骤，使用新拓扑再次运行示例程序。 示例分类结果如下所示。

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>运行示例程序以检测人脸
在 Visual Studio Code 中，打开上一步中 `topology.json` 的本地副本，然后将 `inferencingUrl` 的值编辑为 `http://openvino:4000/faceDetection`。 如果已运行前面的示例来检测人、车辆或自行车，则无需再次修改 `operations.json` 文件。

现在可以重复上述步骤，使用新拓扑再次运行示例程序。 示例检测结果如下所示（注意：上面使用的停车场视频不包含任何可检测的人脸，你应该使用其他视频以试用此模型）。

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>清理资源

如果计划学习其他快速入门或教程，请保留创建的资源。 否则，请转到 Azure 门户，再转到资源组，选择运行本教程所用的资源组，并删除所有资源。

## <a name="next-steps"></a>后续步骤

查看高级用户面临的其他挑战：

* 使用支持 RTSP 的 [IP 相机](https://en.wikipedia.org/wiki/IP_camera)，而不是使用 RTSP 模拟器。 可以在 [ONVIF 符合标准的产品](https://www.onvif.org/conformant-products/)页面上搜索支持 RTSP 的 IP 摄像机。 查找符合配置文件 G、S 或 T 的设备。
* 使用 AMD64 或 X64 Linux 设备，而不是 Azure Linux VM。 此设备必须与 IP 相机位于同一网络中。 可以按照[在 Linux 上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)中的说明进行操作。 然后按照[将首个 IoT Edge 模块部署到虚拟 Linux 设备](../../iot-edge/quickstart-linux.md)中的说明，将设备注册到 Azure IoT 中心。
