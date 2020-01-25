---
title: Azure Media Services v3 릴리스 정보 | Microsoft 문서
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Media Services v3의 최신 업데이트에 대한 정보를 제공합니다.
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
ms.openlocfilehash: e7770a3faef0ccf7ca0f5b8c56f98007b8d89436
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719834"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 릴리스 정보

>通过复制并粘贴以下 URL，获取有关何时通过复制和粘贴此 URL 来重新访问此页面的通知： `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` 到 RSS 源读者。

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="known-issues"></a>알려진 문제

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI 또는 지원되는 SDK 중 하나를 사용하세요.

자세한 내용은 [Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md#known-issues)을 참조하세요.

## <a name="november-2019"></a>2019년 11월

### <a name="live-transcription-preview"></a>实时脚本预览

Live 脚本现提供公共预览版，可在美国西部2地区使用。

Live 脚本旨在作为附加功能与实时事件结合使用。  它在传递和标准或高级编码实时事件上都受支持。  启用此功能后，服务将使用认知服务的[语音到文本](../../cognitive-services/speech-service/speech-to-text.md)功能将传入音频中的朗读字词转录为文本。 然后，可以在 MPEG-短线和 HLS 协议中将此文本连同视频和音频一起送达。 计费基于新的外接程序计量器，在实时事件处于 "正在运行" 状态时，它会产生额外的成本。  有关实时脚本和计费的详细信息，请参阅[实时](live-transcription.md)脚本

> [!NOTE]
> 目前，只能在美国西部2区域中使用预览功能。 它目前只支持英语（en-us）的口头字词。

### <a name="content-protection"></a>콘텐츠 보호

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

## <a name="september-2019"></a>2019년 9월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>实时事件的实时线性编码

媒体服务 v3 宣布推出实时事件实时线性编码的24小时 x 365 天的预览。

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们将宣布弃用*Azure Media Indexer*和*Azure Media Indexer 2 预览版*。 有关停用日期，请参阅此[旧版组件](../previous/legacy-components.md)主题。 [Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)替代了这些旧媒体处理器。

有关详细信息，请参阅[从 Azure Media Indexer 迁移和 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](../previous/migrate-indexer-v1-v2.md)。

## <a name="august-2019"></a>2019년 8월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>已为 Media Services 打开了南非区域对 

媒体服务现已在南部北部和南非非洲地区推出。

有关详细信息，请参阅[媒体服务 v3 所在的云和区域](azure-clouds-regions.md)。

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>弃用媒体处理器

我们宣布不推荐要停用的*Windows Azure 媒体编码器*（WAME）和*Azure 媒体编码器*（AME）媒体处理器。 有关停用日期，请参阅此[旧版组件](../previous/legacy-components.md)主题。

有关详细信息，请参阅[将 WAME 迁移到 Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334)并[将 AME 迁移到 Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335)。
 
## <a name="july-2019"></a>2019년 7월

### <a name="content-protection"></a>콘텐츠 보호

当流式处理使用令牌限制保护的内容时，最终用户需要获取作为密钥传递请求的一部分发送的令牌。 使用*令牌重播防护*功能，媒体服务客户可以设置一个限制，该限制可用于请求密钥或许可证。 有关详细信息，请参阅[令牌重播防护](content-protection-overview.md#token-replay-prevention)。

截止到7月，预览版功能仅在美国中部和美国西部提供。

## <a name="june-2019"></a>2019년 6월

### <a name="video-subclipping"></a>视频子剪辑

你现在可以在使用[作业](https://docs.microsoft.com/rest/api/media/jobs)对视频进行编码时剪裁或子剪辑视频。 

此功能适用于使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设或[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设生成的任何[转换](https://docs.microsoft.com/rest/api/media/transforms)。 

请参阅示例：

* [使用 .NET 子剪辑视频](subclip-video-dotnet-howto.md)
* [使用 REST 子剪辑视频](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019년 5월

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

### <a name="performance-improvements"></a>성능 향상

添加了更新，其中包括媒体服务性能改进。

* 已更新处理所支持的最大文件大小。 请参阅、[配额和限制](limits-quotas-constraints.md)。
* [编码加快了提高](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types)。

## <a name="april-2019"></a>2019년 4월

### <a name="new-presets"></a>新预设

* 已将[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)添加到内置分析器预设。
* 已将[ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)添加到内置编码器预设。 有关详细信息，请参阅[内容感知编码](content-aware-encoding.md)。 

## <a name="march-2019"></a>2019년 3월

动态打包现在支持杜比 Atmos。 有关详细信息，请参阅[动态打包支持的音频编解码器](dynamic-packaging-overview.md#audio-codecs)。

你现在可以指定将应用于流式处理定位符的资产或帐户筛选器的列表。 有关详细信息，请参阅[将筛选器与流式处理定位符相关联](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="february-2019"></a>2019년 2월

媒体服务 v3 现在在 Azure 国内云中受支持。 아직은 일부 클라우드에서 일부 기능을 사용할 수 없습니다. 자세한 내용은 [Azure Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

Media Services에 대한 Azure Event Grid 스키마에 [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) 이벤트가 추가되었습니다.

## <a name="january-2019"></a>2019년 1월

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard 및 MPI 파일 

Media Encoder Standard로 인코딩하여 MP4 파일을 생성하는 경우 새 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](dynamic-packaging-overview.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

## <a name="december-2018"></a>2018년 12월

V3 API의 GA 릴리스업데이트에는 다음이 포함됩니다.
       
* **PresentationTimeRange** 속성은 **자산 필터** 및 **계정 필터**에 대해 더 이상 '필수'가 아닙니다. 
* **작업** 및 **변환**의 $top 및 $skip 쿼리 옵션이 제거되었으며 $orderby가 추가되었습니다. 새 순서 지정 기능을 추가되면서, $top 및 $skip 옵션을 이전에 구현하지 않았어도 실수로 노출되는 것으로 확인되었습니다.
* 열거형 확장성을 다시 사용하도록 설정했습니다. 이 기능은 미리 보기 버전의 SDK에서 사용하도록 설정되며 GA 버전에서는 실수로 사용하지 않도록 설정되었습니다.
* 두 개의 미리 정의된 스트리밍 정책 이름이 바뀌었습니다. **SecureStreaming**은 이제 **MultiDrmCencStreaming**입니다. **SecureStreamingWithFairPlay**는 이제 **Predefined_MultiDrmStreaming**입니다.

## <a name="november-2018"></a>2018년 11월

현재 CLI 2.0 모듈이 [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50에 제공됩니다.

### <a name="new-commands"></a>새 명령

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
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - 미디어 예약 단위를 관리할 수 있습니다. 자세한 내용은 [미디어 예약 단위 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="new-features-and-breaking-changes"></a>새 기능과 주요 변경 내용

#### <a name="asset-commands"></a>자산 명령

- ```--storage-account``` 및 ```--container``` 인수가 추가되었습니다.
- ```az ams asset get-sas-url``` 명령에서 만료 시간(현재+23시간) 및 사용 권한(읽기)에 대한 기본값이 추가되었습니다.

#### <a name="job-commands"></a>작업 명령

- ```--correlation-data``` 및 ```--label``` 인수가 추가되었습니다.
- ```--output-asset-names```의 이름이 ```--output-assets```으로 바뀌었습니다. 이제 'assetName = label' 형식으로 공백으로 구분된 자산 목록을 허용합니다. 레이블이 없는 자산은 ‘assetName=’과 같이 보낼 수 있습니다.

#### <a name="streaming-locator-commands"></a>스트리밍 로케이터 명령

- ```az ams streaming locator``` 기본 명령이 ```az ams streaming-locator```로 바뀌었습니다.
- ```--streaming-locator-id``` 및 ```--alternative-media-id support``` 인수가 추가되었습니다.
- ```--content-keys argument``` 인수가 업데이트되었습니다.
- ```--content-policy-name```의 이름이 ```--content-key-policy-name```으로 바뀌었습니다.

#### <a name="streaming-policy-commands"></a>스트리밍 정책 명령

- ```az ams streaming policy``` 기본 명령이 ```az ams streaming-policy```로 바뀌었습니다.
- ```az ams streaming-policy create```에 암호화 매개 변수 지원이 추가되었습니다.

#### <a name="transform-commands"></a>변환 명령

- ```--preset-names``` 인수가 ```--preset```으로 바뀌었습니다. 이제 한 번에 1개의 출력/사전 설정만 설정할 수 있습니다(더 추가하려면 ```az ams transform output add```를 실행해야 함). 또한 사용자 정의 JSON에 경로를 전달하여 사용자 정의 StandardEncoderPreset을 설정할 수 있습니다.
- 제거할 출력 인덱스를 전달하여 ```az ams transform output remove```를 수행할 수 있습니다.
- ```az ams transform create``` 및 ```az ams transform output add``` 명령에 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 인수가 추가되었습니다.

## <a name="october-2018---ga"></a>2018년 10월 - GA

이 섹션에서는 AMS(Azure Media Services) 10월 업데이트에 대해 설명합니다.

### <a name="rest-v3-ga-release"></a>REST v3 GA 릴리스

[REST v3 GA 릴리스](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에는 라이브, 계정/자산 수준 매니페스트 필터 및 DRM 지원을 위한 더 많은 API가 포함되어 있습니다.

#### <a name="azure-resource-management"></a>Azure Resource 관리 

Azure Resource 관리가 지원되어 통합 관리 및 API 운영(이제 한 곳에 모든 기능 제공)가 가능합니다.

이 릴리스부터 Resource Manager 템플릿을 사용하여 라이브 이벤트를 만들 수 있습니다.

#### <a name="improvement-of-asset-operations"></a>자산 작업 개선 

다음과 같은 개선 사항이 도입되었습니다.

- HTTP(s) URL 또는 Azure Blob Storage SAS URL에서 수집합니다.
- 자산에 대한 자체 컨테이너 이름을 지정합니다. 
- 보다 쉬운 출력이 지원되어 Azure Functions로 사용자 지정 워크플로를 만들 수 있습니다.

#### <a name="new-transform-object"></a>새로운 변환 개체

새로운 **변환** 개체로 인해 인코딩 모델이 간소화됩니다. 새로운 개체를 통해 Resource Manager 템플릿 및 사전 설정 인코딩을 쉽게 만들고 공유할 수 있습니다. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 인증 및 RBAC

Azure AD 인증 및 RBAC(역할 기반 액세스 제어)를 통해 Azure AD에서 역할 또는 사용자별로 보안 변환, LiveEvent, 콘텐츠 키 정책 또는 자산을 사용할 수 있습니다.

#### <a name="client-sdks"></a>클라이언트 SDK  

Media Services v3에 지원되는 언어: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>라이브 인코딩 업데이트

다음과 같은 라이브 인코딩 업데이트가 도입되었습니다.

- 라이브에 대한 짧은 대기 시간(엔드투엔드 10초).
- RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
- RTMPS 보안 수집

    라이브 이벤트를 만들면 이제 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다. 
- 24시간 코드 변환 지원 
- SCTE35를 통한 RTMP의 광고 신호 지원 향상

#### <a name="improved-event-grid-support"></a>Event Grid 지원 향상

다음과 같은 Event Grid 지원 향상을 확인할 수 있습니다.

- Azure Event Grid 통합으로 Logic Apps 및 Azure Functions를 사용하여 보다 쉽게 개발할 수 있습니다. 
- 인코딩, 라이브 채널 등의 이벤트를 구독하세요.

### <a name="cmaf-support"></a>CMAF 지원

CMAF를 지원하는 Apple HLS(iOS 11+) 및 MPEG-DASH 플레이어에 대해 CMAF 및 'cbcs' 암호화가 지원됩니다.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA 릴리스가 8월에 발표되었습니다. 현재 지원되는 기능에 대한 자세한 내용은 [Video Indexer란?](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)을 참조하세요. 

### <a name="plans-for-changes"></a>변경 계획

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
라이브, 콘텐츠 키 정책, 계정/자산 필터, 스트리밍 정책을 비롯한 모든 기능에 대한 작업을 포함하고 있는 Azure CLI 2.0 모듈이며, 곧 출시될 예정입니다. 

### <a name="known-issues"></a>알려진 문제

Asset 또는 AccountFilters에 대한 API 미리 보기를 사용하는 고객만 다음 문제의 영향을 받습니다.

Media Services v3 CLI 또는 API를 통해 9월 28일에서 10월 12일 사이에 자산 또는 계정 필터를 생성한 경우에는 버전 충돌로 인해 모든 자산 및 계정 필터를 제거하고 다시 생성해야 합니다. 

## <a name="may-2018---preview"></a>2018년 5월 - 미리 보기

### <a name="net-sdk"></a>.NET SDK

.NET SDK 中提供了以下功能：

* 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial-with-api.md)을 참조하세요.
* 최종 사용자 디바이스에 콘텐츠를 게시하고 스트리밍하는 **스트리밍 로케이터**.
* 콘텐츠를 전송할 때 키 전송 및 콘텐츠 보호(DRM)를 구성하는 **스트리밍 정책** 및 **콘텐츠 키 정책**.
* 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **라이브 이벤트** 및 **라이브 스트리밍**.
* Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
* 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성하고 확장하는 **스트리밍 엔드포인트**.

### <a name="known-issues"></a>알려진 문제

* 작업을 제출할 때는 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일의 경로를 사용하여 원본 비디오를 수집하도록 지정할 수 있습니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [개요](media-services-overview.md)
- [媒体服务 v2 发行说明](../previous/media-services-release-notes.md)
