---
title: 从索引器 v1 和 v2 迁移到 Azure 媒体服务视频索引器 |Microsoft Docs
description: 本主题讨论如何从 Azure Media Indexer v1 和 v2 迁移到 Azure 媒体服务视频索引器。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: d38b11b8fc4351c6b074ccfdf47df06a71e0a0a4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823968"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>从媒体索引器和媒体索引器2迁移到视频索引器

[Azure Media Indexer](media-services-index-content.md)媒体处理器将在2020年10月1日停用。 [Azure Media Indexer 2 预览版](media-services-process-content-with-indexer2.md)媒体处理器将于2020年1月1日停用。  [Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)替代了这些旧媒体处理器。

Azure 媒体服务视频索引器是基于 Azure 媒体分析、Azure 认知搜索、认知服务（如人脸 API、Microsoft Translator、计算机视觉 API 和自定义语音服务）构建的。 有了视频索引器，就可以使用视频索引器视频和音频模型从视频中提取见解。 若要查看视频索引器可用于哪些方案、它所提供的功能以及如何开始使用，请参阅[视频索引器视频和音频型号](../video-indexer/video-indexer-overview.md)。 

可以通过使用[Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md)或直接使用[视频索引器 api](https://api-portal.videoindexer.ai/)从视频和音频文件中提取见解。 目前，视频索引器 Api 提供的功能与媒体服务 v3 Api 提供的功能重叠。

> [!NOTE]
> 若要了解何时需要使用视频索引器与媒体服务分析器预设，请查看[比较文档](../video-indexer/compare-video-indexer-with-media-services-presets.md)。 

本文介绍了从 Azure Media Indexer 和 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器的步骤。  

## <a name="migration-options"></a>迁移选项 

|如果需要  |然后在受影响的域控制器上，运行 |
|---|---|
|一种为隐藏式字幕文件格式的媒体文件格式提供语音到文本脚本的解决方案： VTT、SRT 或 TTML<br/>以及其他音频见解，如关键字、主题推断、声音事件、发言人 diarization、实体提取和转换| 更新应用程序，以通过视频索引器 v2 REST API 或 Azure 媒体服务 v3 音频分析器预设使用 Azure 视频索引器功能。|
|语音到文本功能| 直接使用认知服务语音 API。|  

## <a name="getting-started-with-video-indexer"></a>视频索引器入门

以下部分指向相关链接：[如何开始视频索引器？](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>媒体服务 v3 Api 入门

通过 azure 媒体服务 v3 API，你可以通过[Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md)从视频和音频文件中提取见解。 

凭借 AudioAnalyzerPreset 能够从音频或视频文件中提取多个音频见解。 输出包括音频稿本的 VTT 或 TTML 文件和一个 JSON 文件（包含所有其他音频见解）。 音频见解包括关键字、扬声器索引和语音情绪分析。 AudioAnalyzerPreset 还支持特定语言的语言检测。 有关详细信息，请参阅[转换](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>入门

若要开始，请参阅：

* [教程](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset 示例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)或[.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 示例： [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)或[.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>认知服务语音服务入门

[Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/)提供了一种语音到文本服务，可让你的应用程序、工具或设备可以使用或显示实时实时传输到文本的转录。 您可以使用语音到文本[自定义自己的声音模型、语言模型或发音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 有关详细信息，请参阅[认知服务语音到文本](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 语音到文本服务不采用视频文件格式，只采用[某些音频格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)。 

有关文本到语音转换服务以及如何入门的详细信息，请参阅[什么是语音到文本？](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>与弃用的服务的已知差异 

你会发现，视频索引器、Azure 媒体服务 v3 AudioAnalyzerPreset 和认知服务语音服务服务的可靠性更高，并产生比已淘汰 Azure Media Indexer 1 和 Azure Media Indexer 2 处理器更好的输出质量。  

一些已知的区别包括： 

* 将不再支持采用萨摩斯语格式的隐藏式字幕文件。 这是一种不再广泛使用的较旧字幕格式。 它被 TTML、WebVTT 和 SRT 替换。  
* 将不再支持音频索引 Blob （AIB）文件。 此功能特定于索引器1技术，不再提供。  
* 认知服务语音服务不支持关键字提取。 不过，视频索引器和媒体服务 v3 AudioAnalyzerPreset 都提供一组更可靠的一组 JSON 文件格式的关键字。 

## <a name="need-help"></a>需要帮助？

可以通过导航到 "[新建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)" 来打开支持票证

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页](https://azure.microsoft.com/pricing/details/media-services/#encoding)


