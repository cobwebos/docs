---
title: 探讨 Azure 视频索引器输出 | Microsoft Docs
description: 本主题探讨视频索引器输出。
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366897"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>探讨 v1 API 生成的视频索引器输出

> [!Note]
> 视频索引器 V1 API 现已弃用，将于 2018 年 8 月 1 日删除。 你应该开始使用视频索引器 v2 API，以免出现中断。
>
> 若要使用视频索引器 v2 API 进行开发，请参阅[此处](https://api-portal.videoindexer.ai/)的说明。 

调用“获取明细”API 时，如果响应状态为“正常”，则会获得详细的 JSON 输出（响应内容）。 JSON 内容包含指定的视频见解的详细信息（包括脚本、OCR、人物）。 详细信息包括关键字（主题）、人脸、块。 每个块包括时间范围、脚本行、OCR 行、情绪、人脸和块缩略图。

可以使用“获取明细”API 获取 JSON 内容形式的视频的所有明细。  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
此外，可以通过在视频索引器门户中的视频上按“播放”按钮，来直观检查视频的汇总见解。 有关详细信息，请参阅[查看和编辑视频见解](video-indexer-view-edit.md)。

![洞察力](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

本文探讨“获取明细”API 返回的 JSON 内容。 可以参阅[概念](video-indexer-concepts.md)一文。

> [!NOTE]
> 视频索引器中所有访问令牌的有效期为一小时。

## <a name="root-elements"></a>根元素

属性 | Description
---|---
id|此视频的 ID。 例如 63c6d532ff。
partition|用户可以在上传时指定的逻辑分区，方便以后进行搜索。
名称|视频的名称。 例如 Azure Monitor。
description|视频的说明。 例如，“John Kemnetz 与 Scott Hanselman 一起介绍如何解锁 Azure 通过 Azure Monitor 进行数据监视的功能。”
userName|视频创建者。 例如，Channel9 视频。
createTime |创建时间。 例如，2017-03-31T16:36:41.4504249+00:00。
privacyMode|视频可以为以下模式之一：“Private”、“Public”。 **公共** - 向你帐户中的任何人，以及具有视频链接的每个人显示该视频。 **私用** - 向你帐户中的每个人显示该视频。
isOwned|如果当前用户拥有此视频，则为 true， 否则为 false。  
isBase|如果明细是基于源视频，则为 true。 如果明细是一个播放列表的，而该列表派生自另一明细，则为 false。
durationInSeconds|视频的持续时间。
summarizedInsights|包含一个 [summarizedInsights](#summarizedInsights)。
breakdowns|可以包含一个或多个[明细](#breakdowns)
social|包含一个 **social** 元素，用于描述视频的点赞数和观看数。

## <a name="summarizedinsights"></a>summarizedInsights

本部分介绍见解的摘要。

属性 | Description
---|---
名称|视频的名称。 例如 Azure Monitor。
shortId|视频的 ID。 例如 63c6d532ff。
privacyMode|可以细分为以下模式之一：“私用”、“公共”。 **公共** - 向你帐户中的任何人，以及具有视频链接的每个人显示该视频。 **私用** - 向你帐户中的每个人显示该视频。
duration|包含一个持续时间，用于描述见解发生的时间。 持续时间以秒为单位。
thumbnailUrl|视频的缩略图完整 URL。 例如“https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”。请注意，如果视频是私人视频，则 URL 包含一个一小时的访问令牌。 一小时后，URL 将不再有效，你需要再次获取包含新 URL 的明细，或者调用 GetAccessToken 以获取新的访问令牌并手动构造完整的 URL ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]')。
人脸|可以包含一个或多个[人脸](#faces)
topics|可以包含一个或多个[主题](#topics)
情绪|可以包含一个或多个[情绪](#sentiments)
audioEffects| 可以包含一个或多个 [audioEffects](#audioEffects)
brands| 可以包含零个或多个[品牌](#brands)
统计信息|有关详细信息，请参阅[统计信息](#Statistics)
。
### <a name="statistics"></a>统计信息

|名称|Description|
|---|---|
|CorrespondenceCount|视频中对应关系的数目。|
|WordCount|每个发言人的单词数。|
|SpeakerNumberOfFragments|发言人在视频中的片段数量。|
|SpeakerLongestMonolog|发言人的最长独白。 如果发言人在独白中有沉默，则会将沉默期包含在内。 删除独白开头和结尾的沉默期。| 
|SpeakerTalkToListenRatio|计算方式为将发言人的独白时间（开头和结尾之间没有沉默期）除以视频总时间。 时间将四舍五入为三位小数。|

## <a name="breakdowns"></a>breakdowns

本部分介绍见解的详细信息。

属性 | Description
---|---
id|明细 ID。 例如 63c6d532ff。
state|给定明细 ID 的处理状态。可以是下述值之一：Uploaded、Processing、Processed、Failed。
processingProgress|进度。 例如 10%。
externalId| 可以在上传期间设置 externalId。 例如“4f9c3500-eca7-4ab3-987e-a745017af698”。 可在以后按此外部 ID 来搜索视频。
externalUrl|可以在上传期间设置 externalUrl。 
metadata|可以在上传期间设置元数据。 
insights|可以包含一个或多个[见解](#insights)
thumbnailUrl|视频的缩略图完整 URL。 例如“https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”。 请注意，如果视频是私人视频，则 URL 包含一个一小时的访问令牌。 一小时后，URL 将不再有效，你需要再次获取包含新 URL 的明细，或者调用 GetAccessToken 以获取新的访问令牌并手动构造完整的 URL ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]')。
publishedUrl|已发布的 URL。 例如“https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest”。
viewToken|持有者令牌
sourceLanguage|源语言。 支持以下语言：中文、英语、法语、德语、意大利语、日语、葡萄牙语、俄语、西班牙语。
语言|脚本的语言。

## <a name="insights"></a>insights

属性 | Description 
---|---
transcriptBlocks|可以包含一个或多个 [transcriptBlocks](#transcriptBlocks)
topics|可以包含一个或多个[主题](#topics)
人脸|可以包含一个或多个[人脸](#faces)
participants|可以包含一个或多个[参与者](#participants)
contentModeration|可以包含一个 [contentModeration](#contentModeration)
audioEffectsCategories|可以包含一个或多个 [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>人脸

### <a name="summarizedinsights"></a>summarizedInsights

在 **summarizedInsights** 下显示的**人脸**，显示在视频中发现的每个人脸的摘要。

属性 | Description 
---|---
id|人物的 ID。 例如 11775。
shortId|短 ID。 由于播放列表可能派生自多个明细，因此需要此 ID 来找出这些明细中的哪一个是每个人脸的来源。  
名称|如果识别了人脸，则会添加该人的姓名。 例如“Scott Hanselman”。 如果人脸未知，则会添加“未知 #”。 
description|如果识别了人脸，则会根据必应 API 搜索来填充说明。 否则，说明为 **null**。
title|如果识别了人脸，则会根据必应 API 搜索来填充说明。 否则，标题为 **null**。
thumbnailFullUrl|人脸的缩略图完整 URL。 例如“https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”。请注意，如果视频是私人视频，则 URL 包含一个一小时的访问令牌。 一小时后，URL 将不再有效，你需要再次获取包含新 URL 的明细，或者调用 GetAccessToken 以获取新的访问令牌并手动构造完整的 URL ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]')。
appearances|可以包含一个或多个[外观](#appearances)
seenDuration|显示人脸的时长（以秒为单位）。
seenDurationRatio|相对于视频持续时间的存在 (0-1)。

### <a name="breakdown-insights"></a>breakdown insights

在 **breakdowns** 下显示的**人脸**，描述在视频中发现的每个人脸的详细信息。

属性 | Description 
---|---
id|人物的 ID。 例如 11775。
bingId|
名称|如果识别了人脸，则会添加该人的姓名。 例如“Scott Hanselman”。 如果人脸未知，则会添加“未知 #”。 
thumbnailId|例如，616468f0-1636-4efa-94e7-262f2e575059。
description|如果识别了人脸，则会根据必应 API 搜索来填充说明。 否则，说明为 **null**。
title|如果识别了人脸，则会根据必应 API 搜索来填充说明。 否则，标题为 **null**。
imageUrl|此 URL 指向从源视频提取的图像。  
confidence|置信度分数（越高越好）。
knownPersonId|已知人员（例如名人）的 ID。 如果某人为未知人员，则此 ID 包含零。 例如，e3eaff5f-ee1b-4eac-80ce-ebac47aadf64。

## <a name="topics"></a>topics

### <a name="summarizedinsights"></a>summarizedInsights

在 **summarizedInsights** 下显示的**主题**，显示在视频中发现的每个主题的摘要。

属性 | Description 
---|---
名称|主题名称（例如“Azure”）。 
appearances|可以包含一个或多个[外观](#appearances)。
isTranscript|如果在脚本中找到，则为 true。 如果在 OCR 中找到，则为 false。

### <a name="breakdown-insights"></a>breakdown insights

在 **breakdowns** 下显示的**主题**，描述在视频中发现的每个主题的详细信息。

|属性 | Description |
|---|---|
|id|唯一主题 ID。|
|名称|主题名称。|
|stem|目前不使用此属性。|
|words|目前不使用此属性。|
|rank|相关性分数（越高越好）。|

## <a name="sentiments"></a>情绪

属性 | Description
---|---
sentimentKey| 目前支持以下情绪：Positive、Neutral、Negative。 
appearances|可以包含一个或多个[外观](#appearances)|。
seenDurationRatio|相对于视频持续时间的存在 (0-1)。

## <a name="audioeffects"></a>audioEffects

属性 | Description 
---|---
audioEffectKey| 有效值：Speech、Silence、HandClaps。
appearances|可以包含一个或多个[外观](#appearances)
seenDurationRatio|相对于视频持续时间的存在 (0-1)。
seenDuration|音频效果存在的时长（以秒为单位）。

## <a name="appearances"></a>appearances

属性 | Description 
---|---
startTime| 时间值。
endTime|时间值。
startSeconds| 时间值。
endSeconds| 时间值。

## <a name="participants"></a>participants

属性 | Description 
---|---
id|参与者的 ID。
名称|参与者的名称。 例如，“发言人 1”。
pictureUrl|保留 **pictureUrl** 属性供将来使用。

## <a name="contentmoderation"></a>contentModeration

属性 | Description 
---|---
adultClassifierValue|视频有成人内容的置信度。
racyClassifierValue|视频有不雅内容的置信度。
bannedWordsCount|猥亵词数。 
bannedWordsRatio|猥亵词占单词总数的比例。
reviewRecommended|布尔值，指示是否应对视频进行手动审核。
isAdult|布尔值，指示在手动审核后是否将视频视为成人视频。

## <a name="audioeffectscategories"></a>audioEffectsCategories

属性 | Description 
---|---
type|类别的 ID。
key|以下值之一：Speech、Silence、HandClaps。 

## <a name="transcriptblocks"></a>transcriptBlocks

属性 | Description
---|---
id|块的 ID。
lines|可以包含一个或多个[行](#lines)
sentimentIds|保留 **sentimentIds** 属性供将来使用。
thumbnailIds|保留 **thumbnailIds** 属性供将来使用。
情绪|块中的情绪（0-1，从负面到正面）。
人脸|可以包含一个或多个[人脸](#faces)。
ocrs|可以包含一个或多个 [ocr](#ocrs)。
audioEffectInstances|可以包含一个或多个 [audioEffectInstances](#audioEffectInstances)。
scenes|可以包含一个或多个[场景](#scenes)。
annotations|可以包含零个或多个[注释](#annotations)。

## <a name="ocrs"></a>ocrs

说明在视频中的哪个时间点发现文本内容。 

属性 | Description 
---|---
timeRange|原始视频中的时间范围。
adjustedTimeRange|AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个一小时的视频，只使用其中的 1 行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。
lines|可以包含一个或多个[行](#lines)。

## <a name="lines"></a>lines

### <a name="transcriptblocks"></a>transcriptBlocks

在 **transcriptBlocks** 下显示的**行**，描述在视频中发现的脚本行。

属性 | Description 
---|---
id| 行的 ID。
timeRange|原始视频中的时间范围。
adjustedTimeRange|AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个一小时的视频，只使用其中的 1 行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。
participantID| 此行的发言人的 ID。
text| 脚本。
isIncluded| 在基本明细中始终为 true。 在派生的播放列表中，包括在源视频中的行设置为 isIncluded=true。 所有其他行都是 false。

### <a name="ocrs"></a>ocrs

在 **ocrs** 下显示的**行**，描述在视频中发现的 OCR 行。

属性 | Description 
---|---
id|OCR ID。
width|目前不使用此属性。
height|目前不使用此属性。
语言|OCR 语言。
textData|OCR 文本。
confidence|置信度分数（越高越好）。

## <a name="scenes"></a>scenes

属性 | Description 
---|---
id|场景 ID。
timeRange|包含一个 **timeRange**。
keyFrame|关键帧的时间。
截图|可以包含一个或多个[截图](#shots)。

## <a name="shots"></a>截图

属性 | Description 
---|---
id||截图 ID。
timeRange|包含一个 **timeRange**。
keyFrame|关键帧的时间。

## <a name="audioeffectinstances"></a>audioEffectInstances

属性 | Description 
---|---
type|音频事件的索引：笑 = 1、鼓掌 = 2、音乐 = 3、语音 = 4、静默 = 5
ranges|可以包含一个或多个[范围](#ranges)。

## <a name="ranges"></a>ranges

在 **audioEffectInstances** 下显示的**范围**，描述这些范围中的音频效果。

属性 | Description 
---|---
timeRange|原始视频中的时间范围。
adjustedTimeRange|AdjustedTimeRange 是相对于当前播放列表的时间范围。 由于可以从不同视频的不同行来创建播放列表，因此可以取一个一小时的视频，只使用其中的一行，例如 10:00-10:15。 在这种情况下，你会有一个只有 1 行的播放列表，其中的时间范围是 10:00-10:15，但 adjustedTimeRange 是 00:00-00:15。

## <a name="annotations"></a>annotations

根据可识别对象、生物、风景、动作和视觉模型返回标记。

|属性|Description|
|---|---|
|id|注释的 ID。|
|名称|注释的名称（例如，“人物”、“体育比赛”、“黑框”）。|
|Appearances|可以包含一个或多个外观。|

## <a name="brands"></a>brands

在语音转文本脚本和/或视频 OCR 中检测到的企业和产品品牌名称。 这不包括品牌或徽标检测内容的视觉辨识形式。

属性 | Description
---|---
id | 品牌的 ID。
名称 | 必应品牌或自定义品牌的名称。  
wikiId | 品牌维基百科 URL 的后缀。 例如，“Target_Corporation”是 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 的后缀。
wikiUrl | 品牌的维基百科 URL（如果存在）。 例如，[https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)。
confidence | 视频索引器品牌检测器的置信度值 (0-1)。
description | 描述从维基百科提取的品牌。 
appearances | 可以包含一个或多个外观。
seenDuration | 相对于视频持续时间的存在 (0-1)。

## <a name="next-steps"></a>后续步骤

若要了解如何创建自己的明细，请参阅[查看和编辑视频索引器见解](video-indexer-view-edit.md)。

有关如何在应用程序中嵌入小组件的信息，请参阅[将视频索引器小组件嵌入应用程序](video-indexer-embed-widgets.md)。 

## <a name="see-also"></a>另请参阅

[视频索引器 API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[视频索引器概述](video-indexer-overview.md)

