---
title: 视频索引器场景、截图和关键帧-Azure
titlesuffix: Azure Media Services
description: 本主题概述了视频索引器场景、截图和关键帧。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815648"
---
# <a name="scenes-shots-and-keyframes"></a>场景、镜头和关键帧

视频索引器支持基于结构和语义属性将视频分段为临时单位。 此功能使客户能够根据不同的粒度轻松浏览、管理和编辑视频内容。 例如, 根据本主题中所述的场景、快照和关键帧。   

![场景、镜头和关键帧](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>场景检测  
 
视频索引器根据视觉提示确定场景在视频中的更改时间。场景描述单个事件, 并由一系列在语义上相关的连续快照组成。 场景缩略图是其基础拍摄的第一个关键帧。 视频索引器基于连续照片中的颜色一致性将视频分段, 并检索每个场景的开始时间和结束时间。 场景检测被视为一种挑战性的任务, 因为它涉及量化视频的语义部分。

> [!NOTE]
> 适用于至少包含3场景的视频。

## <a name="shot-detection"></a>镜头检测

视频索引器通过跟踪相邻帧的配色方案中的突然转换和逐步转换, 确定在视频中拍摄图像的时间。 拍摄的元数据包括开始时间和结束时间, 以及该快照中包含的关键帧的列表。 该照片是从同一相机同时拍摄的连续帧。

## <a name="keyframe-detection"></a>关键帧检测

选择最能表示快照的帧。 关键帧是基于美观属性从整个视频中选择的代表帧 (例如, 对比度和 stableness)。 视频索引器将在拍摄的元数据中检索关键帧 Id 的列表, 具体取决于哪些客户可以提取关键帧缩略图。 

关键帧与输出 JSON 中的快照相关联。 

## <a name="next-steps"></a>后续步骤

[检查 API 生成的视频索引器输出](video-indexer-output-json-v2.md#scenes)
