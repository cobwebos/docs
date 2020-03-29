---
title: 从索引器 v1 和 v2 迁移到 Azure 媒体服务视频索引器 |微软文档
description: 本主题讨论如何从 Azure 媒体索引器 v1 和 v2 迁移到 Azure 媒体服务视频索引器。
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
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513229"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>从媒体索引器和媒体索引器 2 迁移到视频索引器

[Azure 媒体索引器](media-services-index-content.md)媒体处理器和[Azure 媒体索引器 2 预览](media-services-process-content-with-indexer2.md)媒体处理器正在停用。 有关停用日期，请参阅此[遗留组件](legacy-components.md)主题。 [Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)将替换这些旧媒体处理器。

Azure 媒体服务视频索引器基于 Azure 媒体分析、Azure 认知搜索、认知服务（如人脸 API、Microsoft 翻译器、计算机视觉 API 和自定义语音服务）构建。 有了视频索引器，就可以使用视频索引器视频和音频模型从视频中提取见解。 要查看视频索引器可以使用哪些方案、它提供哪些功能以及如何入门，请参阅[视频索引器视频和音频模型](../video-indexer/video-indexer-overview.md)。 

您可以使用[Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md)或直接使用[视频索引器 API](https://api-portal.videoindexer.ai/)从视频和音频文件中获取见解。 目前，视频索引器 API 和媒体服务 v3 API 提供的功能之间存在重叠。

> [!NOTE]
> 要了解何时要使用视频索引器与媒体服务分析器预设，请查看[比较文档](../video-indexer/compare-video-indexer-with-media-services-presets.md)。 

本文讨论从 Azure 媒体索引器和 Azure 媒体索引器 2 迁移到 Azure 媒体服务视频索引器的步骤。  

## <a name="migration-options"></a>迁移选项 

|如果您需要  |然后在受影响的域控制器上，运行 |
|---|---|
|以隐藏字幕文件格式为任何媒体文件格式提供语音到文本转录的解决方案：VTT、SRT 或 TTML<br/>以及其他音频见解，例如：关键字、主题推断、声学事件、扬声器分化、实体提取和翻译| 通过视频索引器 v2 REST API 或 Azure 媒体服务 v3 音频分析器预设更新应用程序以使用 Azure 视频索引器功能。|
|语音到文本功能| 直接使用认知服务语音 API。|  

## <a name="getting-started-with-video-indexer"></a>开始使用视频索引器

以下部分将您指向相关链接：[如何开始使用视频索引器？](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>开始使用媒体服务 v3 API

Azure 媒体服务 v3 API 使您能够通过[Azure 媒体服务 v3 分析器预设](../latest/analyzing-video-audio-files-concept.md)从视频和音频文件中提取见解。 

凭借 AudioAnalyzerPreset 能够从音频或视频文件中提取多个音频见解****。 输出包括音频脚本的 VTT 或 TTML 文件和 JSON 文件（以及所有其他音频见解）。 音频见解包括关键字、扬声器索引和语音情绪分析。 音频分析器预设还支持特定语言的语言检测。 有关详细信息，请参阅[转换](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)。

### <a name="get-started"></a>入门

若要开始操作，请参阅：

* [教程](../latest/analyze-videos-tutorial-with-api.md)
* 音频分析器预设示例[：Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)或[.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* 视频分析器预设示例[：Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)或[.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>开始认知服务语音服务

[Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/)提供语音到文本服务，该服务可实时将音频流转录到文本，而应用程序、工具或设备可以使用或显示这些文本。 您可以使用语音到文本自定义[您自己的声学模型、语言模型或发音模型](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)。 有关详细信息，请参阅[认知服务语音到文本](../../cognitive-services/speech-service/speech-to-text.md)。 

> [!NOTE] 
> 语音到文本服务不采用视频文件格式，只采用[某些音频格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats)。 

有关文本到语音服务以及如何开始使用的详细信息，请参阅[什么是语音到文本？](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>与弃用服务的已知差异 

您会发现，视频索引器、Azure 媒体服务 v3 音频Analyzer预设和认知服务语音服务服务比已停用的 Azure 媒体索引器 1 和 Azure 媒体索引器 2 处理器更可靠，并生成更好的高质量输出。  

一些已知的差异包括： 

* 认知服务语音服务不支持关键字提取。 但是，视频索引器和媒体服务 v3 音频分析器预设都提供了一组更强大的 JSON 文件格式的关键字。 

## <a name="need-help"></a>需要帮助？

您可以通过导航到["新建支持"请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来打开支持票证

## <a name="next-steps"></a>后续步骤

* [旧组件](legacy-components.md)
* [定价页面](https://azure.microsoft.com/pricing/details/media-services/#encoding)


