---
title: 常见问题解答 - 情感 API
titlesuffix: Azure Cognitive Services
description: 获取有关情感 API 的常见问题的解答。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238503"
---
# <a name="emotion-api-frequently-asked-questions"></a>情感 API 常见问题解答

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分正式发布。

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的情感 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。  

-----

**问**：处理什么类型的图像时，情感 API 的效果最佳？

**答**：为获得最佳效果，请使用无障碍物遮挡的完整正面人脸图像。 如果是部分正面人脸，可靠性会下降。如果图像中的人脸旋转角度大于 45 度，情感 API 可能无法识别情感。

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

**问**：*我正在传入二进制图像数据，但却看到“人脸图像无效”错误消息。**

**答**：此消息表明，算法无法分析图像。  
* 支持的输入图像格式包括 JPEG、PNG、GIF（第一帧）和 BMP
* 图像文件不得大于 4 MB
* 可检测的人脸大小范围为 36 x 36 到 4096 x 4096 像素。 无法检测超出此范围的人脸
* 某些面部可能因技术难题而无法检测，例如非常大的面部角度（头部姿势），以及较大的阻挡物。 正面和近正面人脸的效果最佳

-----
