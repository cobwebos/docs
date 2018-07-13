---
title: 情感 API FAQ | Microsoft Docs
description: 获取认知服务中情感 API 的常见问题解答。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365459"
---
# <a name="emotion-api-frequently-asked-questions"></a>情感 API 常见问题解答
 
> [!IMPORTANT]
> 将于 2017 年 10 月 30 日停止为视频 API 预览版提供支持。 请试用全新的[视频索引器 API（预览版）](https://azure.microsoft.com/services/cognitive-services/video-indexer/)。它可以检测口语、人脸、字符和情感，不仅便于从视频中轻松提取见解，还能增强内容发现体验（如搜索结果）。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的情感 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。  

-----

**问**：处理什么类型的图像时，情感 API 的效果最佳？

**答**：为获得最佳效果，请使用无障碍物遮挡的完整正面人脸图像。 如果是部分正面人脸，可靠性会下降。如果图像中的人脸旋转角度不小于 45 度，情感 API 可能无法识别情感。

-----

**问**：情感 API 可以识别多少种情感？

**答**：情感 API 可以识别 8 种公认的不同情感： 
* 高兴
* 悲伤
* 惊喜
* 愤怒
* 害怕
* 蔑视
* 厌恶 
* 中立 

-----

**问**：能否向 API 传入实时视频流，并能同时获取结果？

**答**：使用基于图像的情感 API，并对每一帧调用它或出于性能考虑跳过帧。  可参考视频逐帧分析示例。

-----

**问**：我正在传入二进制图像数据，但却看到“人脸图像无效”错误消息。*

**答**：这表明，算法无法分析图像。  
* 支持的输入图像格式包括 JPEG、PNG、GIF（第一帧）和 BMP。 
* 图像文件不得大于 4MB
* 可检测的人脸大小介于 36x36 像素和 4096x4096 像素范围之间。 无法检测大小超出此范围的人脸
* 一些人脸可能因技术难题而无法检测，例如非常大的人脸角度（头部姿态）和较大遮挡。 正面和近正面人脸的效果最佳

-----
