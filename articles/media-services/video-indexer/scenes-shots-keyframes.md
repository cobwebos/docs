---
title: 视频索引器场景、截图和关键帧
titleSuffix: Azure Media Services
description: 本主题概述了视频索引器场景、截图和关键帧。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b24778434596f583be44572612c856fa4e0cecde
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860232"
---
# <a name="scenes-shots-and-keyframes"></a>场景、镜头和关键帧

视频索引器支持基于结构和语义属性将视频分段为临时单位。 此功能使客户能够根据不同的粒度轻松浏览、管理和编辑视频内容。 例如，根据本主题中所述的场景、快照和关键帧。   

![场景、镜头和关键帧](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>场景检测  
 
视频索引器根据视觉提示确定场景在视频中的更改时间。场景描述单个事件，并由一系列在语义上相关的连续快照组成。 场景缩略图是其基础拍摄的第一个关键帧。 视频索引器基于连续照片中的颜色一致性将视频分段，并检索每个场景的开始时间和结束时间。 场景检测被视为一种挑战性的任务，因为它涉及量化视频的语义部分。

> [!NOTE]
> 适用于至少包含3场景的视频。

## <a name="shot-detection"></a>镜头检测

视频索引器通过跟踪相邻帧的配色方案中的突然转换和逐步转换，确定在视频中拍摄图像的时间。 拍摄的元数据包括开始时间和结束时间，以及该快照中包含的关键帧的列表。 该照片是从同一相机同时拍摄的连续帧。

## <a name="keyframe-detection"></a>关键帧检测

选择最能表示快照的帧。 关键帧是基于美观属性从整个视频中选择的代表帧（例如，对比度和 stableness）。 视频索引器将在拍摄的元数据中检索关键帧 Id 的列表，具体取决于哪些客户可以提取关键帧缩略图。 

关键帧与输出 JSON 中的快照相关联。 

## <a name="editorial-shot-type-detection"></a>编辑快照类型检测

与 insights JSON 中的个别截图关联的拍摄类型表示其编辑类型。 在编辑视频到剪辑、尾部或搜索特定的关键帧样式以实现艺术时，可能会发现这些拍摄类型特征非常有用。 根据每个拍摄的第一个关键帧的分析来确定不同的类型。 照片按其第一个关键帧中显示的面部的规模、大小和位置进行标识。 

根据相机与表面之间的距离来确定拍摄的大小和比例。 使用这些属性，视频索引器将检测以下快照类型：

* 宽：显示整个人员的正文。
* 中：显示人员的正文和脸。
* 关闭：主要显示人员的人脸。
* 极近关闭：显示一个人来填充屏幕。 

还可以根据框架中心的使用者字符的位置来确定拍摄类型。 此属性在视频索引器中定义以下快照类型：

* 左面：某人出现在框架的左侧。
* 中心脸：某个人出现在框架的中心区域。
* 右栏：某个人出现在该帧的右侧。
* 户外：某个人出现在户外的设置中。
* 室内：某个人在室内设置中出现。

其他特征：

* 两个抓图：显示两个人的中等大小。
* 多面面：多于两人。

## <a name="next-steps"></a>后续步骤

[检查 API 生成的视频索引器输出](video-indexer-output-json-v2.md#scenes)
