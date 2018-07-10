---
title: 情感 API 概述 | Microsoft Docs
description: 通过认知服务中的情感 API 使用 Microsoft 先进的、基于云的情感识别算法来构建更加个性化的应用。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365788"
---
# <a name="what-is-emotion-api"></a>什么是情感 API？

> [!IMPORTANT]
> 情感 API 已在 2017 年 10 月 30 日弃用。 其功能现在是[人脸 API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/) 的一部分。

欢迎使用 Microsoft 情感 API，这允许使用 Microsoft 的基于云的情感识别算法来构建更加个性化的应用。

### <a name="emotion-recognition"></a>情感识别

情感 API beta 版采用图像作为输入，并返回图像中的每张脸与一组情感对应的置信度，以及来自人脸 API 的面部边界框。 可检测到的情感包括快乐、悲伤、惊讶、愤怒、恐惧、轻蔑、厌恶或中性。 这些情感通过相同的基本面部表情（可由情感 API 识别）以跨文化的通用方式进行沟通。 

**解释结果：** 

在解释来自情感 API 的结果时，检测到的情感应该解释为得分最高的情感，因为评分已规范化，以便归结出一种情感。 用户可以根据其需求在应用程序中设置一个更高的置信度阈值。 

有关情感检测的详细信息，请参阅 API 参考： 
  * 基本：如果用户已调用了人脸 API，则可以提交人脸矩形作为输入并使用基本层。 [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * 标准：如果用户未提交人脸矩形，则应当使用标准模式。  [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

有关如何使用情感 API 解释流式处理视频的示例，请参阅[如何实时分析视频](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion)。
