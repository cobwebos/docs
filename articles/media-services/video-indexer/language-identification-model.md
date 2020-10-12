---
title: 使用视频索引器自动标识口述语言-Azure
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器语言标识模型来自动识别视频中的语言。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81687125"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>通过语言识别模型自动识别口述语言

视频索引器支持自动语言标识 (盖子) ，这是从音频自动标识口述语言内容并发送要以主要标识语言转录的媒体文件的过程。 

目前的盖子支持：英语、西班牙语、法语、德语、意大利语、普通话中文、日语、俄语和葡萄牙语 (巴西) 。 

请确保查看下面的 " [准则和限制](#guidelines-and-limitations) " 部分。

## <a name="choosing-auto-language-identification-on-indexing"></a>为索引选择自动语言识别

使用 API 为视频编制索引或 [重新创建索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 时，请 `auto detect` 在参数中选择选项 `sourceLanguage` 。

使用门户时，请在[视频索引器](https://www.videoindexer.ai/)主页上，中转到你的**帐户视频**，并将鼠标悬停在要对其重新编制索引的视频的名称上。 在右下角单击 "重新索引" 按钮。 在 "**重新索引视频**" 对话框中，从 "**视频源语言**" 下拉框中选择 "*自动检测*"。

![自动检测](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型输出

如果此语言的置信度为，则视频索引器会根据最可能的语言转录视频 `> 0.6` 。 如果无法自信地识别语言，则假定语言为英语。 

在 " `sourceLanguage` 根/视频/见解) 下，模型主导语言在" (的属性 "中提供。 属性下也提供了相应的置信度分数 `sourceLanguageConfidence` 。

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>指导原则和限制

* 自动语言标识 (盖子) 支持以下语言： 

    英语、西班牙语、法语、德语、意大利语、普通话 Chines、日语、俄语和葡萄牙语 (巴西) 。
* 尽管视频索引器支持阿拉伯 (新式标准和 Levantine) 、印地语和韩语，但在盖子中不支持这些语言。
* 如果音频包含除以上支持的列表之外的其他语言，则结果为意外的结果。
* 如果视频索引器无法识别出足够置信度 () 的语言 `>0.6` ，则回退语言为英语。
* 对于带有混合语言音频的文件没有当前支持。 如果音频包含混合语言，则结果为意外的结果。 
* 低质量音频可能会影响模型结果。
* 该模型在音频中要求至少一分钟的语音。
* 该模型用于识别自发对话语音 (不会 ) 语音命令、唱歌等。

## <a name="next-steps"></a>后续步骤

* [概述](video-indexer-overview.md)
* [自动识别和转录多语言内容](multi-language-identification-transcription.md)
