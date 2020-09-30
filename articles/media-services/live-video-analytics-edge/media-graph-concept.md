---
title: 媒体图概念 - Azure
description: 通过使用媒体图，你可以定义应从何处捕获媒体、应如何处理媒体以及应将结果交付到何处。 本文提供了媒体图概念的详细说明。
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 7def82160547b759c7ab4c40c681052747261920
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567072"
---
# <a name="media-graph"></a>媒体图

## <a name="suggested-pre-reading"></a>建议的读前准备

* [IoT Edge 上的实时视频分析概述](overview.md)
* [IoT Edge 上的实时视频分析术语](terminology.md)

## <a name="overview"></a>概述

通过使用媒体图，你可以定义应从何处捕获媒体、应如何处理媒体以及应将结果交付到何处。 可以采用所需方式连接组件或节点来进行定义。 下图提供了媒体图的图形表示形式。  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="媒体图":::

IoT Edge 上的实时视频分析支持不同类型的源、处理器和接收器。

* **源节点**支持将媒体捕获到媒体图中。 从概念上讲，此上下文中的媒体可能是音频流、视频流、数据流或将音频、视频和/或数据组合在单个流中的流。
* **处理器节点**支持处理媒体图中的媒体。
* **接收器节点**支持将处理结果交付到媒体图之外的服务和应用。

## <a name="media-graph-topologies-and-instances"></a>媒体图拓扑和实例 

通过 IoT Edge 上的实时视频分析，你可以通过两个概念来管理媒体图 -“图形拓扑”和“图形实例”。 通过图形拓扑，你可以定义图形的蓝图，其中参数作为值的占位符。 拓扑定义媒体图中使用的节点，以及它们在媒体图中的连接方式。 例如，如果要从照相机录制源，则需要包含接收视频的源节点和写入视频的接收器节点的图形。

拓扑中参数的值是在你创建引用拓扑的图形实例时指定的。 这使你能够创建多个引用相同拓扑的实例，但拓扑中指定的参数的值却不同。 在上面的示例中，可以使用参数来表示照相机的 IP 地址以及录制的视频的名称。 可以使用该拓扑来创建许多图形实例 - 生成内容中每个照相机都对应一个实例，可能各自具有特定的 IP 地址和特定名称。

## <a name="media-graph-states"></a>媒体图状态  

以下状态图显示了图形拓扑和图形实例的生命周期。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="媒体图":::

首先[创建图形拓扑](direct-methods.md#graphtopologyset)。 然后对每个要使用此拓扑处理的实时视频源，[创建图形实例](direct-methods.md#graphinstanceset)。 

图形实例将处于 `Inactive`（空闲）状态。

准备好将实时视频源发送到图形实例时，[激活](direct-methods.md#graphinstanceactivate)该实例。 图形实例将短暂为过渡的 `Activating` 状态，如果成功，则进入 `Active` 状态。 在 `Active` 状态下，将会处理媒体（如果图形实例接收了输入数据）。

> [!NOTE]
>  图形实例可以在没有数据流通过它时处于活动状态（例如相机离线）。
> 当图形实例处于活动状态时，将对你的 Azure 订阅进行计费。

如果有其他实时视频源要处理，则可以重复为同一拓扑创建和激活其他图形实例。

处理完实时视频源后，可以[停用](direct-methods.md#graphinstancedeactivate)该图形实例。 图形实例将短暂为过渡的 `Deactivating` 状态，刷新其包含的所有数据，然后返回到 `Inactive` 状态。

仅当图形实例处于 `Inactive` 状态时，才能将其[删除](direct-methods.md#graphinstancedelete)。

删除引用特定图形拓扑的所有图形实例后，可以[删除图形拓扑](direct-methods.md#graphtopologydelete)。


## <a name="sources-processors-and-sinks"></a>源、处理器和接收器  

IoT Edge 上的实时视频分析支持媒体图中以下类型的节点：

### <a name="sources"></a>源 

#### <a name="rtsp-source"></a>RTSP 源 

使用 RTSP 源节点可以从 [RTSP](https://tools.ietf.org/html/rfc2326 server)中引入媒体。 监控和基于 IP 的照相机使用名为 RTSP（实时流式处理协议）的协议传输它们的数据，这不同于其他类型的设备（例如手机和摄像机）。 此协议用于建立和控制服务器（照相机）和客户端之间的媒体会话。 媒体图中的 RTSP 源节点充当客户端，可以与 RTSP 服务器建立会话。 许多设备（例如大多数 [IP 照相机](https://en.wikipedia.org/wiki/IP_camera)）有内置的 RTSP 服务器。 [ONVIF](https://www.onvif.org/) 强制要求在 [Profile G、S 和 T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) 兼容设备的定义中支持 RTSP。 RTSP 源节点要求你指定 RTSP URL 以及凭据才能启用经过身份验证的连接。

#### <a name="iot-hub-message-source"></a>IoT 中心消息源 

与其他 [IoT Edge 模块](../../iot-edge/iot-edge-glossary.md#iot-edge-module)一样，IoT Edge 上的实时视频分析模块可通过 [IoT Edge 中心](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)接收消息。 这些消息可能发送自其他模块或在 Edge 设备上运行的应用，或者发送自云。 此类消息会传递（路由）到模块上[已命名的输出](../../iot-edge/module-composition.md#sink)。 IoT 中心消息源节点使此类消息能够到达媒体图。 这些消息或信号随后可以在媒体图内部使用，通常用于激活信号门（请参阅下面的[信号门](#signal-gate-processor)）。 

例如，你可以有一个在门打开时生成消息的 IoT Edge 模块。 来自该模块的消息可以路由到 IoT Edge 中心，然后可以从该中心路由到媒体图的 IoT 中心消息源。 在媒体图中，IoT 中心消息源可以将事件传递给信号门处理器，该处理器随后可以打开从 RTSP 源到文件的视频录制。 

### <a name="processors"></a>Processors  

#### <a name="motion-detection-processor"></a>运动检测处理器 

通过运动检测处理器节点，你可以在实时视频中检测运动。 它会检查传入的视频帧并确定视频中是否有移动。 如果检测到运动，它会将视频帧传递到下游组件，并发出事件。 运动检测处理器节点（连同其他节点）可用于在检测到运动时触发传入视频的录制。

#### <a name="frame-rate-filter-processor"></a>帧速率筛选器处理器  

通过帧速率筛选器处理器节点，你可以采用指定的速率从传入的视频流中采样帧。 这使你能够减少发送到下游组件（例如 HTTP 扩展处理器节点）以进行进一步处理的帧数。

#### <a name="http-extension-processor"></a>HTTP 扩展处理器

通过 HTTP 扩展处理器节点，你可以将自己的 IoT Edge 模块连接到媒体图。 此节点以解码的视频帧作为输入，并将此类帧中继到模块公开的 HTTP REST 终结点。 如果需要，此节点能够使用 REST 终结点进行身份验证。 此外，此节点具有内置的图像格式化程序，用于在视频帧中继到 REST 终结点之前对它们进行缩放和编码。 缩放程序可以对图像纵横比进行保留、填充或拉伸。 图像编码器支持 JPEG、PNG 或 BMP 格式。 在 [此处](media-graph-extension-concept.md#http-extension-processor)了解有关处理器的详细信息。

#### <a name="grpc-extension-processor"></a>gRPC 扩展处理器

此 gRPC 扩展处理器节点以解码的视频帧作为输入，并将此类帧中继到模块公开的 [gRPC](terminology.md#grpc) 终结点。 节点支持使用 [共享内存](https://en.wikipedia.org/wiki/Shared_memory) 传输数据或直接将内容嵌入到 gRPC 消息的正文中。 此外，此节点具有内置的图像格式化程序，用于在视频帧中继到 gRPC 终结点之前对它们进行缩放和编码。 缩放程序可以对图像纵横比进行保留、填充或拉伸。 图像编码器支持 jpeg、png 或 bmp 格式。 在 [此处](media-graph-extension-concept.md#grpc-extension-processor)了解有关处理器的详细信息。

#### <a name="signal-gate-processor"></a>信号门处理器  

通过信号门处理器节点，你可以有条件地将媒体从一个节点转发到另一个节点。 它还充当缓冲区，支持媒体和事件的同步。 典型的用例是将信号门处理器节点插入 RTSP 源节点和资产接收器节点之间，并使用运动检测器处理器节点的输出触发门。 使用这样的媒体图时，你只有在检测到运动时才录制视频。

### <a name="sinks"></a>接收器  

#### <a name="asset-sink"></a>资产接收器  

通过资产接收器节点，你可以将媒体（视频和/或音频）数据写入 Azure 媒体服务资产。 媒体图中只能有一个资产接收器节点。 若要详细了解资产以及它们在媒体录制和播放方面的作用，请参阅[资产](terminology.md#asset)部分。 此外，还可以查看[连续视频录制](continuous-video-recording-concept.md)一文，了解如何使用此节点的属性的详细信息。

#### <a name="file-sink"></a>文件接收器  

通过文件接收器节点，你可以将媒体（视频和/或音频）数据写入 IoT Edge 设备本地文件系统上的位置。 媒体图中只能有一个文件接收器节点，并且它必须是信号门处理器节点的下游。 这会将输出文件的持续时间限制为信号门处理器节点属性中指定的值。

#### <a name="iot-hub-message-sink"></a>IoT 中心消息接收器  

通过 IoT 中心消息接收器节点，你可以将事件发布到 IoT Edge 中心。 IoT Edge 中心随后可以将数据路由到其他模块或边缘设备上的应用，或路由到云中的 IoT 中心（根据部署清单中指定的路由）。 IoT 中心消息接收器节点可以接受来自上游处理器（例如运动检测处理器节点）的事件，或通过 HTTP 扩展处理器节点接受来自外部推理服务的事件。

## <a name="rules-on-the-use-of-nodes"></a>使用节点的规则

有关如何在媒体图中使用不同节点的其他规则，请参阅[图形拓扑的限制](quotas-limitations.md#limitations-on-graph-topologies-at-preview)。

## <a name="scenarios"></a>方案

使用上面定义的源、处理器和接收器组合，你可以为涉及实时视频分析的各种方案构建媒体图。 示例方案包括：

* [连续视频录制](continuous-video-recording-concept.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)
* [在不录制视频的情况下进行实时视频分析](analyze-live-video-concept.md)

## <a name="next-steps"></a>后续步骤

若要了解如何在实时视频源上运行运动检测，请参阅[快速入门：使用自己的模型运行实时视频分析](use-your-model-quickstart.md)。
