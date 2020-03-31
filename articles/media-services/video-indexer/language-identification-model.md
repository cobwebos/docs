---
title: 使用视频索引器自动识别口语 - Azure
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器语言标识模型自动标识视频中的口语。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 7a2e03b8dacbf6c3ff20e02c804804b671e86d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513875"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>使用语言识别模型自动识别口语

视频索引器支持自动语言识别 （LID），这是从音频自动识别口语内容以及发送媒体文件以主要标识语言转录的过程。 目前，LID 支持英语、西班牙语、法语、德语、意大利语、中文（简体）、日语、俄语和葡萄牙语（巴西语）。 

## <a name="choosing-auto-language-identification-on-indexing"></a>在索引时选择自动语言标识

使用 API 对视频编制索引或[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)时，在参数中选择`auto detect`该`sourceLanguage`选项。

使用门户时，转到[视频索引器](https://www.videoindexer.ai/)主页上的**帐户视频**，并将鼠标悬停在要重新索引的视频名称上。 在右下角单击重新索引按钮。 在 **"重新索引视频"** 对话框中，从 **"视频源语言**"下拉框中选择 *"自动检测*"。

![自动检测](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>模型输出

如果对该语言的信心是`> 0.6`，则视频索引器会根据最可能的语言转录视频。 如果语言不能自信地识别，则假定口语是英语。 

模型主导语言在见解 JSON 中作为`sourceLanguage`属性（在根/视频/见解下）提供。 属性下`sourceLanguageConfidence`也提供了相应的置信度分数。

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

* 支持的语言包括英语、西班牙语、法语、德语、意大利语、中文（简体）、日语、俄语和巴西葡萄牙语。
* 如果音频包含上面支持列表以外的语言，则结果是意外的。
* 如果视频索引器无法以足够高的置信度 （）`>0.6`标识语言），则回退语言为英语。
* 当前不支持具有混合语言音频的文件。 如果音频包含混合语言，则结果为意外。 
* 低质量音频可能会影响模型结果。
* 该模型需要音频中至少一分钟的语音。
* 该模型旨在识别自发的对话语音（而不是语音命令、唱歌等）。

## <a name="next-steps"></a>后续步骤

* [概述](video-indexer-overview.md)
* [自动识别和转录多语言内容](multi-language-identification-transcription.md)
