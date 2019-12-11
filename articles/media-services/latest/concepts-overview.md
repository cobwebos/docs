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
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970030"
---
# <a name="media-services-concepts"></a>媒体服务概念

本主题简要概述了 Azure 媒体服务的术语和概念。 本文还提供了指向文章的链接，其中深入介绍了媒体服务 v3 的概念和功能。 

在开始开发之前，应该复习这些主题中所述的基本概念。

> [!NOTE]
> 目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或受支持的 [SDK](media-services-apis-overview.md#sdks) 之一。

## <a name="terminology"></a>术语

本部分介绍如何将一些常见的行业条款映射到媒体服务 v3 API。

### <a name="live-event"></a>实时活动

**实时事件**表示引入、转码（可选）和打包视频、音频和实时元数据的管道。

对于从媒体服务 v2 Api 迁移的客户，**实时事件**会替换 v2 中的**通道**实体。 有关详细信息，请参阅[从 V2 迁移到 v3](migrate-from-v2-to-v3.md)。

### <a name="streaming-endpoint-packaging-and-origin"></a>流式处理终结点（打包和源）

**流式处理终结点**表示动态（实时）打包和源服务，它可以使用常用的流式处理媒体协议（HLS 或短划线）将实时内容和点播内容直接传送到客户端播放器应用程序。 此外，**流式处理终结点**提供对业界领先 drm 的动态（实时）加密。

在媒体流行业，此服务通常称为**包装**器或**源**。  此功能行业中的其他常见术语包括 JITP （实时包装程序）或 JITE （实时加密）。 
 
## <a name="cloud-upload-and-storage"></a>云上传和存储

若要开始在 Azure 中管理、加密、编码、分析和流式传输媒体内容，需要创建媒体服务帐户，并将数字文件上传到**资产**中。

- [云上传和存储](storage-account-concept.md)
- [资产概念](assets-concept.md)

## <a name="encoding"></a>编码

将高质量的数字媒体文件上传到资产后，可以将它们编码为可在各种浏览器和设备上播放的格式。 

若要为 Media Services v3 编码，需要创建**转换**和**作业**。

![转换](./media/encoding/transforms-jobs.png)

- [转换和作业](transforms-jobs-concept.md)
- [媒体服务编码](encoding-concept.md)

## <a name="media-analytics"></a>媒体分析

若要分析视频和音频文件，还需要创建**转换**和**作业**。

- [分析视频和音频文件](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>打包、传送、保护

对内容进行编码后，可以利用**动态打包**。 在媒体服务中，**流式处理终结点**/Origin 是用于将媒体内容传递给客户端播放器的动态打包服务。 若要使客户端能够使用输出资产中的视频进行播放，必须创建**流式处理定位符**，然后生成流式处理 url。 

创建**流定位符**时，除了资产名称外，还需要指定**流式处理策略**。 **流式处理策略**使你可以为**流式处理**定位符定义流式处理协议和加密选项（如果有）。

无论是实时还是按需流式传输内容，都可以使用动态打包。 下图显示了具有动态打包工作流的按需流式处理。

![动态打包](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

借助媒体服务，你可以通过高级加密标准（AES-128）或/以及三个主要数字版权管理（DRM）系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任意一个，提供动态加密的实时和点播内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。

如果在流上指定加密选项，请创建**内容密钥策略**，并将其与**流式处理定位符**相关联。 利用**内容密钥策略**，你可以配置如何将内容密钥传递到最终客户端。

下图阐释了媒体服务内容保护工作流： 

![保护内容](./media/content-protection/content-protection.svg)

&#42;动态加密支持 128 "明文密钥"、CBC 和 CENC。 

你可以使用媒体服务**动态清单**仅流式传输视频的特定格式副本或子剪辑。 以下示例使用编码器将夹层资产编码成七个 ISO MP4 视频再现内容（从 180p 到 1080p）。 编码的资产可以动态打包成以下任一流协议：HLS、MPEG DASH 和平滑流。  图表顶部显示了不包含筛选器的资产的 HLS 清单（包含全部七个再现内容）。  左下角显示名为“ott”的筛选器已应用到 HLS 清单。 “ott”筛选器指定要删除所有不低于 1 Mbps 的比特率，因此将最差的两个质量级别从响应中剥除。 在右下角显示已应用名为“mobile”的筛选器的 HLS 清单。 “mobile”筛选器指定要删除分辨率大于 720p 的再现内容，因此将剥除两个 1080p 再现内容。

![再现内容筛选](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [动态打包](dynamic-packaging-overview.md)
- [流式处理终结点](streaming-endpoint-concept.md)
- [流式处理定位符](streaming-locators-concept.md)
- [流式处理策略](streaming-policy-concept.md)
- [内容密钥策略](content-key-policy-concept.md)
- [内容保护](content-protection-overview.md)
- [动态清单](filters-dynamic-manifest-overview.md)
- [筛选器](filters-concept.md)

> [!NOTE]
> Widevine 是 Google Inc. 提供的一项服务，受 Google，Inc. 的服务条款和隐私策略的约束。

## <a name="live-streaming"></a>实时传送视频流

使用 Azure 媒体服务可将直播活动传送到 Azure 云中的客户。 **直播活动**负责引入和处理实时视频源。 当你创建**活动事件**时，将创建一个输入终结点，你可以使用该终结点从远程编码器发送实时信号。 将流流入**实时事件**后，可以通过创建**资产**、**实时输出**和**流式处理定位符**来开始流式处理事件。 **实时输出**会将流存档到**资产**中，并通过**流式处理终结点**将其提供给查看者。 **实时事件**可以是以下两种类型之一：**传递**和**实时编码**。

下图说明了传递类型工作流：

![直通](./media/live-streaming/pass-through.svg)

- [实时传送视频流概述](live-streaming-overview.md)
- [直播活动和实时输出](live-events-outputs-concept.md)

## <a name="monitoring"></a>监视

### <a name="event-grid"></a>事件网格

若要查看作业的进度，应使用**事件网格**。 媒体服务还会发出实时事件类型。 使用事件网格，应用可以侦听和响应几乎来自所有 Azure 服务和自定义源的事件。 

- [处理事件网格事件](reacting-to-media-services-events.md)
- [架构](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

监视指标和诊断日志，以帮助你了解应用程序如何通过 Azure Monitor 执行。

- [指标和诊断日志](media-services-metrics-diagnostic-logs.md)
- [诊断日志架构](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>播放器客户端

你可以使用 Azure Media Player 在各种浏览器和设备上播放媒体服务流式传输的媒体内容。 Azure 媒体播放器采用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。 

- [Azure Media Player 概述](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [对远程文件和流视频进行编码 - REST](stream-files-tutorial-with-rest.md)
* [对上传的文件和流视频进行编码 - .NET](stream-files-tutorial-with-api.md)
* [实时流 - .NET](stream-live-tutorial-with-api.md)
* [分析视频 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 动态加密 - .NET](protect-with-aes128.md)
* [通过多重 DRM 进行动态加密 - .NET](protect-with-drm.md) 
