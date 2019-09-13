---
title: 通过视频索引器自动标识口述语言-Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器语言标识模型来自动识别视频中的语言。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 843b92d3fe34d592b39cd86ece447fef2ff9af67
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931117"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>通过语言标识模型自动标识口述语言

视频索引器支持自动语言标识（盖子），这是自动识别音频中的语言内容并发送以主要标识语言转录的媒体文件的过程。 当前盖子支持英语、西班牙语、法语、德语、意大利语、简体中文、日语、俄语和葡萄牙语（巴西）。 

## <a name="choosing-auto-language-identification-on-indexing"></a>为索引选择自动语言识别

使用 API 为视频编制索引或[重新创建索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)时，请在`auto detect` `sourceLanguage`参数中选择选项。

使用门户时，请在[视频索引器](https://www.videoindexer.ai/)主页上，中转到你的**帐户视频**，并将鼠标悬停在要对其重新编制索引的视频的名称上。 在右下角单击 "重新索引" 按钮。 在 "**重新索引视频**" 对话框中，从 "**视频源语言**" 下拉框中选择 "*自动检测*"。

![自动检测](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型输出

如果此语言的置信度为，则视频索引器会`> 0.6`根据最可能的语言转录视频。 如果无法自信地识别语言，则假定语言为英语。 

可以将 insights JSON 中的模型主导语言作为`sourceLanguage`属性（位于根/视频/见解下）提供。 `sourceLanguageConfidence`属性下也提供了相应的置信度分数。

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

* 支持的语言包括英语、西班牙语、法语、德语、意大利语、简体中文、日语、俄语和巴西葡萄牙语。
* 如果音频包含除以上支持的列表之外的其他语言，则结果为意外的结果。
* 如果视频索引器无法识别出足够置信度（`>0.6`）的语言，则回退语言为英语。
* 对于带有混合语言音频的文件没有当前支持。 如果音频包含混合语言，则结果为意外的结果。 
* 低质量音频苛刻可能会影响模型结果。
* 该模型在音频中要求至少一分钟的语音。
* 该模型用于识别自发对话语音（非语音命令、唱歌等）。

## <a name="next-steps"></a>后续步骤

* [概述](video-indexer-overview.md)
* [自动识别和转录多语言内容](multi-language-identification-transcription.md)
