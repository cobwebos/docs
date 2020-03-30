---
title: 使用视频索引器自动识别和转录多语言内容
titleSuffix: Azure Media Services
description: 本主题演示如何使用视频索引器自动识别和转录多语言内容。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968739"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>自动识别和转录多语言内容（预览）

视频索引器支持多语言内容中的自动语言识别和转录。 此过程涉及从音频自动识别不同段中的口语，发送要转录的每个介质文件的段，并将转录合并回一个统一转录。 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>使用门户索引时选择多语言标识

在上传视频并编制索引视频时，您可以选择**多语言检测**。 或者，您可以在重新索引视频时选择**多语言检测**。 以下步骤描述了如何重新编制索引：

1. 浏览到[视频索引器](https://vi.microsoft.com/)网站并登录。
1. 转到 **"库"** 页面，并将鼠标悬停在要重新索引的视频名称上。 
1. 在右下角，单击 **"重新索引视频**"按钮。 
1. 在 **"重新索引视频"对话框**中，从 **"视频源语言**"下拉框中选择**多语言检测**。

    * 当视频索引为多语言时，见解页将包含该选项，并会出现其他见解类型，使用户能够查看以哪种语言"口语"转录的分段。
    * 从多语言成绩单中可以完全翻译所有语言。
    * 所有其他见解将以检测到的主语言显示- 即音频中出现最多的语言。
    * 播放器上的隐藏字幕也可使用多语言版本。

![门户体验](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>使用 API 索引时选择多语言标识

使用 API 对视频编制索引或[重新编制索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)时，在`multi-language detection`参数中选择该`sourceLanguage`选项。

### <a name="model-output"></a>模型输出

模型将在一个列表中检索视频中检测到的所有语言

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

此外，转录部分中的每个实例都将包括转录它的语言

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

## <a name="guidelines-and-limitations"></a>指导原则和限制

* 支持语言集：英语、法语、德语、西班牙语。
* 支持最多包含三种受支持语言的多语言内容。
* 如果音频包含上面支持列表以外的语言，则结果是意外的。
* 每种语言检测的段长度最小 = 15 秒。
* 语言检测偏移量平均为 3 秒。
* 演讲是连续的。 语言之间的频繁交替可能会影响模型的性能。
* 非母语人士的语音可能会影响模型性能（例如，当使用者使用母语并切换到其他语言时）。
* 该模型旨在识别具有合理音频声学（而不是语音命令、唱歌等）的自发对话语音。
* 项目创建和编辑目前不适用于多语言视频。
* 使用多语言检测时，自定义语言模型不可用。
* 不支持添加关键字。
* 导出隐藏字幕文件时，语言指示将不会显示。
* 更新脚本 API 不支持多种语言文件。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
