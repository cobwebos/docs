---
title: Azure 视频索引器概念 | Microsoft Docs
description: 本主题介绍视频索引器服务的一些概念。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399489"
---
# <a name="video-indexer-concepts"></a>视频索引器概念
 
本主题介绍视频索引器服务的一些概念。
    
## <a name="summarized-insights"></a>汇总见解

汇总见解包含聚合视图形式的数据：人脸、关键字、情绪。 例如，汇总见解包含所有人脸、每个人脸出现的时间范围以及显示人脸的时间百分比，不需在数千个时间范围中逐个检查其中包含哪些人脸。

## <a name="topicskeywords"></a>主题/关键字

主题/关键字位于关键短语列表中，这些短语是视频索引器从文本中提取的。 例如，某个 Scott Guthrie 视频可能包含以下主题/关键字：安全、Azure、Microsoft 云、收入。

## <a name="sentiments"></a>情绪

视频索引器在分析脚本时，也检测情绪。 例如，“这是非常令人兴奋的事件”是正面情绪。

## <a name="time-range-vs-adjusted-time-range"></a>时间范围和调整的时间范围

TimeRange 是原始视频中的时间范围。 AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个 1 小时的视频，只使用其中的 1 行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。
 
## <a name="blocks"></a>块

可以通过块更轻松地浏览数据。 例如，可以在发言人换人或中断时间长时将块细分。

## <a name="next-steps"></a>后续步骤

若要了解如何入门，请参阅[如何注册并上传第一个视频](video-indexer-get-started.md)。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)
