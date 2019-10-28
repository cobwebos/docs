---
title: 通过视频索引器自动识别和转录多语言内容
titleSuffix: Azure Media Services
description: 本主题演示如何通过视频索引器自动识别和转录多语言内容。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968739"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>自动识别和转录多语言内容（预览版）

视频索引器支持自动语言标识和在多语言内容中进行脚本。 此过程涉及在音频中自动识别不同段中的口述语言，发送要转录的媒体文件的每个段并将其合并回一个统一脚本。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>选择针对门户编制索引的多语言标识

可在上传和索引视频时选择**多语言检测**。 或者，你可以在重新索引视频时选择**多语言检测**。 以下步骤说明如何重新索引：

1. 浏览到[视频索引器](https://vi.microsoft.com/)网站并登录。
1. 请在 "**库**" 页上，将鼠标悬停在要重新索引的视频的名称上。 
1. 在右下角，单击 "**重新索引视频**" 按钮。 
1. 在 "**重新索引视频**" 对话框中，从 "**视频源语言**" 下拉框中选择 "**多语言检测**"。

    * 当某个视频被索引为多语言时，"见解" 页将包含该选项，并显示其他见解类型，使用户能够查看哪个段是转录的语言 "口头 language"。
    * 可以从多语言脚本完全提供所有语言的翻译。
    * 所有其他见解都将以所检测到的主语言显示-这是最常出现在音频中的语言。
    * 播放机上的隐藏字幕也可用于多语言。

![门户体验](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>针对 API 的索引选择多语言识别

使用 API 为视频编制[索引或重新创建索引时](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)，请在 `sourceLanguage` 参数中选择 `multi-language detection` 选项。

### <a name="model-output"></a>模型输出

该模型将检索在一个列表中检测到的所有语言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，脚本部分中的每个实例都将包含它的转录语言。

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>指导原则和限制条件

* 一组受支持的语言：英语、法语、德语、西班牙语。
* 支持多语言内容，最多支持三种语言。
* 如果音频包含除以上支持的列表之外的其他语言，则结果为意外的结果。
* 为每种语言检测的最小段长度-15 秒。
* 语言检测偏移平均是3秒。
* 语音应是连续的。 语言间的频繁分支结构可能会影响模型的性能。
* 非本机扬声器的语音可能会影响模型性能（例如，当发言人使用其母语，并切换到另一种语言）时。
* 该模型用于识别具有合理音频噪声（非语音命令、唱歌等）的自发对话语音。
* 项目创建和编辑当前不适用于多语言视频。
* 使用多语言检测时，自定义语言模型不可用。
* 不支持添加关键字。
* 导出隐藏式字幕文件时，不会显示语言指示。
* 更新脚本 API 不支持多语言文件。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
