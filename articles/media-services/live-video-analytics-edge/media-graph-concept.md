---
title: Media graph 概念-Azure
description: Media graph 允许您定义应从何处捕获媒体，如何处理媒体以及应将结果传递到何处。 本文提供了有关 media graph 概念的详细说明。
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 02e960e917a059afdb0d688c7429d27d8e8a48eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300801"
---
# <a name="media-graph"></a>媒体图

## <a name="suggested-pre-reading"></a>建议的读前准备

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)

## <a name="overview"></a>概述

Media graph 允许您定义应从何处捕获媒体，如何处理媒体以及应将结果传递到何处。 为此，可按所需方式连接组件或节点。 下图提供了媒体图的图形化表示形式。  

![媒体图形的图形表示形式](./media/media-graph/overview.png)

IoT Edge 上的实时视频分析支持不同类型的源、处理器和接收器。

* **源节点**允许将媒体捕获到媒体图中。 从概念上讲，此上下文中的媒体可以是音频流、视频流、数据流，或在单个流中组合在一起的音频、视频和/或数据的流。
* **处理器节点**用于处理 media graph 中的媒体。
* **接收器节点**可将处理结果传递给 media graph 之外的服务和应用。

## <a name="media-graph-topologies-and-instances"></a>Media graph 拓扑和实例 

利用 IoT Edge 上的实时视频分析，你可以通过两个概念来管理 media graph – "graph 拓扑" 和 "graph 实例"。 利用图形拓扑，您可以定义图形的蓝图，并将参数用作值的占位符。 该拓扑定义在 media graph 中使用的节点，以及如何在 media graph 中连接这些节点。 例如，如果你想要从照相机记录源，则需要一个具有接收视频的源节点的图形，以及一个用于写入视频的接收器节点。

拓扑中参数的值是在创建引用该拓扑的关系图实例时指定的。 这使您能够创建多个实例，这些实例引用相同的拓扑，但在拓扑中指定的参数具有不同的值。 在上面的示例中，可以使用参数来表示相机的 IP 地址，并使用所记录视频的名称。 您可以使用该拓扑创建多个图形实例-每个照相机的一个实例，可能每个实例都有特定的 IP 地址和特定名称。

## <a name="media-graph-states"></a>媒体图状态  

媒体图形可以处于以下状态之一：

* 非活动-表示配置了 media graph 但不处于活动状态的状态。
* 激活–正在实例化媒体关系图时的状态（即，不活动和活动之间的转换状态）。
* 活动– media graph 处于活动状态时的状态。 

    > [!NOTE]
    >  Media graph 可以处于活动状态，而不会对其进行数据流（例如，输入视频源脱机）。
* 停用–这是 media graph 转换为非活动状态时的状态。

下图演示了 media graph 状态机。

![Media graph 状态机](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>源、处理器和接收器  

IoT Edge 上的实时视频分析支持在媒体图形中使用以下类型的节点：

### <a name="sources"></a>源 

#### <a name="rtsp-source"></a>RTSP 源 

使用 RTSP 源节点可以从 [RTSP] （服务器）中引入媒体 https://tools.ietf.org/html/rfc2326 。 监视和基于 IP 的照相机以称为 RTSP （实时流式处理协议）的协议传输其数据，这与其他类型的设备（如手机和视频相机）不同。 此协议用于建立和控制服务器（照相机）和客户端之间的媒体会话。 媒体图中的 RTSP 源节点充当客户端，并可与 RTSP 服务器建立会话。 许多设备（例如大多数[IP 摄像机](https://en.wikipedia.org/wiki/IP_camera)）都有内置的 RTSP 服务器。 [ONVIF](https://www.onvif.org/)要求在其[配置文件 G，S &](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf)相容设备的定义中支持 RTSP。 RTSP 源节点要求指定 RTSP URL，以及用于启用经过身份验证的连接的凭据。

#### <a name="iot-hub-message-source"></a>IoT 中心消息源 

与其他[IoT Edge 模块](../../iot-edge/iot-edge-glossary.md#iot-edge-module)一样，IoT Edge 模块上的实时视频分析可以通过[IoT Edge 中心](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)接收消息。 可以从其他模块、在边缘设备上运行的应用或从云中发送这些消息。 此类消息传递（路由）到模块上的[命名输入](../../iot-edge/module-composition.md#sink)。 IoT 中心消息源节点允许此类消息到达媒体图。 然后，可以在 media graph 内部使用这些消息或信号，通常用于激活信号入口（请参阅下面的[信号入口](#signal-gate-processor)）。 

例如，可以有一个 IoT Edge 模块，该模块在打开门时生成一条消息。 可以将该模块中的消息路由到 IoT Edge 集线器，然后可以将该消息路由到 media graph 的 IoT 中心消息源。 在 media graph 中，IoT 中心消息源可以将该事件传递给信号入口处理器，然后将该事件从 RTSP 源转换为文件。 

### <a name="processors"></a>Processors  

#### <a name="motion-detection-processor"></a>运动检测处理器 

通过 "运动检测处理器" 节点，您可以检测现场视频中的动作。 它会检查传入的视频帧，确定视频中是否有运动。 如果检测到动作，它会将视频帧传给下游组件，并发出一个事件。 当检测到动作时，"运动检测处理器" 节点（与其他节点结合使用）可用于触发传入视频的录制。

#### <a name="frame-rate-filter-processor"></a>帧速率筛选器处理器  

使用 "帧速率筛选器处理器" 节点，可以按指定速率从传入视频流中采集帧。 这使你可以减少发送到下流组件（如 HTTP 扩展处理器节点）的帧数，以便进一步处理。

#### <a name="http-extension-processor"></a>HTTP 扩展处理器

使用 "HTTP 扩展处理器" 节点，您可以将自己的 IoT Edge 模块连接到 media graph。 此节点将解码的视频帧作为输入，并将此类帧中继到模块公开的 HTTP REST 终结点。 如果需要，此节点能够向 REST 终结点进行身份验证。 此外，在将视频帧中继到 REST 终结点之前，该节点还提供了用于缩放和编码的内置映像格式化程序。 Scaler 具有要保留、填充或拉伸的图像纵横比的选项。 图像编码器支持 jpeg、png 或 bmp 格式。

#### <a name="signal-gate-processor"></a>信号入口处理器  

通过 "信号入口处理器" 节点，您可以有条件地将媒体从一个节点转发到另一个节点。 它还可以充当缓冲区，以便同步媒体和事件。 典型的用例是在 RTSP 源节点和资产接收器节点之间插入一个信号入口处理器节点，并使用 "运动检测程序处理器" 节点的输出来触发入口。 使用此类 media graph，只需在检测到动作时录制视频。

### <a name="sinks"></a>接收器  

#### <a name="asset-sink"></a>资产接收器  

使用资产接收器节点可以将媒体（视频和/或音频）数据备份到 Azure 媒体服务资产。 媒体图中只能有一个资源接收器节点。 请参阅[资产](terminology.md#asset)部分，了解有关资产的详细信息及其在记录和播放媒体方面的角色。 若要详细了解如何使用此节点的属性，还可以参阅[连续视频录制](continuous-video-recording-concept.md)一文。

#### <a name="file-sink"></a>文件接收器  

使用 "文件接收器" 节点可以将媒体（视频和/或音频）数据写入 IoT Edge 设备的本地文件系统上的某个位置。 Media graph 中只能有一个 file sink 节点，并且它必须是 "信号入口处理器" 节点的下游。 这会将输出文件的持续时间限制为 "信号入口处理器" 节点属性中指定的值。

#### <a name="iot-hub-message-sink"></a>IoT 中心消息接收器  

IoT 中心消息接收器节点可用于将事件发布到 IoT Edge 中心。 然后，IoT Edge 集线器可将数据路由到边缘设备上的其他模块或应用，或路由到云中的 IoT 中心（在部署清单中指定的每个路由）。 IoT 中心消息接收器节点可以接受来自上游处理器（如运动检测处理器节点）或从外部推理服务（通过 HTTP 扩展处理器节点）的事件。

## <a name="rules-on-the-use-of-nodes"></a>使用节点的规则

有关如何在 media graph 中使用不同节点的其他规则，请参阅[配额](quotas-limitations.md#limitations-on-graph-topologies-at-preview)。

## <a name="scenarios"></a>方案

使用上面定义的源、处理器和接收器的组合，可以为涉及实时视频分析的各种方案生成媒体图形。 示例方案包括：

* [连续视频录制](continuous-video-recording-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)
* [在不录制视频的情况下进行实时视频分析](analyze-live-video-concept.md)

## <a name="next-steps"></a>后续步骤

若要查看如何对实时视频源运行动作检测，请参阅[快速入门：使用自己的模型运行实时视频分析](use-your-model-quickstart.md)。
