---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文将解答 Azure 媒体服务 v3 常见问题。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2019
ms.author: juliako
ms.openlocfilehash: 98e8c0ccd150776341e644f7565696e8fbd63e99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65556268"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure 媒体服务 v3 常见问题

本文将解答 Azure 媒体服务 (AMS) v3 常见问题。

## <a name="v3-apis"></a>v3 API

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>哪些 Azure 角色可以对 Azure 媒体服务资源执行操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体保留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 一个[作业](https://docs.microsoft.com/rest/api/media/jobs)是要应用到媒体服务的实际请求**转换**为给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一链接来枚举集合，而不依赖特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>哪些功能尚不可用 Azure 媒体服务 v3 中？

有关详细信息，请参阅[功能 v2 Api 方面的差距](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)。

## <a name="live-streaming"></a>实时传送视频流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在实时流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）、OBS Studio（免费应用）等）的功能。

## <a name="content-protection"></a>内容保护

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

1. 在生产环境中，需要获取安全令牌服务 (STS)（Web 服务），以便根据 HTTPS 请求颁发 JWT 令牌。 对于测试，可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 定义的 **GetTokenAsync** 方法中所示的代码。
2. 对用户进行身份验证后，播放器需要向 STS 发出请求以获取此类令牌，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

* 有关使用对称和非对称密钥运行 STS 的示例，请参阅 [https://aka.ms/jwt](https://aka.ms/jwt)。 
* 有关使用此类 JWT 令牌的、基于 Azure Media Player 的播放器示例，请参阅 [https://aka.ms/amtest](https://aka.ms/amtest)（展开“player_settings”链接可查看令牌输入）。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是利用 STS（安全令牌服务）：

在 STS 中，根据用户配置文件添加不同的声明（例如“高级用户”、“基本用户”、“免费试用版用户”）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 当然，对于不同的内容/资产，ContentKeyPolicyRestriction 包含相应的 RequiredClaims。

使用 Azure 媒体服务 Api 用于配置许可证/密钥传递和加密你的资产 (如中所示[此示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs))。

有关详细信息，请参阅：

- [内容保护概述](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，无法使用 Azure 门户来管理 v3 资源。 请使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或受支持的 [SDK](media-services-apis-overview.md#sdks) 之一。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

已从 AMS API 中删除 AssetFile，以便将媒体服务与存储 SDK 依赖项分开。 现在由存储而非媒体服务来保存属于存储的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](migrate-from-v2-to-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在建议使用服务器端存储加密（在默认情况下为打开状态）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
