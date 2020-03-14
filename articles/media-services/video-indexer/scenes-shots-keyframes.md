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
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245936"
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

视频索引器选择最能表示每个拍摄的帧。 关键帧是基于美观属性从整个视频中选择的代表帧（例如，对比度和 stableness）。 视频索引器将在拍摄的元数据中检索关键帧 Id 的列表，具体取决于哪些客户可以将关键帧作为高分辨率图像提取。  

### <a name="extracting-keyframes"></a>提取关键帧

若要提取视频的高分辨率关键帧，必须先上传视频并为其编制索引。

![关键帧](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>视频索引器网站

若要使用视频索引器网站提取关键帧，请上传视频并为其编制索引。 索引作业完成后，单击 "**下载**" 按钮，然后选择 "**项目（ZIP）** "。 这会将项目文件夹下载到你的计算机。 

![关键帧](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解压缩并打开该文件夹。 在 *_KeyframeThumbnail*文件夹中，你将找到从视频中提取的所有关键帧。 

#### <a name="with-the-video-indexer-api"></a>带有视频索引器 API

若要使用视频索引器 API 获取关键帧，请使用[上传视频](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?)呼叫上传和索引视频。 索引作业完成后，调用[获取视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)。 这将为你介绍视频索引器从 JSON 文件中的内容中提取的所有见解。  

你将获得关键帧 Id 列表作为每个拍摄的元数据的一部分。 

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

现在需要在[获取缩略图](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?)调用上运行每个关键帧 id。 这会将每个关键帧映像下载到计算机。 

## <a name="editorial-shot-type-detection"></a>编辑快照类型检测

关键帧与输出 JSON 中的快照相关联。 

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
