---
title: 常见问题解答 - 人脸 API
titlesuffix: Azure Cognitive Services
description: 获取人脸 API 服务的最常见问题解答。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351320"
---
# <a name="face-api-frequently-asked-questions"></a>人脸 API 常见问题解答

> [!TIP]
> 如果本 FAQ 无法解答你的问题，请尝试在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上的人脸 API 服务社区中提问，或在 [UserVoice](https://cognitive.uservoice.com/) 上联系帮助和支持人员。

-----
**问**：因素有哪些可以减少用于识别、 验证或查找相似人脸 API 的准确性？

**答**：通常它是相同的用例，人们难以识别某人包括：
* 障碍物挡住一只或两只眼睛
* Harsh 照明 （背景光等严重）
* 发型或面部毛发变化
* 年龄产生的变化
* 极端的面部表情 （例如尖叫）

人脸 API 通常是在类似上面，具有挑战性的情况下成功，但准确性可能降低。 若要提高识别可靠性并应对这些挑战，请使用包括多种角度和光线的照片来定型 Person。

-----
**问**：我正在传入二进制图像数据，但却看到“人脸图像无效”错误消息。

**答**：此错误意味着该算法分析图像时出现问题。 原因包括：
* 支持的输入图像格式包括 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 4 MB
* 可检测的人脸大小介于 36x36 像素和 4096x4096 像素范围之间。 无法检测超出此范围的人脸
* 某些面部可能检测由于技术难题，例如大的面部角度 （头部姿势），较大的阻挡物。 正面和近正面人脸的效果最佳

-----
