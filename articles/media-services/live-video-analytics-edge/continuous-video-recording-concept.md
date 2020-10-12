---
title: 连续视频录制 - Azure
description: 连续视频录制 (CVR) 是指从视频源连续录制视频的过程。 本主题将讨论什么是 CVR。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 04f09f1968e647c57ba0913a9e7f9e601d045771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566682"
---
# <a name="continuous-video-recording"></a>连续视频录制  

## <a name="suggested-pre-reading"></a>建议的读前准备  

* [媒体图概念](media-graph-concept.md)
* [视频录制概念](video-recording-concept.md)

## <a name="overview"></a>概述

连续视频录制 (CVR) 是指从视频源连续录制视频的过程。 IoT Edge 上的实时视频分析通过包含 RTSP 源节点和资产接收器节点的[媒体图](media-graph-concept.md)从 CCTV 相机全天候连续录制视频。 下图显示了该媒体图的图形表示形式。 可在[此处](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)找到此类媒体图的[图形拓扑](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances)的 JSON 表示形式。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="连续视频录制":::

上面所示的媒体图可以在边缘设备上运行，资产接收器将视频录制到 Azure 媒体服务[资产](terminology.md#asset)。 只要媒体图保持激活状态，视频就会被录制。 由于视频被录制为资产，因此可以使用媒体服务的现有流式传输功能播放视频。 有关更多详细信息，请参阅[播放录制的内容](video-playback-concept.md)。

## <a name="resilient-recording"></a>可复原的录制

IoT Edge 上的实时视频分析支持在不太理想的网络情况下操作，在这种情况下，边缘设备偶尔可能会与云断开连接，或者可能会出现可用带宽下降的情况。 为此，将源中的视频录制到本地缓存中，并定期自动与资产同步。 如果检查[图形拓扑 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)，将看到定义了以下属性：

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

后两个属性与可复原的录制相关（两者也都是资产接收器节点的必需属性）。 localMediaCachePath 属性告知资产接收器在上传到资产之前，使用该文件夹路径来缓存媒体数据。 可以在[此](../../iot-edge/how-to-access-host-storage-from-module.md)文章中了解边缘模块如何利用设备的本地存储。 localMediaCacheMaximumSizeMiB 属性定义资产接收器可用作缓存的磁盘空间（1 MiB = 1024 * 1024 个字节）。 

如果边缘模块长时间断开连接，并且缓存文件夹中存储的内容达到 localMediaCacheMaximumSizeMiB 值，则资产接收器将开始从缓存中丢弃数据，从最早的数据开始。 例如，如果设备在上午 10 点断开连接，而缓存在下午 6 点达到最大限制，则资产接收器将开始删除在上午 10 点录制的数据。 

当恢复网络连接时，资产接收器将从缓存开始上传，也是从最早的数据开始。 在上面的示例中，假设在恢复连接前（例如在下午 6:02）必须从缓存中丢弃 5 分钟的视频，则资产接收器将从上午 10:05 标记开始上传。

如果以后使用[这些](playback-recordings-how-to.md) API 来检查资产，将看到资产中大约从上午 10 点到上午 10:05 之间存在间隙。

## <a name="segmented-recording"></a>分段录制  

如上所述，资产接收器节点会将视频录制到本地缓存中，并定期将视频上传到云。 segmentLength 属性（如上一节所示）有助于控制与将数据写入到正在录制资产的存储帐户相关的写入事务成本。 例如，如果将上传时间从 30 秒增加到 5 分钟，那么存储事务的数量将下降 10 倍 (5*60/30)。

segmentLength 属性确保边缘模块每 segmentLength 秒最多上传一次视频。 此属性的最小值为 30 秒（也是默认值），可以 30 秒递增，最大值为 5 分钟。

> [!NOTE]
> 请参阅[播放录制内容](playback-recordings-how-to.md)一文，了解 segmentLength 对播放的影响。

## <a name="see-also"></a>另请参阅

* [基于事件的视频录制](event-based-video-recording-concept.md)
* [播放录制的内容](video-playback-concept.md)

## <a name="next-steps"></a>后续步骤

[教程：连续视频录制](continuous-video-recording-tutorial.md)
