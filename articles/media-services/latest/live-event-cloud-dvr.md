---
title: Azure 媒体服务 LiveEvent 和云 DVR | Microsoft Docs
description: 本文介绍什么是 LiveOutput，以及如何使用云 DVR。
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 3646c6167f901fe43080d39df42fdb127b1c7fc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828139"
---
# <a name="using-a-cloud-dvr"></a>使用云 DVR

使用 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) 可以控制传出实时流的属性，例如，记录的流数量（如云 DVR 的容量），以及观看者是否可以开始观看实时流。 **LiveEvent** 及其 **LiveOutput** 之间的关系类似于传统的电视广播，其中的频道 (**LiveEvent**) 表示恒定的视频流，录制 (**LiveOutput**) 限定为特定的时间段（例如，下午 6:30 到 7:00 的晚间新闻）。 可以使用数字视频录像机 (DVR) 录制电视节目 – LiveEvents 中的等效功能是通过 ArchiveWindowLength 属性管理的。 它是一个 ISO-8601 时间跨度持续时间（例如 PTHH:MM:SS），指定 DVR 的容量，最小可设置为 3 分钟，最大为 25 小时。

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** 值确定观看者可在当前直播位置中向后寻轨的时间长短。  **ArchiveWindowLength** 值还确定了客户端清单能够增加多长时间。

假设你正在流式传输一场足球比赛，其 **ArchiveWindowLength** 仅为 30 分钟。 开赛后 45 分钟才开始观看赛事的观看者最多可以寻轨到前 15 分钟的标记处。 比赛的 **LiveOutput** 会持续到 **LiveEvent** 停止为止，但超出 **ArchiveWindowLength** 的内容将持续从存储中丢弃，且不可恢复。 在此示例中，赛事起始位置与 15 分钟标记之间的视频将从 DVR 中清除，并且会从[资产](https://docs.microsoft.com/rest/api/media/assets) Blob 存储中的容器内清除。 存档是不可恢复，将从 Azure Blob 存储中的容器内删除。

每个 **LiveOutput** 与某个**资产**相关联，用于将视频记录到关联的 Azure Blob 存储容器。 若要发布 LiveOutput，必须为该**资产**创建 **StreamingLocator**。 创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 后，可以生成可提供给观看者的流 URL。

一个 **LiveEvent** 最多支持三个并发运行的 **LiveOutput**，因此，从一个直播流最多可以创建 3 个录制文件/存档。 这样，便可以根据需要发布和存档事件的不同部分。 假设你需要广播全天候的直播线性源，并需要全天创建不同节目的“录制内容”，以便将其作为点播内容提供给客户，供其抽时间观看。 对于这种情况，请先创建一个主要 LiveOutput，其存档窗口较短，为 1 小时或更短 - 这是供观看者欣赏的主要直播流。 可为此 **LiveOutput** 创建一个 **StreamingLocator**，然后将其作为“直播”源发布到应用程序或网站。 当 **LiveEvent** 处于运行状态时，可以在节目开始时通过编程方式创建另一个并发 **LiveOutput**（也可提前 5 分钟这样做，以便提供一些可供以后剪裁的句柄）。 在结束节目 5 分钟之后，可以删除第二个 **LiveOutput**。 使用第二个**资产**可以创建新的 **StreamingLocator**，以便在应用程序的目录中将此节目发布为点播资产。 可以针对需要作为点播视频共享的其他节目边界或突出显示内容多次重复此过程，同时第一个 **LiveOutput** 提供的“直播”源可以继续广播线性源。 

> [!NOTE]
> **LiveOutput** 在创建时启动，在删除后停止。 删除 LiveOutput 不会删除基础资产和该资产中的内容。 
>
> 如果已使用 StreamingLocator 发布了 LiveOutput 资产，则 LiveEvent（长达 DVR 窗口长度）将继续可见，直到 StreamingLocator 过期或被删除（以先发生为准）。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

