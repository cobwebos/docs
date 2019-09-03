---
title: Azure 媒体服务实时事件和云 DVR | Microsoft Docs
description: 本文介绍了什么是实时输出，以及如何使用云 DVR。
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
ms.openlocfilehash: a10c76dd7fb4ef1e9a45666ff3a3ca0d937d2c94
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231224"
---
# <a name="using-a-cloud-digital-video-recorder-dvr"></a>使用云数字视频录制器 (DVR)

在 Azure 媒体服务中,[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)对象类似于数字录像机, 它会将实时流捕获并记录到媒体服务帐户中的资产中。 记录的内容将保存到[资产](https://docs.microsoft.com/rest/api/media/assets)资源定义的容器中 (容器在附加到帐户的 Azure 存储帐户中)。 实时输出还允许您控制传出实时流的一些属性, 例如在存档记录中保留的流的数量 (例如, cloud DVR 的容量) 以及查看器是否可以开始观看实时流。 磁盘上的存档是一种只保存实时输出的**archiveWindowLength**属性中指定的内容量的循环存档 "窗口"。 超出此窗口的内容将自动从存储容器中丢弃，且不可恢复。 ArchiveWindowLength 值表示 ISO-8601 时间跨度持续时间 (例如, PTHH: MM: SS), 它指定了 DVR 的容量, 最小可设置为3分钟, 最长25小时。

实时事件与其实时输出之间的关系类似于传统的电视广播, 其中通道 (实时事件) 表示视频的恒定流, 而录制 (实时输出) 的范围限定为特定时间段 (例如, 晚上6: 下午6:30 到 7:00 () 的新闻。 将流流入实时事件后, 可以通过创建资产、实时输出和流式处理定位符来开始流式处理事件。 实时输出将存档流, 并通过[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)将其提供给查看者。 可以在具有不同存档长度和设置的实时事件上创建多个实时输出 (最多3个)。 有关实时流式处理工作流的信息, 请参阅[常规步骤](live-streaming-overview.md#general-steps)部分。

## <a name="using-a-dvr-during-an-event"></a>在事件期间使用 DVR 

本部分讨论如何在事件期间使用 DVR 来控制流的哪些部分可用于 "倒带"。

ArchiveWindowLength 值决定了查看器可从当前实时位置查找的时间。 ArchiveWindowLength 值还决定了客户端清单能够增加多长时间。

假设你正在流式处理足球游戏, 并且它的 ArchiveWindowLength 仅为30分钟。 开赛后 45 分钟才开始观看赛事的观看者最多可以寻轨到前 15 分钟的标记处。 游戏的实时输出将继续运行, 直到实时事件停止, 但位于 archiveWindowLength 之外的内容会不断地从存储中丢弃, 且不可恢复。 在此示例中, 事件开始和15分钟标记之间的视频将从 DVR 和资产的 blob 存储中的容器中清除。 存档是不可恢复，将从 Azure Blob 存储中的容器内删除。

实时事件最多支持三个并发运行的实时输出 (可以同时从一个实时流创建最多3个记录/存档)。 这样，便可以根据需要发布和存档事件的不同部分。 假设你需要广播全天候的直播线性源，并需要全天创建不同节目的“录制内容”，以便将其作为点播内容提供给客户，供其抽时间观看。 对于这种情况，请先创建一个主要实时输出，其存档窗口较短，为 1 小时或更短 - 这是供观看者欣赏的主要实时流。 你将为此实时输出创建流式处理定位符, 并将其发布到你的应用程序或网站作为 "实时" 源。 当实时事件正在运行时, 您可以通过编程方式在程序的开始处创建第二个并发实时输出 (或者提前5分钟, 提供稍后要剪裁的句柄)。 此第二个实时输出可以在程序结束5分钟后删除。 使用此第二个资产, 可以创建新的流式处理定位符, 以将此程序发布为应用程序目录中的按需资产。 对于想要作为点播视频共享的其他节目边界或突出显示内容, 你可以多次重复此过程, 而第一个实时输出中的 "实时" 源会继续广播线性源。 

## <a name="creating-an-archive-for-on-demand-playback"></a>为点播播放创建存档

实时输出要存档到的资产, 在删除实时输出时, 会自动成为按需资产。 必须先删除所有实时输出, 然后才能停止实时事件。 可以使用可选的标志[removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body)自动删除停止时的实时输出。 

即使你停止并删除了事件, 只要没有删除资产, 用户也能够按需将你的已存档内容流式传输为视频。 如果资产被某个事件使用, 则不应将其删除;必须先删除该事件。

如果已使用流式处理定位符发布了实时输出的资产, 则在流式处理定位符过期或删除之前 (以先达到者为准), 实时事件 (最多 DVR 窗口长度) 将继续可见。

有关详细信息，请参阅：

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 删除实时输出时, 不会删除资产中的基础资产和内容。 

## <a name="next-steps"></a>后续步骤

* [子剪辑视频](subclip-video-rest-howto.md)。
* [为资产定义筛选器](filters-dynamic-manifest-rest-howto.md)。
