---
title: 分析视频和音频文件
titleSuffix: Azure Media Services
description: 了解如何在 Azure 媒体服务中使用 AudioAnalyzerPreset 和 VideoAnalyzerPreset 分析音频和视频内容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 91a09df83c8ba474d3124c3322f4e3dd5eb7367c
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934692"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>通过 Azure 媒体服务分析视频和音频文件

Azure 媒体服务 v3 使你可以通过视频索引器从视频和音频文件中提取见解。 本文介绍用于提取这些见解的媒体服务 v3 分析器预设。 如果需要更详细的见解，请直接使用视频索引器。 若要了解何时使用视频索引器与媒体服务分析器预设，请查看[比较文档](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

若要使用媒体服务 v3 预设来分析内容，请创建一个**转换**，并提交使用以下预设之一的**作业**： [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)或**AudioAnalyzerPreset**。 有关演示如何使用**VideoAnalyzerPreset**的教程，请参阅[使用 Azure 媒体服务分析视频](analyze-videos-tutorial-with-api.md)。

> [!NOTE]
> 使用视频或音频分析器预设时，请通过 Azure 门户将帐户设置为具有 10 个 S3 媒体预留单位。 有关详细信息，请参阅[缩放媒体处理](media-reserved-units-cli-how-to.md)。

## <a name="compliance-privacy-and-security"></a>合规性、隐私性和安全性

作为一项重要提醒，你必须遵守对你使用视频索引器的所有适用法律，并且你不能以违反他人权限的方式使用视频索引器或任何其他 Azure 服务，可能会对其他人造成损害。 在将任何视频（包括任何生物识别数据）上传到视频索引器服务进行处理和存储之前，你必须拥有所有正确的权限，包括来自视频中个人的所有适当的同意。 若要了解 Microsoft[认知服务条款](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)，请参阅有关视频索引器的隐私和安全性。 若要获得 Microsoft 的隐私义务并处理数据，请查看 Microsoft[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[在线服务条款（"OST"）](https://www.microsoft.com/licensing/product-licensing/products)和[数据处理补充](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)条款（"DPA"）。 其他隐私信息（包括数据保留、删除/销毁）在 OST 和[此处](../video-indexer/faq.md)提供。 通过使用视频索引器，你同意遵守认知服务条款、OST、DPA 和隐私声明。

## <a name="built-in-presets"></a>内置预设

媒体服务当前支持以下内置分析器预设：  

|**预设名称**|**方案**|**详细信息**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|分析音频|该预设应用一组基于 AI 的预定义分析操作，其中包括语音听录。 目前，该预设支持处理包含单个音轨的内容且音轨应仅包含一种语言的语音。 可以使用 BCP-47 格式“language tag-region”为输入中的音频有效负载指定语言。 支持的语言为英语（"en-us" 和 "en-us"）、西班牙语（"es" 和 "es-MX"）、法语（"fr"）、意大利语（"it"）、日语（"ja-jp"）、葡萄牙语（"pt-BR"）、中文（"zh-chs"）、德语（"de"）、阿拉伯语（"ar-示例" 和 "ar-SY"）、俄语（"ru-RU"）、印地语（"hi"）和朝鲜语（"ko-KR"）。<br/><br/> 如果未指定语言或未将其设置为 null，则自动语言检测会选择检测到的第一种语言，并在文件的持续时间内继续选择所选语言。 自动语言检测功能目前支持英语、中文、法语、德语、意大利语、日语、西班牙语、俄语和葡萄牙语。 它不支持在检测到第一种语言后动态切换语言。 自动语言检测功能最适用于包含清晰可辨的语音的录音。 如果自动语言检测无法找到语言，则脚本会退回到英语。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|分析音频和视频|从音频和视频中提取见解（丰富的元数据），并输出 JSON 格式的文件。 可以指定在处理视频文件时是否只想提取音频见解。 有关详细信息，请参阅[分析视频](analyze-videos-tutorial-with-api.md)。|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|检测视频中的人脸|描述在分析视频时要使用的设置，以检测当前存在的所有表面。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

使用此预设，可以从音频或视频文件中提取多个音频见解。 输出包括一个 JSON 文件（包含所有见解）和该音频脚本的 VTT 文件。 此预设接受 [BCP47](https://tools.ietf.org/html/bcp47) 字符串格式的属性，该属性用于指定输入文件的语言。 音频见解包括：

* **音频**脚本：带有时间戳的口述字词的脚本。 支持多种语言。
* **演讲者索引**：扬声器的映射和相应的口述字词。
* **语音情绪分析**：对音频操作执行的情绪分析的输出。
* **关键字**：从音频脚本中提取的关键字。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

使用此预设，可以从视频中提取多个音频和视频见解。 输出包括一个 JSON 文件（包含所有见解）、该视频脚本的 VTT 文件以及视频缩略图集合。 此预设还接受 [BCP47](https://tools.ietf.org/html/bcp47) 字符串格式的属性，该属性表示视频的语言。 视频见解包括上述所有音频见解，此外还包含以下项：

* **面部跟踪**：视频中出现面部的时间。 每个面部都有一个人脸 ID 和一个对应的缩略图集。
* **视觉对象文本**：通过光学字符识别检测到的文本。 文本带有时间戳，还用于提取关键字（除了音频脚本之外）。
* **关键帧**：从视频中提取的关键帧的集合。
* **视觉对象内容裁决**：本质上标记为成人或猥亵的视频部分。
* **批注**：基于预定义的对象模型对视频进行批注的结果

## <a name="insightsjson-elements"></a>insights.json 元素

输出包括一个 JSON 文件（insights），其中包含在视频或音频中找到的所有见解。 JSON 可能包含以下元素：

### <a name="transcript"></a>脚本

|名称|Description|
|---|---|
|id|行 ID。|
|text|脚本本身。|
|语言|脚本语言。 旨在支持每行语言不同的脚本。|
|实例|出现该行的时间范围列表。 如果实例是脚本，则只有 1 个实例。|

示例：

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|名称|Description|
|---|---|
|id|OCR 行 ID。|
|text|OCR 文本。|
|confidence|识别置信度。|
|语言|OCR 语言。|
|实例|出现此 OCR 的时间范围列表（同一 OCR 可重复多次出现）。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>人脸

|名称|Description|
|---|---|
|id|人脸 ID。|
|name|人脸姓名。 它可以是 "Unknown #0"、标识名人或客户训练的人员。|
|confidence|人脸识别置信度。|
|description|名人的说明。 |
|thumbnailId|该人脸的缩略图 ID。|
|knownPersonId|内部 ID （如果是已知的人员）。|
|referenceId|必应 ID （如果是必应名人）。|
|referenceType|当前仅限必应。|
|title|标题（如果是名人，如 "Microsoft CEO"）。|
|imageUrl|如果是名人，则为图像 URL。|
|实例|人脸出现在给定时间范围内的实例。 每个实例还具有一个 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>截图

|名称|Description|
|---|---|
|id|截图 ID。|
|keyFrames|截图内的关键帧列表（每个关键帧都有一个 ID 和实例时间范围列表）。 关键帧实例具有一个 thumbnailId 字段，该字段包含关键帧的缩略图 ID。|
|实例|此截图的时间范围列表（截图仅有 1 个实例）。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|名称|Description|
|---|---|
|CorrespondenceCount|视频中对应关系的数目。|
|WordCount|每个发言人的单词数。|
|SpeakerNumberOfFragments|发言人在视频中的片段数量。|
|SpeakerLongestMonolog|发言人的最长独白。 如果演讲者在包含的 monolog 内有静默处理。 删除独白开头和结尾的沉默期。|
|SpeakerTalkToListenRatio|计算方式为将发言人的独白时间（开头和结尾之间没有沉默期）除以视频总时间。 时间将四舍五入为三位小数。|


### <a name="sentiments"></a>情绪

情绪依据其 sentimentType 字段得出（积极/中立/消极）。 例如：0-0.1、0.1-0.2。

|名称|Description|
|---|---|
|id|情绪 ID。|
|averageScore |该情绪类型的所有实例的所有分数的均值 - 积极/中立/消极|
|实例|出现此情绪的时间范围列表。|
|sentimentType |类型可以是“Positive”、“Neutral”或“Negative”。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>标签

|名称|Description|
|---|---|
|id|标签 ID。|
|name|标签名称（例如“计算机”、“电视”）。|
|语言|标签名称语言（转换后）。 BCP-47|
|实例|出现此标签的时间范围列表（一个标签可重复多次出现）。 每个实例都有置信度字段。 |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>关键字

|名称|Description|
|---|---|
|id|关键字 ID。|
|text|关键字文本。|
|confidence|关键字的识别置信度。|
|语言|关键字语言（转换后）。|
|实例|出现此关键字的时间范围列表（一个关键字可重复多次出现）。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 块包含视频索引器找到的、可能具有成人内容的时间范围。 如果 visualContentModeration 为空，则不会识别成人内容。

被确定包含成人或不雅内容的视频可能仅可供私人观看。 用户可以提交内容的人工审阅请求，在这种情况下，`IsAdult` 属性将包含人工审阅的结果。

|名称|Description|
|---|---|
|id|视觉内容审核 ID。|
|adultScore|成人内容评分（由内容审核员提供）。|
|racyScore|不雅内容评分（由内容审核员提供）。|
|实例|显示此视觉内容审核的时间范围列表。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>后续步骤

[教程：使用 Azure 媒体服务分析视频](analyze-videos-tutorial-with-api.md)
