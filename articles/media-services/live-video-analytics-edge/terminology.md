---
title: IoT Edge 上的实时视频分析术语 - Azure
description: 本文概述了 IoT Edge 上的实时视频分析术语。
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88690606"
---
# <a name="terminology"></a>术语

本文概述了与 [IoT Edge 上的实时视频分析](overview.md)相关的术语。

## <a name="azure-media-services"></a>Azure 媒体服务

Azure 媒体服务是一种云媒体平台，可用于构建媒体解决方案。 有关详细信息，请参阅 [Azure 媒体服务](../latest/media-services-overview.md)文档。

## <a name="asset"></a>资产

[资产](../latest/assets-concept.md) 是 Azure 媒体服务中的实体，它映射到连接到媒体服务帐户的 Azure 存储帐户中的 blob 容器。 与资产关联的所有文件都作为 blob 存储在该容器中，而媒体服务包含与资产相关联的元数据（例如名称、描述、创建时间）。

可以使用 IoT Edge 上的实时视频分析创建资产和/或向现有资产添加数据。 这可以实现连续和基于事件的视频录制和播放（在边缘设备上捕获视频、录制到 Azure 媒体服务，以及通过现有 Azure 媒体服务流式处理功能播放）。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) 是一种与语言无关的高性能远程过程调用 (RPC) 框架。 它通过[协议缓冲区 3](https://developers.google.com/protocol-buffers/docs/proto3) 使用基于会话的结构化架构作为其通信的基础消息交换格式。

## <a name="media-graph"></a>媒体图

通过使用[媒体图](media-graph-concept.md)，你可以定义应从何处捕获媒体、应如何处理媒体以及应将结果交付到何处。 它使你可以定义由源、处理器和接收器节点组成的图形，从而为你提供生成实时视频分析应用程序的能力。 媒体图概念页中详细介绍了媒体图。

## <a name="recording"></a>记录

在安全相机的视频管理系统上下文中，视频录制是指从相机捕获视频并将其存储在文件中以供以后通过移动应用和浏览器应用查看的过程。 视频录制可分类为[连续视频录制](continuous-video-recording-concept.md)和[基于事件的视频录制](event-based-video-recording-concept.md)。 如需详细了解这方面的内容，请参阅[视频录制](video-recording-concept.md)概念页面。

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) 是指实时流式处理协议。 它是一种应用程序级别的协议，用于控制数据与实时属性的传送。 RTSP 提供了一个可扩展的框架，用于实现实时数据（如音频和视频）的受控、按需交付。 

## <a name="streaming"></a>流式处理

如果你从 Netflix、YouTube 等服务观看了移动设备上的视频，应已体验到流式处理视频。 如果你有足够的带宽) ，则在点击 "播放" (后，播放会立即开始，你可以沿视频时间线来回搜索。 流式处理的思路是只传送正在观看的视频部分，并在数据仍从服务器传输到播放客户端时，让查看器开始播放视频。 在 Azure 媒体服务的环境中， [流式处理](https://en.wikipedia.org/wiki/Streaming_media) 是指将媒体从 [Azure 媒体服务](../azure-media-player/azure-media-player-overview.md) 传送到流式处理客户端的过程 (例如 Azure Media Player) 。 你可以使用 Azure 媒体服务通过行业标准的基于 HTTP 的媒体流式处理协议（例如 [HTTP Live Streaming (HLS)](https://developer.apple.com/streaming/) 和 [MPEG-DASH](https://dashif.org/about/)）将视频流式传输到客户端。 Azure Media Player 和 web 播放器（如 [JW Player](https://www.jwplayer.com/)、 [hls.js](https://github.com/video-dev/hls.js/)、 [VideoJS](https://videojs.com/)、 [Google Shaka Player](https://github.com/google/shaka-player)）支持 HLS，或者可以使用 Android 的 [Exoplayer](https://github.com/google/ExoPlayer) 和 iOS [AV 基础](https://developer.apple.com/av-foundation/)在移动应用中以本机方式呈现。 Azure Media Player 同样支持 MPEG-DASH，请参阅[查找此页面上的客户端列表](https://dashif.org/clients/)。 

通过使用[媒体图](#media-graph)将视频录制到 Azure 媒体服务中的资产，可以使用媒体服务流式处理功能通过 HLS 和 DASH 传送视频流。 有关详细信息，请参阅[视频播放](video-playback-concept.md)一文。

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) 是指视频管理系统。 此类系统用于配置和控制 CCTV 相机，捕获并录制其中的视频。 这些系统还提供客户端应用程序来播放录制的视频

## <a name="next-steps"></a>后续步骤

[媒体图](media-graph-concept.md)
