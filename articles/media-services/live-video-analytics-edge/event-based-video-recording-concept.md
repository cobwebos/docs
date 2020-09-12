---
title: 基于事件的视频录制 - Azure
description: 基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能源于视频信号本身的处理（例如动作检测），也可能来自独立源（例如开门）。  本文介绍了一些与基于事件的视频录制有关的用例。
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f3efd2b9be41928ab4721d6db4aa84c0f1f57e2f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568470"
---
# <a name="event-based-video-recording"></a>基于事件的视频录制  
 
## <a name="suggested-pre-reading"></a>建议的读前准备  

* [连续视频录制](continuous-video-recording-concept.md)
* [播放录制的内容](video-playback-concept.md)
* [媒体图概念](media-graph-concept.md)

## <a name="overview"></a>概述 

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能源于视频信号本身的处理（例如动作检测），也可能来自独立源（例如开门）。 

可以使用由 [RTSP 源](media-graph-concept.md#rtsp-source)节点、[资产接收器](media-graph-concept.md#asset-sink)节点和其他节点组成的媒体图，将 CCTV 相机中的视频（由事件触发）录制到媒体服务资产中，如以下用例所述。 可以将[资产接收器](media-graph-concept.md#asset-sink)节点配置为在每次发生事件时生成新资产，使与每个事件相对应的视频位于其自己的资产中。 也可以选择使用一种资产来保存所有事件的视频。 

作为资产接收器节点的替代方法，你可以使用[文件接收器](media-graph-concept.md#file-sink)节点将视频的短片段（与感兴趣的事件相关）捕获到边缘设备的本地文件系统上的指定位置。 

本文介绍了一些与基于事件的视频录制有关的用例。

### <a name="video-recording-based-on-motion-detection"></a>基于动作检测的视频录制  

在此用例中，只能在视频中检测到动作时录制视频片段，并在生成此类视频片段时收到警报。 这可能与涉及关键基础结构保护的商业安全场景相关。 通过在检测到意外动作时生成警报并录制视频，可以提高人类操作员的效率，因为只有在生成警报时才需要执行操作。

下图显示了用于处理此用例的媒体图的图形表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)找到此类媒体图的图形拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="基于动作检测的视频录制":::

在该图中，RTSP 源节点捕获了来自相机的实时视频源，并将其传递到[动作检测处理器](media-graph-concept.md#motion-detection-processor)节点。 在检测到实时视频中的动作后，动作检测处理器节点将生成事件，该事件将到达[信号入口处理器](media-graph-concept.md#signal-gate-processor)节点以及 IoT 中心消息接收器节点。 后一个节点将事件发送到 IoT Edge 中心，从那里可以将事件路由到其他目标以触发警报。 

来自动作检测器节点的事件将触发信号入口处理器节点，并将使来自 RTSP 源节点的实时视频源流到资产接收器节点。 如果没有后续此类动作检测事件，入口将在配置的时间范围之后关闭。 这决定了录制视频的持续时间。

### <a name="video-recording-based-on-events-from-other-sources"></a>基于来自其他源的事件的视频录制  

在此用例中，可以使用来自另一个 IoT 传感器的信号触发视频录制。 下图显示了用于处理此用例的媒体图的图形表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)找到此类媒体图的图形拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="基于来自其他源的事件的视频录制":::

在该图中，外部传感器将事件发送到 IoT Edge 中心。 然后，事件通过 [IoT 中心消息源](media-graph-concept.md#iot-hub-message-source)节点路由到信号入口处理器节点。 信号入口处理器节点的行为与之前的用例相同 - 当外部事件触发它时，它将打开并让实时视频源从 RTSP 源节点流到文件接收器节点（或资产接收器节点）。 

如果你使用文件接收器节点，则视频将录制到边缘设备上的本地文件系统中。 否则，如果你使用资产接收器节点，则视频将录制到资产中。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>基于外部推理模块的视频录制 

在此用例中，你可以根据来自外部逻辑系统的信号来录制视频片段。 此类用例的一个示例是，只有在公路上的交通视频源中检测到卡车时才录制视频片段。 下图显示了用于处理此用例的媒体图的图形表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)找到此类媒体图的图形拓扑的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="基于外部推理模块的视频录制":::

在该图中，RTSP 源节点捕获了来自相机的实时视频源，并将其传递到两个分支：一个具有[信号入口处理器](media-graph-concept.md#signal-gate-processor)节点，另一个使用 [HTTP 扩展](media-graph-concept.md)节点将数据发送到外部逻辑模块。 HTTP 扩展节点允许媒体图通过 REST 将图像帧（以 JPEG、BMP 或 PNG 格式）发送到外部推理服务。 此信号路径通常仅支持低帧速率（小于 5fps）。 可以使用[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)节点来降低发送到 HTTP 扩展节点的视频的帧速率。

来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 中心，外部逻辑模块可以在此处对这些结果进行进一步处理。 例如，如果推理服务能够检测车辆，则逻辑模块可以查找特定的车辆（例如公共汽车或卡车）。 当逻辑模块检测到相关对象时，它可以通过将事件通过 IoT Edge 中心发送到图形中的 IoT 中心消息源节点来触发信号入口处理器节点。 来自信号入口的输出显示为到达文件接收器节点或资产接收器节点。 如果是到达文件接收器节点，视频将录制到边缘设备的本地文件系统中。 如果是到达资产接收器节点，视频将录制到资产中。

增强此示例的一种方法是，在抵达帧速率筛选器处理器节点之前使用动作检测器处理器。 这将减少推理服务的负担，例如夜间公路上可能会有很长一段时间没有车辆经过。 

## <a name="next-steps"></a>后续步骤

[教程：基于事件的视频录制](event-based-video-recording-tutorial.md)
