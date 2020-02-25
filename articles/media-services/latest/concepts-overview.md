---
title: Azure 媒体服务术语和概念-Azure |Microsoft Docs
description: 本主题简要概述了 Azure 媒体服务的术语和概念，并提供了更多详细信息的链接。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e2c718b3b5ee27b5781f5f6287e0ee45fa405170
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562371"
---
# <a name="media-services-concepts"></a>媒体服务概念

本主题简要概述了 Azure 媒体服务的术语和概念。 本文还提供了指向文章的链接，其中深入介绍了媒体服务 v3 的概念和功能。 

在开始开发之前，应该复习这些主题中所述的基本概念。

> [!NOTE]
> 目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或受支持的 [SDK](media-services-apis-overview.md#sdks) 之一。

## <a name="media-services-v3-terminology"></a>媒体服务 v3 术语

|术语|说明|
|---|---|
|实时事件|**实时事件**表示引入、转码（可选）和打包视频、音频和实时元数据的管道。<br/><br/>对于从媒体服务 v2 Api 迁移的客户，**实时事件**会替换 v2 中的**通道**实体。 有关详细信息，请参阅[从 V2 迁移到 v3](migrate-from-v2-to-v3.md)。|
|流式处理终结点/打包/源|**流式处理终结点**表示动态（实时）打包和源服务，它可以使用常用的流式处理媒体协议（HLS 或短划线）将实时内容和点播内容直接传送到客户端播放器应用程序。 此外，**流式处理终结点**提供对业界领先 drm 的动态（实时）加密。<br/><br/>在媒体流行业，此服务通常称为**包装**器或**源**。  此功能行业中的其他常见术语包括 JITP （实时包装程序）或 JITE （实时加密）。 

## <a name="media-services-v3-concepts"></a>媒体服务 v3 概念

|概念|说明|链接|
|---|---|---|
|资产和上传内容|若要开始在 Azure 中管理、加密、编码、分析和流式传输媒体内容，需要创建媒体服务帐户，并将数字文件上传到**资产**中。|[云上传和存储](storage-account-concept.md)<br/><br/>[资产概念](assets-concept.md)|
|对内容进行编码|将高质量的数字媒体文件上传到资产后，可以将它们编码为可在各种浏览器和设备上播放的格式。 <br/>若要为 Media Services v3 编码，需要创建**转换**和**作业**。|[转换和作业](transforms-jobs-concept.md)<br/><br/>[媒体服务编码](encoding-concept.md)|
|分析内容（视频索引器）|媒体服务 v3 允许使用媒体服务 v3 预设从视频和音频文件中提取见解。 若要使用媒体服务 v3 预设分析内容，需要创建**转换**和**作业**。<br/><br/>如果需要更详细的见解，请直接使用[视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)。|[分析视频和音频文件](analyzing-video-audio-files-concept.md)|
|打包和交付|对内容进行编码后，可以利用**动态打包**。 在媒体服务中，**流式处理终结点**是用于将媒体内容传递给客户端播放器的动态打包服务。 若要使客户端能够使用输出资产中的视频进行播放，必须创建**流式处理定位符**，然后生成流式处理 url。 <br/><br/>创建**流定位符**时，除了资产名称外，还需要指定**流式处理策略**。 **流式处理策略**使你可以为**流式处理**定位符定义流式处理协议和加密选项（如果有）。 无论是实时还是按需流式传输内容，都可以使用动态打包。 <br/><br/>你可以使用媒体服务**动态清单**仅流式传输视频的特定格式副本或子剪辑。|[动态打包](dynamic-packaging-overview.md)<br/><br/>[流式处理终结点](streaming-endpoint-concept.md)<br/><br/>[流式处理定位符](streaming-locators-concept.md)<br/><br/>[流式处理策略](streaming-policy-concept.md)<br/><br/>[动态清单](filters-dynamic-manifest-overview.md)<br/><br/>[筛选器](filters-concept.md)|
|内容保护|借助媒体服务，你可以通过高级加密标准（AES-128）或/以及三个主要数字版权管理（DRM）系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任意一个，提供动态加密的实时和点播内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 <br/><br/>如果在流上指定加密选项，请创建**内容密钥策略**，并将其与**流式处理定位符**相关联。 利用**内容密钥策略**，你可以配置如何将内容密钥传递到最终客户端。<br/><br/> 如果需要相同的选项，请尝试重复使用策略。| [内容密钥策略](content-key-policy-concept.md)<br/><br/>[内容保护](content-protection-overview.md)|
|实时传送视频流|使用媒体服务可将直播活动传送到 Azure 云中的客户。 **直播活动**负责引入和处理实时视频源。 当你创建**活动事件**时，将创建一个输入终结点，你可以使用该终结点从远程编码器发送实时信号。 将流流入**实时事件**后，可以通过创建**资产**、**实时输出**和**流式处理定位符**来开始流式处理事件。 **实时输出**会将流存档到**资产**中，并通过**流式处理终结点**将其提供给查看者。 **实时事件**可以是以下两种类型之一：**传递**和**实时编码**。|[实时传送视频流概述](live-streaming-overview.md)<br/><br/>[直播活动和实时输出](live-events-outputs-concept.md)|
|通过事件网格进行监视|若要查看作业的进度，应使用**事件网格**。 媒体服务还会发出实时事件类型。 使用事件网格，应用可以侦听和响应来自几乎所有 Azure 服务和自定义源的事件。 |[处理事件网格事件](reacting-to-media-services-events.md)<br/><br/>[架构](media-services-event-schemas.md)|
|监视 Azure Monitor|监视指标和诊断日志，以帮助你了解应用程序如何通过 Azure Monitor 执行。|[指标和诊断日志](media-services-metrics-diagnostic-logs.md)<br/><br/>[诊断日志架构](media-services-diagnostic-logs-schema.md)|
|播放器客户端|你可以使用 Azure Media Player 在各种浏览器和设备上播放媒体服务流式传输的媒体内容。 Azure 媒体播放器采用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。 |[Azure Media Player 概述](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [对远程文件和流视频进行编码 - REST](stream-files-tutorial-with-rest.md)
* [对上传的文件和流视频进行编码 - .NET](stream-files-tutorial-with-api.md)
* [实时流 - .NET](stream-live-tutorial-with-api.md)
* [分析视频 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 动态加密 - .NET](protect-with-aes128.md)
* [通过多重 DRM 进行动态加密 - .NET](protect-with-drm.md) 
