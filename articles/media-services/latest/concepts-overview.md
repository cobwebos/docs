---
title: 媒体服务术语和概念
titleSuffix: Azure Media Services
description: 了解 Azure 媒体服务的术语和概念。
services: media-servicesgit
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
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500087"
---
# <a name="media-services-terminology-and-concepts"></a>媒体服务术语和概念

本主题简要概述 Azure 媒体服务的术语和概念。 本文还提供了指向文章的链接，并深入解释了媒体服务 v3 的概念和功能。

在开始开发之前，应审查这些主题中描述的基本概念。

> [!NOTE]
> 目前，您可以使用 Azure[门户](https://portal.azure.com/)：管理媒体服务 v3[实时事件](live-events-outputs-concept.md)、查看（未管理）v3[资产](assets-concept.md)，以及[获取有关访问 API 的信息](access-api-portal.md)。
> 对于所有其他管理任务（例如，[转换、作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用[REST API](https://aka.ms/ams-v3-rest-ref) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支持的[SDK](media-services-apis-overview.md#sdks)之一。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>媒体服务 v3 术语

|术语|描述|
|---|---|
|实时事件|**实时事件**表示用于引入、转码（可选）以及打包视频、音频和实时元数据的管道。<br/><br/>对于从媒体服务 v2 API 迁移的客户，**实时事件**取代了 v2 中的**频道**实体。 有关详细信息，请参阅[从 v2 迁移到 v3](migrate-from-v2-to-v3.md)。|
|流式处理终结点/打包/原点|**流式处理终结点**表示动态（实时）打包和原点服务，可直接将实时和按需内容传递到客户端播放器应用程序。 它使用一种常见的流媒体协议（HLS 或 DASH）。 此外，**流式处理终结点**为业界领先的数字版权管理系统 （DRM） 提供动态（及时）加密。<br/><br/>在媒体流行业，此服务通常称为**打包器**或**来源**。  此功能的行业中其他常见术语包括 JITP（及时打包器）或 JITE（及时加密）。

## <a name="media-services-v3-concepts"></a>媒体服务 v3 概念

|概念|描述|链接|
|---|---|---|
|资产和上传内容|若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建一个媒体服务帐户，并将数字文件上传到**资产**中。|[云上传和存储](storage-account-concept.md)<br/><br/>[资产的概念](assets-concept.md)|
|对内容进行编码|将优质数字媒体文件上传到资产中后，可将其编码为可在各种浏览器和设备上播放的格式。 <br/><br/>若要使用媒体服务 v3 进行编码，需要创建**转换**和**作业**。|[转换和作业](transforms-jobs-concept.md)<br/><br/>[使用媒体服务进行编码](encoding-concept.md)|
|分析内容（视频索引器）|媒体服务 v3 允许您使用媒体服务 v3 预设从视频和音频文件中获取见解。 要使用媒体服务 v3 预设分析内容，您需要创建**转换**和**作业**。<br/><br/>如果需要更详细的见解，请使用[视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)。|[分析视频和音频文件](analyzing-video-audio-files-concept.md)|
|打包和交付|将内容编码后，可以利用**动态打包**。 在媒体服务中，**流式处理终结点**是用于向客户端播放器提供媒体内容的动态打包服务。 要使输出资产中的视频可供客户端播放，必须创建**流式定位器**，然后生成流式处理 URL。 <br/><br/>创建**流式处理器**时，除了资产的名称外，还需要指定**流式处理策略**。 **流式处理策略**使您能够为**流式处理器**定义流式处理协议和加密选项（如果有）。 无论流式传输的是直播内容还是点播内容，都要使用动态打包。 <br/><br/>可以使用媒体服务**动态清单**来仅流式传输视频的特定再现内容或子剪辑。|[动态打包](dynamic-packaging-overview.md)<br/><br/>[流式处理终结点](streaming-endpoint-concept.md)<br/><br/>[流式处理定位符](streaming-locators-concept.md)<br/><br/>[流式处理策略](streaming-policy-concept.md)<br/><br/>[动态清单](filters-dynamic-manifest-overview.md)<br/><br/>[筛选器](filters-concept.md)|
|内容保护|借助媒体服务，您可以使用高级加密标准 （AES-128） 或/以及三个主要 DRM 系统中的任何一个动态加密的实时和点播内容：微软 PlayReady、Google Widevine 和 Apple FairPlay。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 <br/><br/>若要针对流指定加密选项，请创建**内容密钥策略**并将其与**流定位符**相关联。 使用**内容密钥策略**，可以配置如何将内容密钥传送到终端客户端。<br/><br/> 当需要相同的选项时，请尝试重用策略。| [内容密钥策略](content-key-policy-concept.md)<br/><br/>[内容保护](content-protection-overview.md)|
|实时传送视频流|使用媒体服务可将直播活动传送到 Azure 云中的客户。 **直播活动**负责引入和处理实时视频源。 创建**实时事件**时，将创建一个输入终结点，可用于从远程编码器发送实时信号。 一旦流流入**实时事件**，您可以通过创建**资产**、**实时输出**和**流式处理器**来开始流式处理事件。 **实时输出**会将流存档到**资产**中，使观看者可通过**流式处理终结点**使用该流。 实时事件可以设置为*传递*（本地实时编码器发送多个比特率流）或*实时编码*（本地实时编码器发送单个比特率流）。 |[实时传送视频流概述](live-streaming-overview.md)<br/><br/>[直播活动和实时输出](live-events-outputs-concept.md)|
|使用事件网格进行监控|要查看作业的进度，请使用**事件网格**。 媒体服务还会发出实时事件类型。 使用事件网格，应用可以侦听和响应来自几乎所有 Azure 服务和自定义源的事件。 |[处理事件网格事件](reacting-to-media-services-events.md)<br/><br/>[架构](media-services-event-schemas.md)|
|使用 Azure 监视器进行监视|监视指标和诊断日志，帮助您了解应用如何使用 Azure 监视器。|[指标和诊断日志](media-services-metrics-diagnostic-logs.md)<br/><br/>[诊断日志架构](media-services-diagnostic-logs-schema.md)|
|播放器客户端|可以在各种浏览器和设备上使用 Azure Media Player 播放媒体服务流式传输的媒体内容。 Azure 媒体播放器使用行业标准（如 HTML5、媒体源扩展 （MSE） 和加密媒体扩展 （EME） 来提供丰富的自适应流体验。 |[Azure Media Player 概述](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [对远程文件和流视频进行编码 – REST](stream-files-tutorial-with-rest.md)
* [对上传的文件和流视频进行编码 – .NET](stream-files-tutorial-with-api.md)
* [实时流 - .NET](stream-live-tutorial-with-api.md)
* [分析视频 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 动态加密 - .NET](protect-with-aes128.md)
* [通过多重 DRM 进行动态加密 - .NET](protect-with-drm.md)
