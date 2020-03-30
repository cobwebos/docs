---
title: 视频索引器场景、镜头和关键帧
titleSuffix: Azure Media Services
description: 本主题概述了视频索引器场景、镜头和关键帧。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245936"
---
# <a name="scenes-shots-and-keyframes"></a>场景、镜头和关键帧

视频索引器支持根据结构和语义属性将视频分割到时态单位。 此功能使客户能够根据不同的粒度轻松浏览、管理和编辑其视频内容。 例如，基于本主题中介绍的场景、镜头和关键帧。   

![场景、镜头和关键帧](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>场景检测  
 
视频索引器根据视觉提示确定视频中场景何时更改。场景描述单个事件，由一系列连续镜头组成，这些镜头在语义上是相关的。 场景缩略图是其基础拍摄的第一个关键帧。 视频索引器根据连续镜头的颜色一致性将视频分割成场景，并检索每个场景的开始和结束时间。 场景检测被认为是一项具有挑战性的任务，因为它涉及量化视频的语义方面。

> [!NOTE]
> 适用于包含至少 3 个场景的视频。

## <a name="shot-detection"></a>镜头检测

视频索引器通过跟踪相邻帧的配色方案中的突然和渐进过渡，根据视觉提示确定视频拍摄何时发生更改。 拍摄的元数据包括开始和结束时间，以及该拍摄中包含的关键帧列表。 拍摄是同时从同一摄像机拍摄的连续帧。

## <a name="keyframe-detection"></a>关键帧检测

视频索引器选择最能代表每个镜头的帧。 关键帧是根据美观特性（例如，对比度和稳定性）从整个视频中选择的代表帧。 视频索引器检索关键帧 I 的清单，作为拍摄元数据的一部分，客户可以基于该列表提取关键帧作为高分辨率图像。  

### <a name="extracting-keyframes"></a>提取关键帧

要提取视频的高分辨率关键帧，必须首先上载和索引视频。

![关键 帧](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>使用视频索引器网站

要使用视频索引器网站提取关键帧，请上传和索引视频。 索引作业完成后，单击 **"下载**"按钮并选择 **"项目 "（ZIP）。** 这将将工件文件夹下载到您的计算机。 

![关键 帧](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解压缩并打开文件夹。 在 *_KeyframeThumbnail*文件夹中，您将找到从视频中提取的所有关键帧。 

#### <a name="with-the-video-indexer-api"></a>使用视频索引器 API

要使用视频索引器 API 获取关键帧，请使用[上传视频](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?)呼叫上传和索引视频。 索引作业完成后，调用[获取视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)。 这将为您提供视频索引器从 JSON 文件中的内容中提取的所有见解。  

您将获得关键帧 I 的列表，作为每个镜头元数据的一部分。 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

现在，您需要在[获取缩略图](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?)调用上运行每个关键帧的 ID。 这将将每个关键帧图像下载到您的计算机。 

## <a name="editorial-shot-type-detection"></a>编辑拍摄类型检测

关键帧与输出 JSON 中的镜头相关联。 

与见解 JSON 中的单个拍摄关联的拍摄类型表示其编辑类型。 在将视频编辑成剪辑、预告片或为艺术目的搜索特定类型的关键帧时，您可能会发现这些拍摄类型特征很有用。 根据对每个拍摄的第一个关键帧的分析确定不同类型的类型。 镜头由第一个关键帧中显示的人脸的比例、大小和位置标识。 

拍摄大小和比例根据摄像机与帧中显示的人脸之间的距离来确定。 使用这些属性，视频索引器检测以下拍摄类型：

* 宽：显示整个人的身体。
* 中等：显示一个人的上半身和面部。
* 特写：主要显示一个人的脸。
* 极端特写：显示一个人的脸填充屏幕。 

拍摄类型还可以根据主题字符相对于框架中心的位置来确定。 此属性在视频索引器中定义以下拍摄类型：

* 左面：一个人出现在框架的左侧。
* 中心面：一个人出现在框架的中心区域。
* 右面：一个人出现在框架的右侧。
* 户外：一个人出现在户外环境中。
* 室内：一个人出现在室内环境中。

其他特征：

* 两张照片：显示两张中等大小的人脸。
* 多张面孔：两个以上。


## <a name="next-steps"></a>后续步骤

[检查 API 生成的视频索引器输出](video-indexer-output-json-v2.md#scenes)
