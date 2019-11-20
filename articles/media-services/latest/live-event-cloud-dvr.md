---
title: 使用时间变化的输出来创建按需视频播放
titleSuffix: Azure Media Services
description: 了解如何使用时间移位和实时输出来记录实时流和创建按需播放。
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: acba251a57f39c07d690d0c55665b8914feaf06c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186227"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>使用时间变化的输出来创建按需视频播放

在 Azure 媒体服务中，[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)对象类似于数字视频录制器，它会捕获实时流并将其记录到媒体服务帐户中的某个资产内。 记录的内容将保存到[资产](https://docs.microsoft.com/rest/api/media/assets)资源定义的容器中（该容器位于附加到你的帐户的 Azure 存储帐户中）。 实时输出还允许您控制传出实时流的一些属性，例如在存档记录中保留的流的数量（例如，cloud DVR 的容量），或查看者可以开始观看实时流的时间。 磁盘上的存档是一种只保存实时输出的**archiveWindowLength**属性中指定的内容量的循环存档 "窗口"。 超出此窗口范围的内容将自动从存储容器中丢弃，且无法恢复。 ArchiveWindowLength 值表示 ISO-8601 timespan 持续时间（例如，PTHH： MM： SS），它指定了 DVR 的容量。 该值最少可设置为三分钟，最大值为25小时。

实时事件和其实时输出之间的关系类似于传统的电视广播，因为频道（实时事件）表示视频的恒定流，而录制（实时输出）的范围限定为特定时间段（例如，晚上的新闻6：下午6:30 到7： 00 (）。 将流流入实时事件后，可以通过创建资产、实时输出和流式处理定位符来开始流式处理事件。 实时输出会存档流，并使观看者可通过[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)使用该流。 可以在具有不同存档长度和设置的实时事件上创建多个实时输出（最多3个）。 有关实时传送视频流工作流的信息，请参阅[常规步骤](live-streaming-overview.md#general-steps)部分。

## <a name="using-a-dvr-during-an-event"></a>在发生事件期间使用 DVR

本部分介绍在发生事件期间如何使用 DVR 来控制可“后退”的流部分。

`archiveWindowLength` 值决定了查看器可从当前实时位置返回的时间。 `archiveWindowLength` 值还决定了客户端清单能够增加多长时间。

假设你正在流式处理足球游戏，它的 `ArchiveWindowLength` 仅为30分钟。 开赛后 45 分钟才开始观看赛事的观看者最多可以寻轨到前 15 分钟的标记处。 游戏的实时输出将继续，直到实时事件停止。 超出 archiveWindowLength 的内容会不断地从存储中丢弃，且不可恢复。 在此示例中，赛事起始位置与 15 分钟标记之间的视频将从 DVR 中清除，并且会从资产 Blob 存储中的容器内清除。 存档无法恢复，并将从 Azure blob 存储中的容器删除。

实时事件最多支持三个并发运行的实时输出（可以同时从一个实时流创建最多3个记录/存档）。 此支持允许根据需要发布和存档事件的不同部分。 假设你需要广播全天候的直播线性源，并需要全天创建不同节目的“录制内容”，以便将其作为点播内容提供给客户，供其抽时间观看。 对于这种情况，您首先创建一个包含1小时或更短的简短存档窗口的主实时输出–这是您的查看器将在其中进行调整的主要实时流。 你将为此实时输出创建流式处理定位符，并将其发布到你的应用或网站作为 "实时" 源。 当实时事件处于运行状态时，可以在节目开始时通过编程方式创建另一个并发实时输出（也可提前 5 分钟这样做，以便提供一些可供以后剪裁的控点）。 在结束节目 5 分钟之后，可以删除第二个实时输出。 使用此第二个资产，可以创建新的流式处理定位符，以将此程序发布为应用目录中的按需资产。 可以针对需要作为点播视频共享的其他节目边界或突出显示内容多次重复此过程，同时第一个实时输出提供的“实时”源可以继续广播线性源。

## <a name="creating-an-archive-for-on-demand-playback"></a>为点播创建存档

当删除实时输出时，实时输出所存档的资产将自动成为按需资产。 必须先删除所有实时输出，然后才能停止实时事件。 可以使用可选的标志[removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body)自动删除停止时的实时输出。

即使你停止并删除了事件，只要没有删除资产，用户也可以按需将存档内容流式传输为视频。 如果资产被某个事件使用，则不应将其删除;必须先删除该事件。

如果已使用流式处理定位符发布了实时输出的资产，则在流式处理定位符过期或删除之前（以先达到者为准），实时事件（最多 DVR 窗口长度）将继续可见。

有关详细信息，请参阅：

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 删除实时输出时，不会删除资产中的基础资产和内容。

## <a name="next-steps"></a>后续步骤

* [创建视频的子剪辑](subclip-video-rest-howto.md)。
* [为资产定义筛选器](filters-dynamic-manifest-rest-howto.md)。
