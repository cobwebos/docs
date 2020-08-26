---
title: 教程 - 使用 Azure IoT Central 视频分析 - 对象和运动检测应用程序模板监视视频
description: 本教程介绍如何使用视频分析 - 对象和运动检测应用程序模板中的面板来管理相机和监视视频。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 30e123b24a5d2c9e45df6ee6dc6debfb88b920f3
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037861"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>教程：监视和管理视频分析 - 对象和运动检测应用程序

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 将对象和运动检测相机添加到 IoT Central 应用程序。
> * 管理视频流，并在检测到感兴趣的事件时播放它们。

## <a name="prerequisites"></a>先决条件

在开始之前，应完成：

* [在 Azure IoT Central 中创建实时视频分析应用程序](./tutorial-video-analytics-create-app.md)教程。
* 之前的[创建用于视频分析的 IoT Edge 实例 (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 或当前的[创建用于视频分析的 IoT Edge 实例 (Linux VM)](tutorial-video-analytics-iot-edge-nuc.md) 教程。

你应在本地计算机上安装 [Docker](https://www.docker.com/products/docker-desktop) 以运行视频查看器应用程序。

## <a name="add-an-object-detection-camera"></a>添加对象检测相机

在 IoT Central 应用程序中，导航到之前创建的“LVA Gateway 001”设备。 然后选择“命令”选项卡。

将下表中的值用作“添加相机请求”命令的参数值。 表中所示的值假定你正在使用 Azure VM 中的模拟相机，如果你使用的是真实相机，请适当地调整这些值：

| 字段| 说明| 示例值|
|---------|---------|---------|
| 相机 ID      | 用于预配的设备 ID | camera-003 |
| 相机名称    | 友好名称           | 对象检测相机 |
| RTSP URL       | 流的地址   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP 用户名  |                         | user    |
| RTSP 密码  |                         | password    |
| 检测类型 | 下拉列表                | 对象检测       |

选择“运行”以添加相机设备：

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="添加相机":::

> [!NOTE]
> LVA Edge 对象检测器设备模板已存在于应用程序。

## <a name="add-a-motion-detection-camera-optional"></a>添加运动检测相机（可选）

如果有两个连接到 IoT Edge 网关设备的相机，请重复上述步骤，将运动检测相机添加到应用程序。 对“相机 ID”、“相机名称”和“RTSP URL”参数使用不同的值  。

## <a name="view-the-downstream-devices"></a>查看下游设备

选择 LVA Gateway 001 设备的“下游设备”选项卡，以查看刚添加的相机设备 ：

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="检查":::

应用程序的“设备”页面上的列表中也会显示相机设备。

## <a name="configure-and-manage-the-camera"></a>配置和管理相机

导航到 camera-003 设备，然后选择“管理”选项卡 。

使用默认值，或在需要自定义设备属性时进行修改：

**摄像机设置**

| 属性 | 说明 | 建议的值 |
|-|-|-|
| 视频播放主机 | Azure Media Player 查看器的主机 | http://localhost:8094 |

**LVA 操作和诊断**

| 属性 | 说明 | 建议的值 |
|-|-|-|
| 自动启动 | LVA 网关重启时启动对象检测 | 已选中 |
| 调试遥测 | 事件跟踪 | 可选 |
|推理超时（秒）| 用于确定推理是否已停止的时间量 | 20 |

**AI 对象检测**

| 属性 | 说明 | 建议的值 |
|-|-|-|
| 最大推理捕获时间（秒） | 最大捕获时间 | 15 |
| 检测类 | 带有检测标记以逗号分隔的字符串。 有关详细信息，请参阅[支持的标记列表](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt) | 卡车、客车 |
| 置信度阈值 | 确定对象检测是否有效的限定百分比 | 70 |
| 推理帧采样速率 (fps) | [说明如下] | 2 |

选择“保存” 。

数秒钟后，你会看到每个设置的“已接受”确认消息：

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="对象检测":::

## <a name="start-lva-processing"></a>启动 LVA 处理

导航到 camera-003 设备，然后选择“命令”选项卡 。

运行“启动 LVA 处理”命令。

命令完成后，查看命令历史记录，确保没有错误：

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="“启动 LVA 处理”命令":::

## <a name="monitor-the-cameras"></a>监视相机

导航到 camera-003 设备，然后选择“面板”选项卡 ：

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="相机面板":::

“检测计数”磁贴显示在一秒钟的检测间隔内对每个所选检测类别对象的平均检测计数。

“检测类”饼图显示每种类类型所占的检测百分比。

“推理事件视频”是一系列指向 Azure 媒体服务中包含检测的资产的链接。 该链接使用下一部分中所述的主机播放机。

## <a name="start-the-streaming-endpoint"></a>启动流式处理终结点

启动媒体服务终结点，使客户端媒体播放器应用程序能够流式传输录制的视频：

* 在 Azure 门户中，导航到 lva-rg 资源组。
* 打开“流式处理终结点”资源。
* 在“流式处理终结点详细信息”页上，选择“开始” 。 你将看到一条警告，表示终结点启动时计费开始。

## <a name="view-stored-video"></a>查看存储的视频

观看相机并对可疑图像做出反应的时代已经过去了。 借助自动事件标记功能和指向经推理检测后的存储视频的直接链接，安全操作员可以在列表中找到感兴趣的事件，然后点击链接查看视频。

可以使用 [AMP 视频播放器](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer)查看 Azure 媒体服务帐户中存储的视频。

IoT Central 应用程序将视频存储在 Azure 媒体服务中，在其中可以对该视频进行流式传输。 需要视频播放器才能播放 Azure 媒体服务中存储的视频。

请确保 Docker 正在本地计算机上运行。

打开命令提示符，然后使用以下命令在本地计算机上的 Docker 容器中运行视频播放器。 将命令中的占位符替换为你之前在 scratchpad.txt 文件中记下的值。 为媒体服务帐户创建服务主体时，你记下了 `amsAadClientId`、`amsAadSecret`、`amsAadTenantId`、`amsSubscriptionId` 和 `amsAccountName`：

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker 参数 | AMS API 访问 (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

在应用程序中，导航到“监视器”面板。 然后，在“推理事件视频”磁贴上单击其中一个捕获对象检测超链接。 本地视频播放器显示的页面上会出现视频：

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="视频片段":::

## <a name="change-the-simulated-devices-in-application-dashboard"></a>在应用程序面板中更改模拟设备

应用程序面板最初填充的是 IoT Central 模拟设备生成的遥测数据和属性。 若要将磁贴配置为真实相机或 Live555 模拟器的遥测数据，请按以下步骤操作：

1. 导航到“真实相机显示器”面板。
1. 选择“编辑”  。
1. 在“推理计数”磁贴中，选择“配置”图标。
1. 在“配置图表”部分中，选择“LVA Edge 对象探测器”设备组中的一个或多个真实相机 。
1. 选择 `AI Inference Interface/Inference Count` 遥测数据字段。
1. 选择“更新”。

   

1. 对以下磁贴重复上述步骤：
    1. “检测”饼图使用 `AI Inference Interface/Inference/entity/tag/value` 遥测数据类型。
    1. “推理”使用 `AI Inference Interface/Inference/entity/tag/value` 上一个已知值。
    1. “置信度(%)”使用 `AI Inference Interface/Inference/entity/tag/confidence` 上一个已知值。
    1. “快照”使用以图像形式显示的 `AI Inference Interface/Inference Image`。
    1. “推理事件视频”使用以链接形式显示的 `AI Inference Interface/Inference Event Video`。
1. 选择“保存” 。

“真实相机显示器”面板现在显示真实相机设备的值：

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="真实相机应用程序面板":::

## <a name="pause-processing"></a>暂停处理

可以在应用程序中暂停实时视频分析处理：

1. 在应用程序中，导航到对象检测相机的“命令”页面。 然后，运行“停止 LVA 处理”命令。

1. 停止媒体服务帐户的流式处理终结点：
    * 在 Azure 门户中，导航到 lva-rg 资源组。
    * 单击“流式处理终结点”资源。
    * 在“流式处理终结点详细信息”页上，选择“停止” 。

## <a name="tidy-up"></a>整理

如果应用程序使用完毕，可以删除创建的所有资源，如下所示：

1. 在 IoT Central 应用程序中，请导航至“管理”部分中的“你的应用程序”页面 。 然后选择“删除”。
1. 在 Azure 门户中，删除“lva-rg”资源组。
1. 在本地计算机上，停止“amp-viewer”Docker 容器。

## <a name="next-steps"></a>后续步骤

你现在已经了解了如何将相机添加到 IoT Central 应用程序，以及如何配置它们以进行对象和运动检测。

若要了解如何自定义 IoT Edge 模块的源代码，请参阅以下内容：

> [!div class="nextstepaction"]
> [修改和生成实时视频分析网关模块](./tutorial-video-analytics-build-module.md)
