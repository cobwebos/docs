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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322329"
---
# <a name="using-a-cloud-dvr"></a>使用云 DVR

使用[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)可以控制传出实时流的属性，例如，记录的流数量（如云 DVR 的容量），以及观看者是否可以开始观看实时流。 **实时事件**与其**实时输出**之间的关系类似于传统的电视广播，其中的频道（**实时事件**）表示恒定的视频流，录制（**实时输出**）限定为特定的时间段（例如，下午 6:30 到 7:00 的晚间新闻）。 可以使用数字视频录像机 (DVR) 录制电视节目 – 实时事件中的等效功能是通过 ArchiveWindowLength 属性管理的。 它是一个 ISO-8601 时间跨度持续时间（例如 PTHH:MM:SS），指定 DVR 的容量，最小可设置为 3 分钟，最大为 25 小时。

## <a name="live-output"></a>实时输出

**ArchiveWindowLength** 值确定观看者可在当前直播位置中向后寻轨的时间长短。  **ArchiveWindowLength** 值还确定了客户端清单能够增加多长时间。

假设你正在流式传输一场足球比赛，其 **ArchiveWindowLength** 仅为 30 分钟。 开赛后 45 分钟才开始观看赛事的观看者最多可以寻轨到前 15 分钟的标记处。 比赛的**实时输出**会持续到**实时事件**停止为止，但超出 **ArchiveWindowLength** 的内容将持续从存储中丢弃，且不可恢复。 在此示例中，赛事起始位置与 15 分钟标记之间的视频将从 DVR 中清除，并且会从[资产](https://docs.microsoft.com/rest/api/media/assets) Blob 存储中的容器内清除。 存档是不可恢复，将从 Azure Blob 存储中的容器内删除。

每个**实时输出**与一个**资产**相关联，用于将视频记录到关联的 Azure Blob 存储容器。 若要发布实时输出，必须为该**资产**创建**流式处理定位符**。 创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)后，可以生成可提供给观看者的流 URL。

一个**实时事件**最多支持三个并发运行的**实时输出**，因此，从一个实时流最多可以创建 3 个录制文件/存档。 这样，便可以根据需要发布和存档事件的不同部分。 假设你需要广播全天候的直播线性源，并需要全天创建不同节目的“录制内容”，以便将其作为点播内容提供给客户，供其抽时间观看。 对于这种情况，请先创建一个主要实时输出，其存档窗口较短，为 1 小时或更短 - 这是供观看者欣赏的主要实时流。 可以为此**实时输出**创建一个**流式处理定位符**，然后将其作为“实时”源发布到应用程序或网站。 当**实时事件**处于运行状态时，可以在节目开始时通过编程方式创建另一个并发**实时输出**（也可提前 5 分钟这样做，以便提供一些可供以后剪裁的句柄）。 在结束节目 5 分钟之后，可以删除第二个**实时输出**。 使用第二个**资产**可以创建新的**流式处理定位符**，以便在应用程序的目录中将此节目发布为点播资产。 可以针对需要作为点播视频共享的其他节目边界或突出显示内容多次重复此过程，同时第一个**实时输出**提供的“实时”源可以继续广播线性源。 

> [!NOTE]
> **实时输出**在创建时启动，在删除后停止。 删除**实时输出**不会删除基础**资产**和该资产中的内容。 
>
> 如果已使用**流式处理定位符**发布了**实时输出**资产，则**实时事件**（最长为 DVR 窗口长度）将继续可见，直到**流式处理定位符**过期或被删除（以先发生者为准）。

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)

