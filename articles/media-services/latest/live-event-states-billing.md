---
title: Azure 媒体服务中的 LiveEvent 状态和计费 | Microsoft Docs
description: 本主题概述了 Azure 媒体服务 LiveEvent 状态和计费。
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933676"
---
# <a name="live-event-states-and-billing"></a>直播活动状态和计费

在 Azure 媒体服务中，一旦直播活动的状态转换为**正在运行**，就会开始计费。 若要停止对直播活动的计费，必须停止直播活动。

如果[Live 事件](https://docs.microsoft.com/rest/api/media/liveevents)上的**LiveEventEncodingType**设置为 Standard 或 Premium1080p，则媒体服务自动关闭输入源丢失12小时后仍处于**运行**状态的任何实时事件，并且没有**活动输出**s。 但是，在直播活动处于**正在运行**状态的时间段内，仍会进行计费。

> [!NOTE]
> 传递实时事件不会自动关闭，必须通过 API 显式停止，以避免过多的计费。 

## <a name="states"></a>States

直播活动可能会处于以下任一状态。

|状况|描述|
|---|---|
|**已停止**| 这是创建后实时事件的初始状态（除非自动启动设置为 true。）此状态下不会发生计费。 在此状态下，可以更新直播活动属性，但不允许进行流式处理。|
|**正在启动**| 正在启动直播活动并分配资源。 此状态下不会发生计费。 此状态下不允许进行更新或流式处理。 如果发生错误，则直播活动会返回到“已停止”状态。|
|**正在运行**| 已分配了直播活动资源，已生成了引入和预览 URL，并且能够接收实时流。 此时，计费处于活动状态。 必须显式对直播活动资源调用停止操作才能停止进一步计费。|
|**正在停止**| 正在停止直播活动并解除预配资源。 此暂时性状态下不会发生计费。 此状态下不允许进行更新或流式处理。|
|**正在删除**| 正在删除直播活动。 此暂时性状态下不会发生计费。 此状态下不允许进行更新或流式处理。|

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)
