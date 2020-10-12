---
title: Azure 媒体服务打包和来源错误 | Microsoft Docs
description: 本主题描述 Azure 媒体服务流式处理终结点（来源）服务可能会出现的错误。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8442c52052a2016da002d392c6fe0834b3813229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295319"
---
# <a name="streaming-endpoint-origin-errors"></a>流式处理终结点（来源）错误

本主题描述 Azure 媒体服务[流式处理终结点服务](streaming-endpoint-concept.md)可能会出现的错误。

## <a name="400-bad-request"></a>400 错误的请求

请求包含无效的信息，出于以下原因之一被拒绝，错误代码如下：

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 语法或格式错误。 示例包括无效类型、无效片段或无效轨迹的请求。 |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|请求的 URL 中没有加密标记。 CMAF 请求要求在 URL 中提供加密标记。 配置了多个加密类型的其他协议还要求使用加密标记来消除歧义。 |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|履行请求的存储请求失败并出现“错误的请求”错误。 |

## <a name="403-forbidden"></a>403 禁止访问

出于以下原因之一，不允许该请求：

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|履行请求的存储请求失败并出现“身份验证失败”。 如果存储密钥已轮换，并且服务无法同步存储密钥，则可能会发生这种情况。 <br/><br/>请在 Azure 门户中转到[帮助 + 支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以联系 Azure 支持人员。|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |存储操作错误，访问因帐户权限不足而失败。 |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |履行请求的存储请求因存储帐户禁用而失败。 |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |存储操作错误，访问因一般错误而失败。 |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |由于 StreamingPolicy 中的配置不当，输出格式被阻止。 |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |内容需要加密，输出格式需要传送策略。 |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |未在传送策略设置中设置加密。 |

## <a name="404-not-found"></a>404 未找到

操作正在尝试处理一个不再存在的资源。 例如，该资源可能已删除。

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |找不到请求的轨迹。 |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |找不到请求的资源。 |
|MPE_UNAUTHORIZED |0x80890244 |访问未经授权。 |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |找不到请求的时间戳。 |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |找不到请求的动态清单筛选器。 |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |请求的片段索引超出了有效范围。 |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |找不到用于获取 moov 缓冲区的实时媒体条目。 |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |在请求的时间找不到特定轨迹的片段。<br/><br/>该片段可能不在存储中。 请尝试使用其他某个可能具有片段的呈现层。 |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |在清单中找不到请求的比特率的媒体条目。 <br/><br/>播放器可能请求了不在清单中的特定比特率的视频轨迹。|
|MPE_METADATA_NOT_FOUND |0x80890257 |在清单中找不到某些元数据，或者在存储中找不到变基。 |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |存储操作错误，找不到资源。 |

## <a name="409-conflict"></a>409 冲突

在 `PUT` 或 `POST` 操作中为某个资源提供的 ID 已被现有的资源占用。 请使用该资源的另一个 ID 来解决此问题。

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |存储操作错误，冲突错误。  |

## <a name="410"></a>410

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|对于实时传送视频流，如果筛选器的 forceEndTimestamp 设置为 true，则开始或结束时间戳将会超出当前的 DVR 窗口。|

## <a name="412-precondition-failure"></a>412 前提条件失败

操作指定的 eTag 不同于服务器上的可用版本，即，发生了乐观并发错误。 请在读取资源的最新版本并更新请求中的 eTag 之后重试该请求。

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |请求的片段未准备就绪。|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|存储操作错误，前提条件失败。|

## <a name="415-unsupported-media-type"></a>415 不支持的媒体类型

客户端发送的有效负载格式不是支持的格式。

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 不应对已加密的内容应用加密。|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |加密对于输入格式无效。|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 传送策略类型无效。|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |可能以多种输出格式共享了原始设置。|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|媒体格式或类型不受支持。 例如，媒体服务不支持 64 个以上的质量级别计数。 在 FLV 视频标记中，媒体服务不支持包含多个 SPS 和多个 PPS 的视频帧。|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| 所请求资产的输入格式不受支持。 媒体服务支持平滑流（实时流）、MP4 (VoD) 和渐进式下载格式。|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|请求的输出格式不受支持。 媒体服务支持平滑流、DASH（CSF、CMAF）、HLS（v3、v4、CMAF）和渐进式下载格式。|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|遇到了不受支持的加密类型。|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|输出格式不支持请求的媒体类型。 支持的类型为视频、音频或“SUBT”字幕。|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|使用与输出格式不兼容的媒体格式编码了源资产媒体。|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|使用与输出格式不兼容的视频格式编码了源资产。 支持 H.264、AVC、H.265（HEVC、hev1 或 hvc1）。|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|使用与输出格式不兼容的音频格式编码了源资产。 支持的音频格式为 AAC、E-AC3 (DD+)、Dolby DTS。|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|源保护的资产无法转换为该输出格式。|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|输出格式不支持保护格式。|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|输入格式不支持保护格式。|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|视频 NAL 单元无效，例如，示例中只有第一个 NAL 可以是 AUD。|
|MPE_INVALID_NALU_SIZE|0x80890260|NAL 单元大小无效。|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|NAL 单元长度值无效。|
|MPE_FILTER_INVALID|0x80890236|动态清单筛选器无效。|
|MPE_FILTER_VERSION_INVALID|0x80890237|筛选器版本无效或不受支持。|
|MPE_FILTER_TYPE_INVALID|0x80890238|筛选器类型无效。|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|筛选器指定的范围无效。|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|筛选器指定的轨迹属性无效。|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|筛选器指定的呈现窗口长度无效。|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|筛选器指定的实时后退值无效。|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|旧式筛选器仅支持一个 absTimeInHNS 元素。|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|应用筛选器后根本没有其他流。|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|实时后退值超出了 DVR 窗口。|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|实时后退值大于呈现窗口。|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|超出了允许的最大默认筛选器数目 (10)。|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|组合请求筛选器中不允许使用多个第一视频质量运算符。|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|第一个质量比特率属性的数字必须是 1。|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS 片段持续时间必须小于 DVR 窗口和 HLS 后退值的 1/3。|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|片段持续时间必须小于或等于大约 20 秒，否则输入质量级别不会经过时间对齐。|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS 特定的错误，找不到 ReservedBox，但在分析 DTS 块期间它应该出现在 DTSSpecficBox 中。|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 特定的错误，分析 DTS 块期间在 DTSSpecficBox 中找不到通道。|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 特定的错误，DTSSpecficBox 中的样本类型不匹配。|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 特定的错误，已设置多资产，但 DTSH 样本类型不匹配。|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 特定的错误，核心流大小无效。|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 特定的错误，样本分辨率无效。|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 特定的错误，子流扩展索引无效。|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 特定的错误，子流块号无效。|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 特定的错误，采样频率无效。|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 特定的错误，子流扩展中的参考时钟代码无效。|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 特定的错误，设置的扬声器重新映射数目无效。|

有关加密的文章和示例，请参阅：

- [概念：内容保护](content-protection-overview.md)
- [概念：内容密钥策略](content-key-policy-concept.md)
- [概念：流式处理策略](streaming-policy-concept.md)
- [示例：使用 AES 加密进行保护](protect-with-aes128.md)
- [示例：使用 DRM 进行保护](protect-with-drm.md)

有关筛选器的指导，请参阅：

- [概念：动态清单](filters-dynamic-manifest-overview.md)
- [概念：筛选器](filters-concept.md)
- [示例：使用 REST API 创建筛选器](filters-dynamic-manifest-rest-howto.md)
- [示例：使用 .NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [示例：使用 CLI 创建筛选器](filters-dynamic-manifest-cli-howto.md)

有关实时流的文章和示例，请参阅：

- [概念：实时传送视频流概述](live-streaming-overview.md)
- [概念：实时事件和实时输出](live-events-outputs-concept.md)
- [示例：实时传送视频流教程](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 不符合范围要求

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|存储操作错误，返回了 HTTP 416 错误，范围无效。|

## <a name="500-internal-server-error"></a>500 内部服务器错误

在处理请求期间，媒体服务会遇到一些阻止处理继续执行的错误。  

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|已从 Winhttp 错误代码 ERROR_WINHTTP_TIMEOUT (0x00002ee2) 接收并转换。|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|已从 Winhttp 错误代码 ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe) 接收并转换。|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|已从 Winhttp 错误代码 ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7) 接收并转换。|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|存储操作错误，HTTP 500 错误之一的常规 InternalError。|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|存储操作错误，HTTP 500 错误之一的常规 OperationTimedOut。|
|MPE_STORAGE_FAILURE|0x808900F2|存储操作错误，除 InternalError 或 OperationTimedOut 以外的其他 HTTP 500 错误。|

## <a name="503-service-unavailable"></a>503 服务不可用

服务器当前无法接收请求。 导致此错误的可能原因是向服务发出了过多的请求。 对于发出过多服务请求的应用程序，媒体服务限制机制将限制其资源使用量。

> [!NOTE]
> 查看错误消息和错误代码字符串，获取收到 503 错误的原因的更多详细信息。 此错误并不始终意味着限制。
> 

|错误代码|十六进制值 |错误说明|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|存储操作错误，收到了 HTTP 服务器繁忙错误 503。|

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>另请参阅

- [编码错误代码](/rest/api/media/jobs/get#joberrorcode)
- [Azure 媒体服务概念](concepts-overview.md)
- [配额和限制](limits-quotas-constraints.md)

## <a name="next-steps"></a>后续步骤

[示例：使用 .NET 访问 ApiException 中的错误代码和消息](configure-connect-dotnet-howto.md#connect-to-the-net-client)
