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
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 6e52a51c82529a98ef679dd747b9c8b7d177b660
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338818"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒体服务 v3 常见问题解答

本文将解答 Azure 媒体服务 (AMS) v3 常见问题。

## <a name="general"></a>常规

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体预留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](https://docs.microsoft.com/rest/api/media/jobs)是针对媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一链接来枚举集合，而不依赖特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[与 v2 API 之间的功能差距](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](media-services-account-concept.md)。

## <a name="live-streaming"></a>实时传送视频流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在实时流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）、OBS Studio（免费应用）等）的功能。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>应使用 AES-128 明文密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两个系统之间的主要差别在于，使用 AES 加密时，内容密钥将通过 TLS 传输到客户端，这样，密钥将经过传输中加密，但不会经过任何进一步的加密（“明文加密”）。 因此，用于解密内容的密钥可由客户端播放器访问，并且可以在客户端上的网络跟踪中以纯文本形式显示。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，加密员工观看的在公司内部分发的公司视频）。

DRM 系统 (如 PlayReady、Widevine 和 FairPlay) 对用于对内容进行解密的密钥提供额外级别的加密, 与 AES-128 明文密钥相比。 内容密钥将会加密成受 DRM 运行时保护的密钥，此外，还会受到 TLS 提供的任何传输级加密。 此外，解密是操作系统级别在安全的环境中处理的，在这样的环境中，恶意用户更难进行攻击。 在查看者可能不是受信任方且需要更高等级的安全性的用例中，建议使用 DRM。

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何仅向具有特定权限的用户显示视频，而不使用 Azure AD？

无需使用任何特定的令牌提供程序（如 Azure AD）。 可以使用非对称密钥加密来创建自己的[JWT](https://jwt.io/)提供程序（称为 STS、安全令牌服务）。 在自定义 STS 中，可以根据业务逻辑添加声明。

确保颁发者、受众和声明完全匹配 JWT 和 ContentKeyPolicy 中使用的 ContentKeyPolicyRestriction 之间的所有内容。

有关详细信息，请参阅[使用媒体服务动态加密来保护内容](content-protection-overview.md)。

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

1. 在生产环境中，需要获取安全令牌服务 (STS)（Web 服务），以便根据 HTTPS 请求颁发 JWT 令牌。 对于测试，可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 定义的 **GetTokenAsync** 方法中所示的代码。
2. 对用户进行身份验证后，播放器需要向 STS 发出请求以获取此类令牌，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

* 有关使用对称和非对称密钥运行 STS 的示例，请参阅 [https://aka.ms/jwt](https://aka.ms/jwt)。 
* 有关使用此类 JWT 令牌的、基于 Azure Media Player 的播放器示例，请参阅 [https://aka.ms/amtest](https://aka.ms/amtest)（展开“player_settings”链接可查看令牌输入）。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是利用 STS（安全令牌服务）：

在 STS 中，根据用户配置文件添加不同的声明（例如“高级用户”、“基本用户”、“免费试用版用户”）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 当然，对于不同的内容/资产，ContentKeyPolicyRestriction 包含相应的 RequiredClaims。

使用 Azure 媒体服务 API 来配置许可证/传送密钥以及加密资产（如[此示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)中所示）。

有关详细信息，请参阅：

- [内容保护概述](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 不过，最好是使用 HTTP 进行流式传输，因此需要考虑混合内容问题。

* 浏览器不允许混合内容。 但是 Silverlight 等插件和适用于平滑流与 DASH 的 OSMF 插件允许混合内容。 混合内容存在安全隐患 - 因为能够插入恶意 JavaScript 威胁，从而使客户数据处于风险之中。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 播放混合内容时，silverlightSS 技术需要清除混合内容警告。 flashSS 技术在没有混合内容警告的情况下处理混合内容。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

### <a name="what-about-live-streaming"></a>如何使用实时流？

可以使用完全相同的设计和实现来保护媒体服务中的实时传送视频流，方法是将与节目关联的资产视为 VOD 资产。 若要为实时内容提供多重 DRM 保护，请在将资产关联到实时输出之前，将相同的设置/处理应用到资产，就如同它是 VOD 资产一样。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？

通常，客户可能已在他们自己的数据中心或由 DRM 服务提供商托管的位置投资了许可证服务器场。 使用媒体服务内容保护，可以在混合模式下操作。 可以在媒体服务中托管和动态保护内容，而 DRM 许可证由 Azure 媒体服务外部的服务器传送。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 因此，需要通过一个 STS 颁发此类 JWT。 
* 不再需要在媒体服务中配置许可证传送服务。 需要在配置 ContentKeyPolicies 时，提供许可证获取 URL（针对 PlayReady、Widevine 和 FairPlay）。

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
