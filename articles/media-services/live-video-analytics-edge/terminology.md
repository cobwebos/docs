---
title: IoT Edg 术语实时视频分析-Azure
description: 本文概述了有关 IoT Edge 术语的实时视频分析。
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: 58f3d7b54fd0bd19e6bc0a057ef053bb2c74cd97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84816520"
---
# <a name="terminology"></a>术语

本文概述了与[IoT Edge 上的实时视频分析](overview.md)相关的术语。

## <a name="azure-media-services"></a>Azure 媒体服务

Azure 媒体服务是一种可用于构建媒体解决方案的云媒体平台。 可以在[Azure 媒体服务](../latest/media-services-overview.md)文档中了解更多相关信息。

## <a name="asset"></a>资产

[资产](../latest/assets-concept.md)是 Azure 媒体服务中的一个实体，映射到连接到媒体服务帐户的 Azure 存储帐户中的 blob 容器。 与资产关联的所有文件都作为 blob 存储在该容器中，而媒体服务包含与资产相关联的元数据（例如名称、描述、创建时间）。

IoT Edge 上的实时视频分析可以创建资产和/或向现有资产添加数据。 这可以实现连续和基于事件的视频录制和播放（在边缘设备上播放视频捕获、记录到 Azure 媒体服务，以及通过现有 Azure 媒体服务流式处理功能播放）。

## <a name="streaming"></a>流式处理

如果你从 Netflix、YouTube 等服务观看了移动设备上的视频，则已体验到流式处理视频。 播放结束后立即开始播放（如果你有足够的带宽），可以沿视频时间线来回搜索。 使用流式处理时，其思路是只传送正在观看的视频部分，并让查看器在数据仍从服务器传输到播放客户端时开始播放视频。 在 Azure 媒体服务的环境中，[流式处理](https://en.wikipedia.org/wiki/Streaming_media)是指将媒体从[Azure 媒体服务](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview)传送到流式处理客户端的过程（例如 Azure Media Player）。 可以使用 Azure 媒体服务将视频流式传输到使用行业标准的、基于 HTTP 的媒体流式处理协议[（例如 HTTP Live Streaming （HLS）](https://developer.apple.com/streaming/)和[MPEG 破折号](https://dashif.org/about/)）的客户端。 Azure Media Player 和 web 播放器（如[JW Player](https://www.jwplayer.com/)、 [hls.js](https://github.com/video-dev/hls.js/)、 [VideoJS](https://videojs.com/)、 [Google Shaka Player](https://github.com/google/shaka-player)）支持 HLS，或者可以使用 Android 的[Exoplayer](https://github.com/google/ExoPlayer)和 iOS [AV 基础](https://developer.apple.com/av-foundation/)在移动应用中以本机方式呈现。 Azure Media Player 也支持 MPEG-短划线，请[在此页上查找客户端列表](https://dashif.org/clients/)。 

通过使用[media graph](#media-graph)s 将视频录制到 Azure 媒体服务中的资产，可以使用媒体服务流式处理功能在 HLS 和短线中传送视频流。 可在[视频播放](video-playback-concept.md)文章中了解更多相关信息。

## <a name="recording"></a>录制

在用于安全相机的视频管理系统的环境中，视频录制是指从相机捕获视频并将其存储在文件（或文件）中以供以后通过移动和浏览器应用进行查看的过程。 视频录制可分类为[连续视频录制](continuous-video-recording-concept.md)和[基于事件的视频录制](event-based-video-recording-concept.md)。 [视频录制](video-recording-concept.md)概念页中更详细地介绍了这些内容。

## <a name="media-graph"></a>媒体图

[Media graph](media-graph-concept.md)允许您定义媒体的捕获位置、处理方式以及应将结果传递到何处。 它使您可以定义由源、处理器和接收器节点组成的图形，从而为您提供生成实时视频分析应用程序的能力。 Media graph 概念页中详细介绍了媒体关系图。

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326)是指实时流式处理协议。 它是一个应用程序级别的协议，用于控制数据与实时属性的传送。 RTSP 提供一个可扩展的框架，用于实现实时数据（如音频和视频）的受控、按需交付。 

## <a name="vms"></a>机

[Vm](https://en.wikipedia.org/wiki/Video_management_system)指的是视频管理系统。 此类系统用于配置和控制 CCTV 相机、捕获和录制其中的视频。 这些系统还提供客户端应用程序，用于播放录制的视频

## <a name="next-steps"></a>后续步骤

[媒体图](media-graph-concept.md)
