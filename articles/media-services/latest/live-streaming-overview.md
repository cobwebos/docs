---
title: Azure 媒体服务 v3 实时传送视频流概述 |Microsoft Docs
description: 本文概述如何使用 Azure 媒体服务 v3 实时传送视频流。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/12/2019
ms.author: juliako
ms.openlocfilehash: b366262ce7849658eb84444d873956c25ab38804
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244663"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>使用 Azure 媒体服务 v3 实时传送视频流

使用 Azure 媒体服务可将直播活动传送到 Azure 云中的客户。 若要使用媒体服务流式传输直播活动，需要以下组件：  

- 一个相机，用于捕获直播活动。<br/>有关设置建议，请查看[简单且可移植的事件视频设备设置]( https://link.medium.com/KNTtiN6IeT)。

    如果你无权访问照相机，则可以使用[Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm)之类的工具从视频文件生成实时源。
- 一个实时视频编码器，用于将相机（或其他设备，例如便携式计算机）的信号转换为可发送到媒体服务的贡献源。 贡献源可包括与广告相关的信号，例如 SCTE-35 标记。<br/>有关推荐的实时传送视频流编码器的列表，请参阅[实时传送视频流编码器](recommended-on-premises-live-encoders.md)。 此外，请查看此博客：[包含 OBS 的实时流式处理生产](https://link.medium.com/ttuwHpaJeT)。
- 媒体服务中的组件，用于引入、预览、打包、记录、加密直播活动并将其广播给客户，或者广播给 CDN 进行进一步分发。

本文概述了如何使用媒体服务实时传送视频流，并提供指向其他相关文章的链接。
 
> [!NOTE]
> 目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或受支持的 [SDK](media-services-apis-overview.md#sdks) 之一。

## <a name="dynamic-packaging"></a>动态打包

借助媒体服务，你可以利用[动态打包](dynamic-packaging-overview.md)功能，通过此功能可以从发送到服务的发布源中按[MPEG 破折号、HLS 和平滑流式处理格式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)预览和广播实时流。 观看者可以使用任何与 HLS、DASH 或平滑流式处理兼容的播放器播放实时流。 可以使用 Web 应用程序或移动应用程序中的 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) 传送采用上述任何协议的流。

## <a name="dynamic-encryption"></a>动态加密

动态加密可让你通过 AES-128 或三个主要数字版权管理（DRM）系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中的任何一个来动态加密你的实时或点播内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 有关详细信息，请参阅[动态加密](content-protection-overview.md)。

> [!NOTE]
> Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="dynamic-manifest"></a>动态清单

动态筛选用于控制向播放机发送的磁道、格式、比特率和表示时间窗口的数量。 有关详细信息，请参阅[筛选器和动态清单](filters-dynamic-manifest-overview.md)。

## <a name="live-event-types"></a>实时事件类型

[直播活动](https://docs.microsoft.com/rest/api/media/liveevents)负责引入和处理实时视频源。 可以将实时事件设置为*传递*（本地实时编码器发送多比特率流）或*实时编码*（本地实时编码器发送单比特率流）。 有关媒体服务 v3 中的实时流式处理的详细信息，请参阅[实时事件和实时输出](live-events-outputs-concept.md)。

### <a name="pass-through"></a>直通

![直通](./media/live-streaming/pass-through.svg)

使用传递**实时事件**时，你依赖于本地实时编码器生成多比特率视频流，并将其作为贡献源发送到实时事件（使用 RTMP 或零散的输入协议）。 实时事件随后会通过传入视频流进入动态包装器（流式处理终结点），而无需任何进一步的转码。 此类直通实时事件针对长时间运行的实时事件或24x365 线性实时流进行了优化。 

### <a name="live-encoding"></a>实时编码  

![实时编码](./media/live-streaming/live-encoding.svg)

在媒体服务中使用云编码时，可以配置本地实时编码器，以便将单比特率视频作为贡献源（最32Mbps 聚合）发送到实时事件（使用 RTMP 或零散的输入协议）。 实时事件将传入的单比特率流转码为[多比特率视频流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，同时提供不同的分辨率来改善交付，并使其可通过行业标准协议（例如 MPEG-短线、Apple HTTP LIVE STREAMING （HLS）和 Microsoft 平滑流式处理）交付到播放设备。 

### <a name="live-transcription-preview"></a>实时脚本（预览）

Live 脚本是一项功能，可将其用于传递或实时编码的实时事件。 有关详细信息，请参阅[实时](live-transcription.md)脚本。 启用此功能后，服务将使用认知服务的[语音到文本](../../cognitive-services/speech-service/speech-to-text.md)功能将传入音频中的朗读字词转录为文本。 然后，可以在 MPEG-短线和 HLS 协议中将此文本连同视频和音频一起送达。

> [!NOTE]
> 目前，可以在美国西部2中将实时脚本作为预览功能提供。

## <a name="live-streaming-workflow"></a>实时传送视频流工作流

若要了解媒体服务 v3 中的实时流式处理工作流，必须先查看并了解以下概念： 

- [流式处理终结点](streaming-endpoint-concept.md)
- [直播活动和实时输出](live-events-outputs-concept.md)
- [流式处理定位符](streaming-locators-concept.md)

### <a name="general-steps"></a>一般步骤

1. 在媒体服务帐户中，确保**流式处理终结点**（源）正在运行。 
2. 创建[直播活动](live-events-outputs-concept.md)。 <br/>创建事件时，可以将其启动方式指定为自动启动。 或者，可以在准备好开始流式传输后，启动事件。<br/> 如果将 autostart 设置为 true，则直播活动会在创建后立即启动。 只要直播活动开始运行，就会开始计费。 必须显式对直播活动资源调用停止操作才能停止进一步计费。 有关详细信息，请参阅[直播活动状态和计费](live-event-states-billing.md)。
3. 获取引入 URL，并配置本地编码器以使用 URL 发送发布内容源。<br/>请参阅[推荐的实时编码器](recommended-on-premises-live-encoders.md)。
4. 获取预览 URL 并使用它验证来自编码器的输入是否实际接收。
5. 创建新的**资产**对象。 

    每个实时输出都与一个资产关联，用于将视频记录到关联的 Azure blob 存储容器中。 
6. 创建**实时输出**，并使用你创建的资产名称，以便可以将流存档到资产中。

    实时输出在创建时启动，在删除后停止。 删除实时输出时，不会删除资产中的基础资产和内容。
7. 使用[内置流式处理策略类型](streaming-policy-concept.md)创建**流式处理定位符**。

    若要发布实时输出，必须为关联的资产创建流式处理定位符。 
8. 列出流式处理定位器的路径，以取回要使用的 URL（这些是确定性的）。
9. 获取要从中进行流式**处理的流式处理终结点**（源）的主机名。
10. 将步骤 8 中的 URL 与步骤 9 中的主机名合并，获取完整的 URL。
11. 如果希望停止查看**直播活动**，则需要停止流式处理活动并删除**流式处理定位符**。
12. 如果已完成流式处理事件，并想要清理先前设置的资源，请遵循以下过程。

    * 停止从编码器推送流。
    * 停止直播活动。 直播活动在停止后，不会产生任何费用。 当需要重新启动它时，它会采用相同的引入 URL，因此，无需重新配置编码器。
    * 除非想要继续以点播流形式提供直播活动的存档，否则可以停止流式处理终结点。 如果直播活动处于停止状态，则不会产生任何费用。

实时输出要存档到的资产，在删除实时输出时，会自动成为按需资产。 必须先删除所有实时输出，然后才能停止实时事件。 可以使用可选的标志[removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body)自动删除停止时的实时输出。 

> [!TIP]
> 请参阅[实时流式处理教程](stream-live-tutorial-with-api.md)，这篇文章介绍了实现上述步骤的代码。

## <a name="other-important-articles"></a>其他重要文章

- [推荐的实时编码器](recommended-on-premises-live-encoders.md)
- [使用云 DVR](live-event-cloud-dvr.md)
- [实时事件类型功能比较](live-event-types-comparison.md)
- [状态和计费](live-event-states-billing.md)
- [滞后时间](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [实时传送视频流教程](stream-live-tutorial-with-api.md)
* [有关从媒体服务 v2 迁移到 v3 的指导](migrate-from-v2-to-v3.md)
