---
title: 基于事件的视频录制-Azure
description: 基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 相关事件可能是由于处理视频信号本身（例如，在动作上检测）或来自独立源（例如打开门）引起的。  本文介绍了一些与基于事件的视频录制相关的用例。
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 0a6f7ca4233c195c7494fc6f63e7dfb5bf654c17
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260740"
---
# <a name="event-based-video-recording"></a>基于事件的视频录制  
 
## <a name="suggested-pre-reading"></a>建议的读前准备  

* [连续视频录制](continuous-video-recording-concept.md)
* [播放录制的内容](video-playback-concept.md)
* [媒体图概念](media-graph-concept.md)

## <a name="overview"></a>概述 

基于事件的视频录制 (EVR) 是指由事件触发的视频录制过程。 出现问题的原因可能是由于处理视频信号本身（例如，在动作上检测）或来自独立源（例如打开门）。 

你可以使用媒体图（由[RTSP 源](media-graph-concept.md#rtsp-source)节点、[资产接收器](media-graph-concept.md#asset-sink)节点和其他节点，如以下用例中所述）从 CCTV 相机将视频录制到媒体服务资产。 你可以将 "[资产接收器](media-graph-concept.md#asset-sink)" 节点配置为在每次发生事件时生成新资产，使对应于每个事件的视频位于其自己的资产中。 你还可以选择使用一个资产来保存所有事件的视频。 

作为资产接收器节点的替代方法，您可以使用[文件接收器](media-graph-concept.md#file-sink)节点将简短的视频片段（与相关事件相关）捕获到边缘设备上的本地文件系统上的指定位置。 

本文介绍了一些与基于事件的视频录制相关的用例。

### <a name="video-recording-based-on-motion-detection"></a>基于动作检测的视频录制  

在此用例中，只能在视频中检测到动作时录制视频剪辑，并在生成此类视频剪辑时发出警报。 这可能与涉及关键基础结构保护的商业安全方案相关。 通过在检测到意外动作时生成警报和录制视频，可以提高人员操作员的工作效率，因为只需在生成警报时执行操作。

下图显示了用于处理此用例的媒体图的图形化表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json)找到此类 media graph 的图形拓扑的 JSON 表示形式。

![基于动作检测的视频录制](./media/event-based-video-recording/motion-detection.png)

在关系图中，RTSP 源节点从相机捕获实时视频源，并将其传送到[运动检测处理器](media-graph-concept.md#motion-detection-processor)节点。 检测实时视频中的动作时，运动检测处理器节点会生成一个事件，该事件将进入 "[信号入口处理器](media-graph-concept.md#signal-gate-processor)" 节点以及 "IoT 中心" 消息接收器节点。 后一节点会将事件发送到 IoT Edge 中心，然后将这些事件路由到其他目标来触发警报。 

"运动检测器" 节点中的事件将触发 "信号入口处理器" 节点，并允许来自 RTSP 源节点的实时视频源流过 "资产接收器" 节点。 如果没有后续的此类动作检测事件，则入口将在配置的时间长度后关闭。 这会确定所录制视频的持续时间。

### <a name="video-recording-based-on-events-from-other-sources"></a>基于来自其他源的事件的视频录制  

在此用例中，可以使用来自另一个 IoT 传感器的信号来触发视频的录制。 下图显示了用于处理此用例的媒体图的图形化表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json)找到此类 media graph 的图形拓扑的 JSON 表示形式。

![基于来自其他源的事件的视频录制](./media/event-based-video-recording/other-sources.png)

在关系图中，外部传感器将事件发送到 IoT Edge 中心。 然后通过[IoT 中心消息源](media-graph-concept.md#iot-hub-message-source)节点将事件路由到信号入口处理器节点。 "信号入口处理器" 节点的行为与以前用例相同，它将打开，并让实时视频源从 RTSP 源节点流过到外部事件触发的文件接收器节点（或资产接收器节点）。 

如果使用文件接收器节点，则会将视频记录到边缘设备上的本地文件系统中。 否则，如果使用资产接收器节点，则会将视频记录到资产中。

### <a name="video-recording-based-on-an-external-inferencing-module"></a>基于外部推断模块的视频录制 

在此用例中，可以根据外部逻辑系统中的信号来录制视频剪辑。 例如，在高速公路上流量的视频源中检测到卡车时，这种用例可能会记录视频剪辑。 下图显示了用于处理此用例的媒体图的图形化表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)找到此类 media graph 的图形拓扑的 JSON 表示形式。

![基于外部推断模块的视频录制](./media/event-based-video-recording/external-inferencing-module.png)

在关系图中，RTSP 源节点从相机捕获实时视频源，并将其传递给两个分支：一个具有 "[信号入口处理器](media-graph-concept.md#signal-gate-processor)" 节点，另一个使用[HTTP 扩展](media-graph-concept.md)节点将数据发送到外部逻辑模块。 "HTTP 扩展" 节点允许 media graph 以 JPEG、BMP 或 PNG 格式将图像帧发送到外部推理服务（而不是 REST）。 此信号路径通常仅支持低帧速率（<5fps）。 可以使用 "[帧速率筛选器处理器](media-graph-concept.md#frame-rate-filter-processor)" 节点降低视频的帧速率，使其转到 HTTP 扩展节点。

来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 集线器，外部逻辑模块可以进一步对这些结果进行处理。 例如，如果推理服务能够检测车辆，则逻辑模块可能会查找特定车辆，如总线或卡车。 逻辑模块检测到相关对象时，它可以通过将事件通过 IoT Edge 集线器发送到关系图中的 IoT 中心消息源节点来触发 "信号入口处理器" 节点。 显示来自信号入口的输出，以跳到文件接收器节点或资产接收器节点。 在前一种情况下，会将视频记录到边缘设备上的本地文件系统中。 对于后一种情况，会将视频记录到资产中。

此示例的增强功能是在帧速率筛选器处理器节点之前使用运动检测程序处理器。 这会减少推理服务上的负载，如夜间，当高速公路上没有车辆时，可能会有很长一段时间。 

## <a name="next-steps"></a>后续步骤

[教程：基于事件的视频录制](event-based-video-recording-tutorial.md)
