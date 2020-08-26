---
title: 无需录制即可分析实时视频-Azure
description: 媒体图可用于仅从实时视频流中提取分析，而无需将其记录到边缘或云中。 本文讨论了这一概念。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 29e00b9c04a652771ca150e2a45e980d20f8bc1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260180"
---
# <a name="analyzing-live-video-without-any-recording"></a>无需录制即可分析实时视频

## <a name="suggested-pre-reading"></a>建议的读前准备 

* [媒体图概念](media-graph-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="overview"></a>概述  

您可以使用 media graph 来分析实时视频，而无需将视频的任何部分记录到文件或资产。 如下所示的媒体图与[基于事件的视频录制一](event-based-video-recording-concept.md)文中的关系图类似，但没有资产接收器节点或文件接收器节点。

### <a name="motion-detection"></a>动作检测

下面显示的媒体图包含一个[RTSP 源](media-graph-concept.md#rtsp-source)节点、一个[动作检测处理器](media-graph-concept.md#motion-detection-processor)节点和一个[IoT 中心消息接收器](media-graph-concept.md#iot-hub-message-sink)节点。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json)找到此类 media graph 的图形拓扑的 JSON 表示形式。 此图可让你检测传入实时视频流中的运动，并通过 IoT 中心消息接收器节点将动作事件中继到其他应用和服务。 外部应用或服务可以触发警报，或将通知发送给相应的人员。

![基于运动检测的实时视频分析](./media/analyze-live-video/motion-detection.png)

### <a name="analyzing-video-using-a-custom-vision-model"></a>使用自定义视觉模型分析视频 

下面显示的媒体图可让你使用打包在不同模块中的自定义视觉模型来分析实时视频流。 可在[此处](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json)找到此类 media graph 的图形拓扑的 JSON 表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)查看将模型包装为作为推理服务运行 IoT Edge 模块的一些示例。

![基于外部推断模块的实时视频分析](./media/analyze-live-video/external-inferencing-module.png)

在此媒体图中，"帧速率筛选器处理器" 节点会在将传入的实时视频流发送到[HTTP 扩展处理器](media-graph-concept.md#http-extension-processor)节点之前降低传入实时视频流的帧速率，该节点将图像帧（JPEG、BMP 或 PNG 格式）发送到外部推理服务而不是 REST。 来自外部推理服务的结果由 HTTP 扩展节点检索，并通过 IoT 中心消息接收器节点中继到 IoT Edge 集线器。 这种类型的媒体图可用于构建各种方案的解决方案，如了解相交处车辆的时序分布、了解零售商店中的使用者流量模式等。

此示例的增强功能是在帧速率筛选器处理器节点之前使用运动检测程序处理器。 这将减少推理服务上的负载，因为它仅在视频中有运动活动时使用。

![基于通过 external 推断模块的动画检测帧的实时视频分析](./media/analyze-live-video/motion-detected-frames.png)

## <a name="next-steps"></a>后续步骤

[连续视频录制](continuous-video-recording-concept.md)
