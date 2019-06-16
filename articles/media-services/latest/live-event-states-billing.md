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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2907b5be7f8d5fda3d510484179e80b065ab64b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074896"
---
# <a name="live-event-states-and-billing"></a>实时事件状态和计费

在 Azure 媒体服务中，一旦实时事件的状态转换为**正在运行**，就会开始计费。 若要停止对实时事件的计费，必须停止实时事件。

当**LiveEventEncodingType**上你[Live 事件](https://docs.microsoft.com/rest/api/media/liveevents)设置为标准或 Premium1080p，媒体服务自动关闭任何仍处于的实时事件**运行**状态 12小时后输入的源丢失，并且没有任何**Live 输出**s 运行。 但是，在实时事件处于**正在运行**状态的时间段内，仍会进行计费。

## <a name="states"></a>States

实时事件可能会处于以下任一状态。

|状态|描述|
|---|---|
|**已停止**| 这是实时事件在创建后的初始状态（除非设置了自动启动）。此状态下不会发生计费。 在此状态下，可以更新实时事件属性，但不允许进行流式处理。|
|**正在启动**| 正在启动实时事件并分配资源。 此状态下不会发生计费。 此状态下不允许进行更新或流式处理。 如果发生错误，则实时事件会返回到“已停止”状态。|
|**正在运行**| 已分配了实时事件资源，已生成了引入和预览 URL，并且能够接收实时流。 此时，计费处于活动状态。 必须显式对实时事件资源调用停止操作才能停止进一步计费。|
|**正在停止**| 正在停止实时事件并解除预配资源。 此暂时性状态下不会发生计费。 此状态下不允许进行更新或流式处理。|
|**正在删除**| 正在删除实时事件。 此暂时性状态下不会发生计费。 此状态下不允许进行更新或流式处理。|

## <a name="next-steps"></a>后续步骤

- [实时传送视频流概述](live-streaming-overview.md)
- [实时传送视频流教程](stream-live-tutorial-with-api.md)
