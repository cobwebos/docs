---
title: 使用时移和实时输出创建点播视频
titleSuffix: Azure Media Services
description: 本文介绍如何使用时间移位和实时输出来记录实时流和创建按需播放。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/07/2020
ms.author: juliako
ms.openlocfilehash: 231aeb210a7b97e8c0cfd0e21c48053c660b6128
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995807"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>使用时移和实时输出创建点播视频

在 Azure 媒体服务中，[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)对象类似于数字视频录制器，它会捕获实时流并将其记录到媒体服务帐户中的某个资产内。 记录的内容将保存到[资产](https://docs.microsoft.com/rest/api/media/assets)资源定义的容器中（该容器位于附加到你的帐户的 Azure 存储帐户中）。 使用实时输出还可以控制传出实时流的某些属性，例如，存档记录中保存的流的量（如云 DVR 的容量），以及观看者何时可以开始观看实时流。 磁盘上的存档是一个环形存档“窗口”，仅保存实时输出的 **archiveWindowLength** 属性中指定的内容量。 超出此窗口的内容将自动从存储容器中丢弃，且不可恢复。 archiveWindowLength 值表示一个 ISO-8601 时间跨度持续时间（例如 PTHH:MM:SS），指定 DVR 的容量。 该值最少可设置为1分钟，最大值为25小时。

实时事件与其实时输出之间的关系类似于传统的电视广播，其中的频道（实时事件）表示恒定的视频流，录制（实时输出）限定为特定的时间段（例如，下午 6:30 到 7:00 的晚间新闻）。 将流传输到实时事件后，可以通过创建资产、实时输出和流定位符来启动流事件。 实时输出会存档流，并使观看者可通过[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)使用该流。 可以使用不同的存档长度和设置针对实时事件创建多个实时输出（最多三个）。 有关实时传送视频流工作流的信息，请参阅[常规步骤](live-streaming-overview.md#general-steps)部分。

## <a name="using-a-dvr-during-an-event"></a>在发生事件期间使用 DVR

本部分介绍在发生事件期间如何使用 DVR 来控制可“后退”的流部分。

`archiveWindowLength` 值确定观看者可在当前直播位置中向后的多长时间继续播放。 `archiveWindowLength` 值还确定了客户端清单能够增加多长时间。

假设你正在流式传输一场足球比赛，其 `ArchiveWindowLength` 仅为 30 分钟。 开赛后 45 分钟才开始观看赛事的观看者最多可以寻轨到前 15 分钟的标记处。 比赛的实时输出会持续到实时事件停止为止。 超出 archiveWindowLength 的内容将持续从存储中丢弃，且不可恢复。 在此示例中，赛事起始位置与 15 分钟标记之间的视频将从 DVR 中清除，并且会从资产 Blob 存储中的容器内清除。 存档是不可恢复，将从 Azure Blob 存储中的容器内删除。

一个实时事件最多支持三个并发运行的实时输出（可从一个实时流最多同时创建 3 个录制文件/存档）。 此项支持可让你根据需要发布和存档事件的不同部分。 假设你需要广播全天候的直播线性源，并需要全天创建不同节目的“录制内容”，以便将其作为点播内容提供给客户，供其抽时间观看。 对于这种情况，请先创建一个主要实时输出，其存档窗口较短，为 1 小时或更短 - 这是供观看者欣赏的主要实时流。 可以为此实时输出创建一个流定位符，然后将其作为“实时”源发布到应用或网站。 当实时事件处于运行状态时，可以在节目开始时通过编程方式创建另一个并发实时输出（也可提前 5 分钟这样做，以便提供一些可供以后剪裁的控点）。 在结束节目 5 分钟之后，可以删除第二个实时输出。 使用第二个资产可以创建新的流定位符，以便在应用的目录中将此节目发布为点播资产。 可以针对需要作为点播视频共享的其他节目边界或突出显示内容多次重复此过程，同时第一个实时输出提供的“实时”源可以继续广播线性源。

## <a name="creating-an-archive-for-on-demand-playback"></a>为点播创建存档

实时输出要存档到的资产，在删除实时输出时，会自动成为点播资产。 必须先删除所有实时输出，然后才能停止实时事件。 在停止时，可以使用可选标志 [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) 自动删除实时输出。

即使你停止并删除了事件，只要没有删除资产，用户也能够将已存档内容作为点播视频进行流式传输。 如果资产被某个事件使用，则不应将其删除，必须先删除该事件。

如果已使用流定位符发布了实时输出的资产，则实时事件（最长为 DVR 窗口长度）将继续可见，直到流定位符过期或被删除（以先发生者为准）。

有关详细信息，请参阅：

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 删除实时输出不会删除基础资产和该资产中的内容。

## <a name="next-steps"></a>后续步骤

* [创建视频的子剪辑](subclip-video-rest-howto.md)。
* [为资产定义筛选器](filters-dynamic-manifest-rest-howto.md)。
