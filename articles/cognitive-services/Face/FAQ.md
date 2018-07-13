---
title: 人脸 API 服务常见问题解答 | Microsoft Docs
description: 获取人脸 API 服务的最常见问题解答。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365453"
---
# <a name="face-api-frequently-asked-questions"></a>人脸 API 常见问题解答
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的人脸 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。

-----
**问**：哪些因素会降低人脸 API 的识别、验证或查找相似这些功能的准确性？

**答**：这通常与肉眼难以识别某人的情况相同，具体包括以下情形：
* 障碍物挡住一只或两只眼睛
* 刺眼的光（例如，严重逆光）
* 发型或面部毛发变化
* 年龄产生的变化
* 极端面部表情（例如，尖叫）

人脸 API 在这些棘手情况下通常都很成功，但准确性可能会降低。 若要提高识别可靠性并应对这些挑战，请使用包括多种角度和光线的照片来定型 Person。

-----
**问**：我正在传入二进制图像数据，但却看到“人脸图像无效”错误消息。

**答**：这表明，算法无法分析图像。 原因包括：
* 支持的输入图像格式包括 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 4MB
* 可检测的人脸大小介于 36x36 像素和 4096x4096 像素范围之间。 无法检测大小超出此范围的人脸
* 一些人脸可能因技术难题而无法检测，例如非常大的人脸角度（头部姿态）和较大遮挡。 正面和近正面人脸的效果最佳

-----
