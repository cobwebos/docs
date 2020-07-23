---
title: 连续视频录制-Azure
description: 连续视频录制（CVR）是指连续记录视频源的视频。 本主题讨论什么是 CVR。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9a785125d4cfb2324224f4676e1d429342ec325c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260620"
---
# <a name="continuous-video-recording"></a>连续视频录制  

## <a name="suggested-pre-reading"></a>建议的读前准备  

* [媒体图概念](media-graph-concept.md)
* [视频录制概念](video-recording-concept.md)

## <a name="overview"></a>概述

连续视频录制（CVR）是指连续记录视频源的视频。 IoT Edge 上的实时视频分析通过包含一个 RTSP 源节点和一个资产接收器节点的[媒体图形](media-graph-concept.md)，以24小时的方式持续持续记录视频。 下图显示了这种媒体图形的图形表示。 可在[此处](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset)找到此类 media graph 的[图形拓扑](media-graph-concept.md?branch=release-preview-media-services-lva#media-graph-topologies-and-instances)的 JSON 表示形式。

![连续视频录制](./media/continuous-video-recording/continuous-video-recording-overview.png)

上面所示的媒体图可以在边缘设备上运行，并将资产接收器记录视频到 Azure 媒体服务[资产](terminology.md#asset)。 只要 media graph 保持激活状态，就会记录视频。 由于视频被记录为资产，因此可以使用媒体服务的现有流式处理功能播放视频。 有关更多详细信息，请参阅[播放录制的内容](video-playback-concept.md)。

## <a name="resilient-recording"></a>复原录制

IoT Edge 上的实时视频分析支持在不太理想的网络情况下操作，在这种情况下，边缘设备有时可能会失去与云的连接，或者可能会出现可用带宽下降的情况。 为此，将源中的视频记录到本地缓存中，并定期自动与资产同步。 如果检查[关系图拓扑 JSON](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)，将看到定义了以下属性：

```
    "segmentLength": "PT30S",
    "localMediaCacheMaximumSizeMiB": "2048",
    "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```
后两个属性与复原录制相关（两者也都是资产接收器节点的必需属性）。 LocalMediaCachePath 属性告知资产接收器在上传到资产之前，使用该文件夹路径来缓存媒体数据。 可以[查看本文，了解边缘](https://docs.microsoft.com/azure/iot-edge/how-to-access-host-storage-from-module)模块如何使用设备的本地存储。 LocalMediaCacheMaximumSizeMiB 属性定义资产接收器可用作缓存的磁盘空间量（1 MiB = 1024 * 1024 个字节）。 

如果边缘模块长时间失去连接，并且缓存文件夹中存储的内容达到 localMediaCacheMaximumSizeMiB 值，则资产接收器将开始从缓存中丢弃数据，从最旧的数据开始。 例如，如果设备在10AM 断开连接，而缓存达到了6到6点的最大限制，则资产接收器将开始删除在10AM 记录的数据。 

当恢复网络连接时，资产接收器将从缓存开始上传，并再次从最早的数据开始。 在上面的示例中，假设在恢复连接的时间（如6：1:02）时，需要从缓存中丢弃5分钟的视频，则资产接收器将从10：05AM 标记开始上载。

如果以后使用[这些](playback-recordings-how-to.md)api 检查资产，则会看到资产中的间隔大约10AM 到10：05AM。

## <a name="segmented-recording"></a>分段记录  

如上所述，资产接收器节点会将视频记录到本地缓存中，并定期将视频上传到云。 SegmentLength 属性（如上一节所示）将帮助你控制将数据写入到正在记录资产的存储帐户的写入事务成本。 例如，如果将上传时间从30秒增加到5分钟，则会将存储事务的数量降为10（5 * 60/30）。

SegmentLength 属性确保边缘模块每 segmentLength 秒最多上载一次视频。 此属性的最小值为30秒（也是默认值），可以增加30秒，最大值为5分钟。

>[!NOTE]
>请参阅[此](playback-recordings-how-to.md)文，了解 segmentLength 对播放的影响。


## <a name="see-also"></a>请参阅

* [基于事件的视频录制](event-based-video-recording-concept.md)
* [播放录制的内容](video-playback-concept.md)


## <a name="next-steps"></a>后续步骤

[教程：连续视频录制](continuous-video-recording-tutorial.md)
