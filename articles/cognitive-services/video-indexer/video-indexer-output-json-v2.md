---
title: 探讨 v2 API 生成的 Azure 视频索引器输出 | Microsoft Docs
description: 本主题探讨 v2 API 生成的视频索引器输出。
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 43cc02417fad8a2fa46bd309235951393cd55b8a
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "40187363"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>探讨 v2 API 生成的视频索引器输出

> [!Note]
> 视频索引器 V1 API 已于 2018 年 8 月 1 日弃用。 从现在起，应该使用视频索引器 v2 API。 <br/>若要使用视频索引器 v2 API 进行开发，请参阅[此处](https://api-portal.videoindexer.ai/)的说明。 

调用“获取视频索引”API 时，如果响应状态为 OK，则你会获得详细的 JSON 输出（响应内容）。 JSON 内容包含指定的视频见解的详细信息。 见解包括脚本、ocr、人脸、主题、块等维度。维度包含视频中出现每个维度时显示的时间范围实例。  

此外，可以通过在视频索引器门户中的视频上按“播放”按钮，来直观检查视频的汇总见解。 有关详细信息，请参阅[查看和编辑视频见解](video-indexer-view-edit.md)。

![洞察力](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

本文探讨“获取视频索引”API 返回的 JSON 内容。 

> [!NOTE]
> 视频索引器中所有访问令牌的有效期为一小时。


## <a name="root-elements"></a>根元素

|名称|Description|
|---|---|
|accountId|播放列表的 VI 帐户 ID。|
|id|播放列表的 ID。|
|名称|播放列表的名称。|
|description|播放列表的说明。|
|userName|创建播放列表的用户名。|
|created|播放列表的创建时间。|
|privacyMode|播放列表的隐私模式（私用/公共）。|
|state|播放列表的状态（已上传、正在处理、已处理、失败、已隔离）。|
|isOwned|指示播放列表是否由当前用户创建。|
|isEditable|指示当前用户是否有权编辑播放列表。|
|isBase|指示该播放列表是基础播放列表（视频），还是由其他视频构成的播放列表（派生）。|
|durationInSeconds|播放列表的总持续时间。|
|summarizedInsights|包含一个 [summarizedInsights](#summarizedinsights)。
|videos|构成播放列表的[视频](#videos)列表。<br/>如果此播放列表由其他视频的时间范围构成（派生），则此列表中的视频仅包含这些时间范围的数据。|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

本部分介绍见解的摘要。

|属性 | Description|
|---|---|
|名称|视频的名称。 例如 Azure Monitor。|
|shortId|视频的 ID。 例如 63c6d532ff。|
|privacyMode|可以细分为以下模式之一：“私用”、“公共”。 **公共** - 向你帐户中的任何人，以及具有视频链接的每个人显示该视频。 **私用** - 向你帐户中的每个人显示该视频。|
|duration|包含一个持续时间，用于描述见解发生的时间。 持续时间以秒为单位。|
|thumbnailVideoId|从其创建缩略图的视频的 ID。
|thumbnailId|视频的缩略图 ID。 若要获取实际缩略图，请调用 Get-Thumbnail (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) 并为其传递 thumbnailVideoId 和 thumbnailId。|
|人脸|可以包含零个或多个人脸。 有关更详细的信息，请参阅 [faces](#faces)。|
|关键字|可以包含零个或多个关键字。 有关更详细的信息，请参阅 [keywords](#keywords)。|
|情绪|可以包含零个或多个情绪。 有关更详细的信息，请参阅 [sentiments](#sentiments)。|
|audioEffects| 可以包含零个或多个音效。 有关更详细的信息，请参阅 [audioEffects](#audioeffects)。|
|标签| 可以包含零个或多个标签。 有关更详细的信息，请参阅 [labels](#labels)。|
|brands| 可以包含零个或多个品牌。 有关更详细的信息，请参阅 [brands](#brands)。|
|statistics | 有关更详细的信息，请参阅 [statistics](#statistics)。|

## <a name="videos"></a>videos

|名称|Description|
|---|---|
|accountId|视频的 VI 帐户 ID。|
|id|视频的 ID。|
|名称|视频的名称。
|state|视频的状态（已上传、正在处理、已处理、失败、已隔离）。|
|processingProgress|处理进度（例如 20%）。|
|failureCode|无法处理时显示的失败代码（例如“UnsupportedFileType”）。|
|failureMessage|无法处理时显示的失败消息。|
|externalId|视频的外部 ID（如果用户已指定）。|
|externalUrl|视频的外部 URL（如果用户已指定）。|
|metadata|视频的外部元数据（如果用户已指定）。|
|isAdult|指示视频是否已经过人工审查，并已标识为成人视频。|
|insights|见解对象。 有关详细信息，请参阅 [insights](#insights)。|
|thumbnailId|视频的缩略图 ID。 若要获取实际缩略图，请调用 Get-Thumbnail (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) 并为其传递视频 ID 和 thumbnailId。|
|publishedUrl|用于流式传输视频的 URL。|
|publishedUrlProxy|要从中流式传输视频的 URL（适用于 Apple 设备）。|
|viewToken|用于流式传输视频的短期查看令牌。|
|sourceLanguage|视频的源语言。|
|语言|视频的实际语言（翻译）。|
|indexingPreset|用于编制视频索引的预设。|
|streamingPreset|用于发布视频的预设。|
|linguisticModelId|用于转录视频的 CRIS 模型。|
|statistics | 有关详细信息，请参阅 [statistics](#statistics)。|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

见解是一组维度（例如，脚本行、人脸、品牌等），其中的每个维度是唯一元素（例如，face1、face2、face3）的列表，每个元素有自身的元数据及其实例（具有其他可选元数据的时间范围）的列表。

人脸可能具有 ID、名称、缩略图、其他元数据及其时态实例的列表（例如：00:00:05 – 00:00:10、00:01:00 - 00:02:30 和 00:41:21 – 00:41:49。）每个时态实例可以包含其他元数据。 例如，人脸的矩形坐标 (20,230,60,60)。

|版本|代码版本|
|---|---|
|sourceLanguage|视频的源语言（采用一种主要语言）。 格式为 [BCP-47](https://tools.ietf.org/html/bcp47) 字符串。|
|语言|见解语言（从源语言翻译）。 格式为 [BCP-47](https://tools.ietf.org/html/bcp47) 字符串。|
|脚本|[transcript](#transcript) 维度。|
|ocr|[ocr](#ocr) 维度。|
|关键字|[keywords](#keywords) 维度。|
|blocks|可以包含一个或多个[块](#blocks)|
|人脸|[faces](#faces) 维度。|
|标签|[labels](#labels) 维度。|
|截图|[shots](#shots) 维度。|
|brands|[brands](#brands) 维度。|
|audioEffects|[audioEffects](#audioEffects) 维度。|
|情绪|[sentiments](#sentiments) 维度。|
|visualContentModeration|[visualContentModeration](#visualcontentmoderation) 维度。|
|textualConentModeration|[textualConentModeration](#textualconentmoderation) 维度。|

示例：

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualConentModeration": ...
}
```

#### <a name="blocks"></a>blocks

属性 | Description
---|---
id|块的 ID。|
instances|此块的时间范围列表。|

#### <a name="transcript"></a>脚本

|名称|Description|
|---|---|
|id|行 ID。|
|text|脚本本身。|
|语言|脚本语言。 旨在支持每行语言不同的脚本。|
|instances|出现该行的时间范围列表。 如果实例是脚本，则只有 1 个实例。|

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

#### <a name="ocr"></a>ocr

|名称|Description|
|---|---|
|id|OCR 行 ID。|
|text|OCR 文本。|
|confidence|识别置信度。|
|语言|OCR 语言。|
|instances|出现此 OCR 的时间范围列表（同一 OCR 可重复多次出现）。|

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

#### <a name="keywords"></a>关键字

|名称|Description|
|---|---|
|id|关键字 ID。|
|text|关键字文本。|
|confidence|关键字的识别置信度。|
|语言|关键字语言（转换后）。|
|instances|出现此关键字的时间范围列表（一个关键字可重复多次出现）。|

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

#### <a name="faces"></a>人脸

|名称|Description|
|---|---|
|id|人脸 ID。|
|名称|人脸姓名。 可以为“Unknown #0”、公认的名人或客户训练的人。|
|confidence|人脸识别置信度。|
|description|名人的说明。 |
|thumbnalId|该人脸的缩略图 ID。|
|knownPersonId|如果是已知人员，则为此人的内部 ID。|
|referenceId|如果是必应名人，则为此人的必应 ID。|
|referenceType|当前仅限必应。|
|title|如果是名人，则为此人的头衔（例如“Microsoft 的 CEO”）。|
|imageUrl|如果是名人，则为此人的图像 URL。|
|instances|在给定时间范围内出现该人脸的实例。 每个实例还具有一个 thumbnailsId。 |

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

#### <a name="labels"></a>标签

|名称|Description|
|---|---|
|id|标签 ID。|
|名称|标签名称（例如“计算机”、“电视”）。|
|语言|标签名称语言（转换后）。 BCP-47|
|instances|出现此标签的时间范围列表（一个标签可重复多次出现）。 每个实例都有置信度字段。 |


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

#### <a name="shots"></a>截图

|名称|Description|
|---|---|
|id|截图 ID。|
|keyFrames|截图内的关键帧列表（每个关键帧都有一个 ID 和实例时间范围列表）。 关键帧实例具有一个 thumbnailId 字段，该字段包含关键帧的缩略图 ID。|
|instances|此截图的时间范围列表（截图仅有 1 个实例）。|

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

#### <a name="brands"></a>brands

在语音转文本脚本和/或视频 OCR 中检测到的企业和产品品牌名称。 这不包括品牌或徽标检测内容的视觉辨识形式。

|名称|Description|
|---|---|
|id|品牌 ID。|
|名称|品牌名称。|
|referenceId | 品牌维基百科 URL 的后缀。 例如，“Target_Corporation”是 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 的后缀。
|referenceUrl | 品牌的维基百科 URL（如果存在）。 例如，[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)。
|description|品牌说明。|
|标记|与此品牌关联的预定义标记的列表。|
|confidence|视频索引器品牌检测器的置信度值 (0-1)。|
|instances|此品牌的时间范围列表。 每个实例有一个 brandType，表示此品牌是出现在脚本还是 OCR 中。|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistics

|名称|Description|
|---|---|
|CorrespondenceCount|视频中对应关系的数目。|
|WordCount|每个发言人的单词数。|
|SpeakerNumberOfFragments|发言人在视频中的片段数量。|
|SpeakerLongestMonolog|发言人的最长独白。 如果发言人在独白中有沉默，则会将沉默期包含在内。 删除独白开头和结尾的沉默期。| 
|SpeakerTalkToListenRatio|计算方式为将发言人的独白时间（开头和结尾之间没有沉默期）除以视频总时间。 时间将四舍五入为三位小数。|

#### <a name="audioeffects"></a>audioEffects

|名称|Description|
|---|---|
|id|音频效果 ID。|
|type|音频效果类型（例如鼓掌、语音、静音）。|
|instances|出现此音频效果的时间范围列表。|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>情绪

情绪依据其 sentimentType 字段得出（积极/中立/消极）。 例如：0-0.1、0.1-0.2。

|名称|Description|
|---|---|
|id|情绪 ID。|
|averageScore |该情绪类型的所有实例的所有分数的均值 - 积极/中立/消极|
|instances|出现此情绪的时间范围列表。|
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 块包含视频索引器找到的、可能具有成人内容的时间范围。 如果 visualContentModeration 为空，则表示未识别到成人内容。

找到的包含成人或猥亵内容的视频可能仅供私人观看。 用户可以选择请求人工审查内容，在这种情况下，IsAdult 属性将包含人工审查的结果。

|名称|Description|
|---|---|
|id|视觉内容审核 ID。|
|adultScore|成人内容评分（由内容审核员提供）。|
|racyScore|猥亵内容评分（由内容审核员提供）。|
|instances|显示此视觉内容审核的时间范围列表。|

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

#### <a name="textualconentmoderation"></a>textualConentModeration 

|名称|Description|
|---|---|
|id|文本内容审核 ID。|
|bannedWordsCount |受禁单词的数目。|
|bannedWordsRatio |占单词总数的比。|


## <a name="next-steps"></a>后续步骤

[视频索引器 API](https://api-portal.videoindexer.ai)

有关如何在应用程序中嵌入小组件的信息，请参阅[将视频索引器小组件嵌入应用程序](video-indexer-embed-widgets.md)。 

