---
title: 视频索引器的场景中，快照和关键帧-Azure
titlesuffix: Azure Media Services
description: 本主题概述的视频索引器场景、 快照和关键帧。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d312a93f83ef38fa1ae855a1e313280fc608948d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799408"
---
# <a name="scenes-shots-and-keyframes"></a>场景、镜头和关键帧

视频索引器支持基础结构和语义属性上的临时单位在支持将视频。 此功能使客户能够轻松地浏览、 管理和编辑基于不同的粒度其视频内容。 例如，根据场景、 快照和关键帧，本主题中所述。 **场景检测**功能目前处于预览状态。   

![场景、镜头和关键帧](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>场景检测 （预览版）

视频索引器确定当场景更改在视频中基于视觉提示。场景描述了单个事件，它由一系列连续快照，在语义上是相关。 场景缩略图是其基础截图的第一个关键帧。 视频索引器为基于颜色一致性在连续快照之间的场景段视频，并检索开始和结束时间的每个场景。 场景检测被视为具有挑战性的任务，因为它涉及量化语义方面的视频。

> [!NOTE]
> 适用于包含至少 3 个场景的视频。

## <a name="shot-detection"></a>镜头检测

视频索引器确定在视频中通过跟踪转换配色方案中的相邻帧突然和逐渐转换基于视觉提示，在镜头发生更改时。 截图的元数据包括一个的开始和结束时间，以及该快照中包含的关键帧的列表。 快照是在同一时间来自同一个照相机的连续帧。

## <a name="keyframe-detection"></a>关键帧检测

选择最能代表截图帧。 关键帧是从整个视频基于美观属性 （例如，对比度和 stableness） 中选择代表帧。 视频索引器检索截图的元数据，根据哪些客户可以提取关键帧缩略图的一部分的关键帧 Id 的列表。 

关键帧与相关联的输出 JSON 中的快照。 

## <a name="next-steps"></a>后续步骤

[检查视频索引器输出中生成的 API](video-indexer-output-json-v2.md#scenes)