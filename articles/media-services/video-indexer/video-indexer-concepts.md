---
title: 视频索引器概念
titleSuffix: Azure Media Services
description: 本文介绍 Azure 媒体服务视频索引器服务的一些概念。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900680"
---
# <a name="video-indexer-concepts"></a>视频索引器概念
 
本文介绍视频索引器服务的一些概念。
    
## <a name="summarized-insights"></a>汇总见解

汇总见解包含聚合视图形式的数据：人脸、主题和情感。 例如，汇总见解包含所有人脸、每个人脸出现的时间范围以及显示人脸的时间百分比，不需在数千个时间范围中逐个检查其中包含哪些人脸。

## <a name="time-range-vs-adjusted-time-range"></a>时间范围和调整的时间范围

TimeRange 是原始视频中的时间范围。 AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个 1 小时的视频，只使用其中的 1 行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。
 
## <a name="blocks"></a>Blocks

可以通过块更轻松地浏览数据。 例如，可以在发言人换人或中断时间长时将块细分。

## <a name="next-steps"></a>后续步骤

若要了解如何入门，请参阅[如何注册并上传第一个视频](video-indexer-get-started.md)。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)
