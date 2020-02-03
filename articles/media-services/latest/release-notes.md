---
title: Azure 媒体服务 v3 发行说明 | Microsoft Docs
description: 为了让大家随时了解最新的开发成果，本文提供了 Azure 媒体服务 v3 的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 12/13/2019
ms.author: juliako
ms.openlocfilehash: 52d8dda8b543e5bdf3ca88ae3784df65be3a2ba1
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962937"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure 媒体服务 v3 发行说明

>通过复制并粘贴以下 URL，获取有关何时通过复制和粘贴此 URL 来重新访问此页面的通知： `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` 到 RSS 源读者。

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="known-issues"></a>已知问题

> [!NOTE]
> 目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI 或支持的 SDK 之一。

有关详细信息，请参阅[有关从媒体服务 v2 迁移到 v3 的指导](migrate-from-v2-to-v3.md#known-issues)。

## <a name="january-2020"></a>2020 年 1 月

### <a name="improvements-in-media-processors"></a>媒体处理器的改进

- 改进对视频分析中的隔行扫描的支持–在将其发送到推理引擎之前，此类内容现在已正确取消隔行扫描。
- 使用 "最佳" 模式生成缩略图时，编码器现在会搜索超过30秒，以选择不单色的帧。
 
## <a name="november-2019"></a>2019 年 11 月

### <a name="live-transcription-preview"></a>实时脚本预览

Live 脚本现提供公共预览版，可在美国西部2地区使用。

Live 脚本旨在作为附加功能与实时事件结合使用。  它在传递和标准或高级编码实时事件上都受支持。  启用此功能后，服务将使用认知服务的[语音到文本](../../cognitive-services/speech-service/speech-to-text.md)功能将传入音频中的朗读字词转录为文本。 然后，可以在 MPEG-短线和 HLS 协议中将此文本连同视频和音频一起送达。 计费基于新的外接程序计量器，在实时事件处于 "正在运行" 状态时，它会产生额外的成本。  有关实时脚本和计费的详细信息，请参阅[实时](live-transcription.md)脚本

> [!NOTE]
> 目前，只能在美国西部2区域中使用预览功能。 它目前只支持英语（en-us）的口头字词。

### <a name="content-protection"></a>内容保护

现在，所有区域都提供了在9月份后的有限区域中发布的*令牌重播防护*功能。
媒体服务客户现在可以对用于请求密钥或许可证的相同令牌的次数设置限制。 有关详细信息，请参阅[令牌重播防护](content-protection-overview.md#token-replay-prevention)。

### <a name="new-recommended-live-encoder-partners"></a>新建议的实时编码器合作伙伴

添加了对 RTMP 实时流式处理的以下新建议合作伙伴编码器的支持：

- [Cambria Live 4。3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 和 Max 操作相机](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>文件编码增强功能

- 现在可以使用新的内容感知编码预设。 它使用内容感知编码生成一组 GOP 对齐的 Mp4。 根据给定的任何输入内容，该服务会对输入内容执行初始的轻型分析。 它将使用这些结果来确定最理想的层数、相应的比特率，以及用于通过自适应流式处理传递的分辨率设置。 此预设特别适用于低复杂性和中等复杂度的视频，其中的输出文件是低比特率，但仍会为查看器提供良好的体验。 输出将包含带有视频和音频交错的有文件的文件。 有关详细信息，请参阅[开放 API 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)。
- 改善了 Media Encoder Standard 中 sizer 的性能和多线程处理。 在特定情况下，客户应看到 5-40% VOD 编码之间的性能提升。 编码为多个比特率的低复杂性内容会显示最高的性能。 
- 现在，在使用基于时间的 GOP 设置时，标准编码会在 VOD 编码期间为可变帧速率（VFR）内容保留常规 GOP 节奏。  这意味着，如果客户提交的混合帧速率内容在 15-30 fps 之间有所不同，示例现在应查看在输出时计算为自适应比特率流式处理的文件。 这将提高在 HLS 或短划线交付时无缝切换跟踪的功能。 
-  改善了可变帧速率（VFR）源内容的 AV 同步

### <a name="video-indexer-video-analytics"></a>视频索引器，视频分析

- 使用 VideoAnalyzer 预设提取的关键帧现在采用视频的原始分辨率，而不是调整大小。 高分辨率关键帧提取提供原始质量的图像，并使你能够利用 Microsoft 计算机视觉和自定义视觉服务提供的基于映像的人工智能模型，从视频获得更多见解。

## <a name="september-2019"></a>2019 年 9 月

###  <a name="media-services-v3"></a>媒体服务 v3  

#### <a name="live-linear-encoding-of-live-events"></a>实时事件的实时线性编码

媒体服务 v3 宣布推出实时事件实时线性编码的24小时 x 365 天的预览。

###  <a name="media-services-v2"></a>媒体服务 v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们将宣布弃用*Azure Media Indexer*和*Azure Media Indexer 2 预览版*。 有关停用日期，请参阅此[旧版组件](../previous/legacy-components.md)主题。 [Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)替代了这些旧媒体处理器。

有关详细信息，请参阅[从 Azure Media Indexer 迁移和 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019 年 8 月

###  <a name="media-services-v3"></a>媒体服务 v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>已为 Media Services 打开了南非区域对 

媒体服务现已在南部北部和南非非洲地区推出。

有关详细信息，请参阅[媒体服务 v3 所在的云和区域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>媒体服务 v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们宣布不推荐要停用的*Windows Azure 媒体编码器*（WAME）和*Azure 媒体编码器*（AME）媒体处理器。 有关停用日期，请参阅此[旧版组件](../previous/legacy-components.md)主题。

有关详细信息，请参阅[将 WAME 迁移到 Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334)并[将 AME 迁移到 Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335)。
 
## <a name="july-2019"></a>2019 年 7 月

### <a name="content-protection"></a>内容保护

当流式处理使用令牌限制保护的内容时，最终用户需要获取作为密钥传递请求的一部分发送的令牌。 使用*令牌重播防护*功能，媒体服务客户可以设置一个限制，该限制可用于请求密钥或许可证。 有关详细信息，请参阅[令牌重播防护](content-protection-overview.md#token-replay-prevention)。

截止到7月，预览版功能仅在美国中部和美国西部提供。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-subclipping"></a>视频子剪辑

你现在可以在使用[作业](https://docs.microsoft.com/rest/api/media/jobs)对视频进行编码时剪裁或子剪辑视频。 

此功能适用于使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设或[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设生成的任何[转换](https://docs.microsoft.com/rest/api/media/transforms)。 

请参阅示例：

* [使用 .NET 子剪辑视频](subclip-video-dotnet-howto.md)
* [使用 REST 子剪辑视频](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019 年 5 月

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>媒体服务诊断日志和指标 Azure Monitor 支持

你现在可以使用 Azure Monitor 查看 Media Services 发出的遥测数据。

* 使用 Azure Monitor 诊断日志来监视 Media Services 密钥传送终结点发送的请求。 
* 监视媒体服务[流式处理终结点](streaming-endpoint-concept.md)发出的指标。   

有关详细信息，请参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)。

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>动态打包中的多音频跟踪支持 

当使用多个编解码器和语言的多个音频轨迹流式传输资产时，[动态打包](dynamic-packaging-overview.md)现在支持 HLS 输出的多个音频轨迹（版本4或更高版本）。

### <a name="korea-regional-pair-is-open-for-media-services"></a>韩国区域对为 Media Services 开放 

媒体服务现已在韩国中部和韩国南部区域推出。 

有关详细信息，请参阅[媒体服务 v3 所在的云和区域](azure-clouds-regions.md)。

### <a name="performance-improvements"></a>性能改进

添加了更新，其中包括媒体服务性能改进。

* 已更新处理所支持的最大文件大小。 请参阅、[配额和限制](limits-quotas-constraints.md)。
* [编码加快了提高](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-presets"></a>新预设

* 已将[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)添加到内置分析器预设。
* 已将[ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)添加到内置编码器预设。 有关详细信息，请参阅[内容感知编码](content-aware-encoding.md)。 

## <a name="march-2019"></a>2019 年 3 月

动态打包现在支持杜比 Atmos。 有关详细信息，请参阅[动态打包支持的音频编解码器](dynamic-packaging-overview.md#audio-codecs)。

你现在可以指定将应用于流式处理定位符的资产或帐户筛选器的列表。 有关详细信息，请参阅[将筛选器与流式处理定位符相关联](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019 年 2 月

媒体服务 v3 现在在 Azure 国内云中受支持。 目前，并非所有功能在所有云中都可用。 有关详细信息，请参阅[存在 Azure 媒体服务 v3 的云和区域](azure-clouds-regions.md)。

[Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) 事件已添加到媒体服务的 Azure 事件网格架构中。

## <a name="january-2019"></a>2019 年 1 月

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard 和 MPI 文件 

使用 Media Encoder Standard 编码生成 MP4 文件时，新的 .mpi 文件会生成并添加到输出资产中。 此 MPI 文件旨在提高[动态打包](dynamic-packaging-overview.md)和流式处理方案的性能。

不应修改或删除该 MPI 文件，也不应在存在（或不存在）此类文件的情况下采用服务中的任何依赖项。

## <a name="december-2018"></a>2018 年 12 月

V3 API 的正式版中的更新包括：
       
* **PresentationTimeRange** 属性不再是 **AssetFilters** 和 **AccountFilters** 所必需的。 
* 已删除 **Jobs** 和 **Transforms** 的 $top 和 $skip 查询选项，并添加了 $orderby。 在添加新排序功能的过程中，我们发现之前意外地公开了 $top 和 $skip 选项，尽管它们并未实现。
* 重新启用了枚举可扩展性。 此功能在 SDK 的预览版中已启用，但在正式版中被意外禁用。
* 已重命名两个预定义的流式处理策略。 **SecureStreaming** 现在名为 **MultiDrmCencStreaming**。 **SecureStreamingWithFairPlay** 现在名为 **Predefined_MultiDrmStreaming**。

## <a name="november-2018"></a>2018 年 11 月

CLI 2.0 模块现在可用于 [Azure 媒体服务 v3 正式版](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) - v 2.0.50。

### <a name="new-commands"></a>新命令

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - 用于管理媒体预留单位。 有关详细信息，请参阅[缩放媒体预留单位](media-reserved-units-cli-how-to.md)。

### <a name="new-features-and-breaking-changes"></a>新功能和重大更改

#### <a name="asset-commands"></a>资产命令

- 添加了 ```--storage-account``` 和 ```--container``` 参数。
- 在 ```az ams asset get-sas-url``` 命令中添加了到期时间默认值（现在时间 + 23 小时）和权限默认值（读取）。

#### <a name="job-commands"></a>作业命令

- 添加了 ```--correlation-data``` 和 ```--label``` 参数
- ```--output-asset-names``` 已重命名为 ```--output-assets```。 现在，它接受 'assetName=label' 格式的资产列表（以空格分隔）。 没有标签的资产可以采用以下格式发送：'assetName='。

#### <a name="streaming-locator-commands"></a>创建流式处理定位符命令

- ```az ams streaming locator``` 基本命令已替换为 ```az ams streaming-locator```。
- 添加了 ```--streaming-locator-id``` 和 ```--alternative-media-id support``` 参数。
- 更新了 ```--content-keys argument``` 参数。
- ```--content-policy-name``` 已重命名为 ```--content-key-policy-name```。

#### <a name="streaming-policy-commands"></a>流式处理策略命令

- ```az ams streaming policy``` 基本命令已替换为 ```az ams streaming-policy```。
- 在 ```az ams streaming-policy create``` 中添加了加密参数支持。

#### <a name="transform-commands"></a>转换命令

- ```--preset-names``` 参数已替换为 ```--preset```。 现在只能一次设置 1 个输出/预设（若要添加更多，必须运行 ```az ams transform output add```）。 此外，还可以通过将路径传递到自定义 JSON 来设置自定义 StandardEncoderPreset。
- 可以通过传递要删除的输出索引来执行 ```az ams transform output remove```。
- 在 ```az ams transform create``` 和 ```az ams transform output add``` 命令中添加了 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 参数。

## <a name="october-2018---ga"></a>October 2018 - GA

本部分介绍 Azure 媒体服务 (AMS) 的 10 月更新。

### <a name="rest-v3-ga-release"></a>REST v3 GA 发布

[REST v3 GA 发布](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)包含更多用于实时、帐户/资产级别清单筛选器和 DRM 支持的 API。

#### <a name="azure-resource-management"></a>Azure 资源管理 

对 Azure 资源管理的支持实现了统一的管理和操作 API（现在所有功能已整合在一起）。

从此版本开始，可以使用资源管理器模板创建直播活动。

#### <a name="improvement-of-asset-operations"></a>改善资产操作 

引入了以下改进：

- 从 HTTP(s) URL 或 Azure Blob 存储 SAS URL 中引入。
- 为资产指定自己的容器名称。 
- 使用 Azure Functions 创建自定义工作流支持更轻松的输出。

#### <a name="new-transform-object"></a>新转换对象

新转换对象简化编码模型。 通过新对象，可以轻松创建和共享编码资源管理器模板和预设。 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 身份验证和 RBAC

Azure AD 身份验证和基于角色的访问控制 (RBAC) 通过 Azure AD 中的角色或用户启用安全转换、直播活动、内容密钥策略或资产。

#### <a name="client-sdks"></a>客户端 SDK  

媒体服务 v3 支持的语言：NET Core、Java、Node.js、Ruby、Typescript、Python、Go。

#### <a name="live-encoding-updates"></a>实时编码更新

引入了以下实时编码更新：

- 针对实时传送视频的新的低延迟模式（10 秒端到端）。
- 改进的 RTMP 支持（提高了稳定性并提供了更多的源编码器支持）。
- RTMPS 安全引入。

    创建直播活动时，现在会得到 4 个引入 URL。 这 4 个引入 URL 几乎是相同的，具有相同的流式处理令牌 (AppId)，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。 
- 24 小时转码支持。 
- 通过 SCTE35 改进了 RTMP 中的广告信号支持。

#### <a name="improved-event-grid-support"></a>改进了事件网格支持

可以看到以下事件网格支持改进：

- 可通过逻辑应用和 Azure Functions 更轻松地开发 Azure 事件网格集成。 
- 订阅事件的编码、直播频道等。

### <a name="cmaf-support"></a>CMAF 支持

CMAF 和“cbcs”加密支持 Apple HLS (iOS 11+) 以及支持 CMAF 的 MPEG-DASH 播放器。

### <a name="video-indexer"></a>视频索引器

视频索引器 GA 版本于 8 月份发布。 有关当前支持的功能的新信息，请参阅[什么是视频索引器](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)。 

### <a name="plans-for-changes"></a>更改计划

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
包含所有功能（包括 Live、内容密钥策略、帐户/资产筛选器、流式处理策略）的操作的 Azure CLI 2.0 模块即将推出。 

### <a name="known-issues"></a>已知问题

只有使用资产或 AccountFilter 预览版 API 的客户才会受到以下问题的影响。

如果你使用媒体服务 v3 CLI 或 API 在 9 月 28 日到 10 月 12 日之间创建了资产或帐户筛选器，因版本冲突，需要删除所有资产和 AccountFilter，并重新创建它们。 

## <a name="may-2018---preview"></a>2018 年 5 月 - 预览版

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中提供了以下功能：

* 转换和作业，用于对媒体内容来进行编码或分析。 有关示例，请参阅[流式传输文件](stream-files-tutorial-with-api.md)和[分析](analyze-videos-tutorial-with-api.md)。
* **流式处理定位符**，用于发布内容并将其流式传输到最终用户设备
* **流式处理策略**和**内容密钥策略**，用于在传送内容时配置密钥传递和内容保护 (DRM)。
* **直播活动**和**实时输出**，用于配置实时传送视频流内容的引入和归档。
* 资产，用于在 Azure 存储中存储和发布媒体内容。 
* **流式处理终结点**，用于配置和缩放实时和点播媒体内容的动态打包、加密和流式处理。

### <a name="known-issues"></a>已知问题

* 提交作业时，可以指定使用 HTTPS URL、SAS URL 或位于 Azure Blob 存储中的文件路径引入源视频。 目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

- [概述](media-services-overview.md)
- [媒体服务 v2 发行说明](../previous/media-services-release-notes.md)
