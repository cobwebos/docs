---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文回答了有关 Azure 媒体服务 v3 的常见问题。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309190"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒体服务 v3 常见问题解答

本文回答了有关 Azure 媒体服务 v3 的常见问题。

## <a name="general"></a>常规

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>如何流式传输到 Apple iOS 设备？

确保路径末尾（URL **/Manifest**部分之后）有 **（格式_m3u8-aapl）** 告诉流源服务器返回 HTTP 实时流式处理 （HLS） 内容，供 Apple iOS 本机设备上使用。 有关详细信息，请参阅[传递内容](dynamic-packaging-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体预留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，我们建议您使用 10 个 S3 媒体保留单位 （MRUs） 预配帐户。 如果需要超过 10 个 S3 MR并网，请使用 Azure[门户](https://portal.azure.com/)打开支持票证。

有关详细信息，请参阅[缩放媒体处理](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个转换描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](https://docs.microsoft.com/rest/api/media/jobs)是媒体服务应用转换到输入视频或音频内容的实际请求。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已经上传、编码并发布了视频。 当我尝试流式传输视频时，为什么不会播放视频？

最常见的原因之一是，您没有尝试在"运行"状态中播放的流式处理终结点。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一个链接枚举集合，而不是依赖于特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[与 v2 API 有关的功能间隙](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](media-services-account-concept.md)。

## <a name="live-streaming"></a>实时传送视频流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>广播完成后如何停止直播？

可以从客户端或服务器端接近它。

#### <a name="client-side"></a>客户端

如果用户想在关闭浏览器时结束广播，则 Web 应用程序应提示用户。 这是 Web 应用程序可以处理的浏览器事件。

#### <a name="server-side"></a>服务器端

您可以通过订阅 Azure 事件网格事件来监视实时事件。 有关详细信息，请参阅[事件网格事件架构](media-services-event-schemas.md#live-event-types)。

可以：

* [订阅](reacting-to-media-services-events.md)流级[Microsoft.Media.LiveEventEncoder 断开连接](media-services-event-schemas.md#liveeventencoderdisconnected)事件，并监视一段时间内没有重新连接来停止和删除实时事件。
* [订阅](reacting-to-media-services-events.md)轨道级[检测信号](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有轨道的传入比特率降为 0，或者上次时间戳不再增加，则可以安全地关闭实时事件。 每个轨道每 20 秒都会出现一次检测信号事件，因此可能有点冗长。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>如何在实时流期间插入中断/视频和图像板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用程序提供切换源的功能，包括远程有线广播、切换工作室（iOS 上）和 OBS 工作室（免费应用程序）。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>我应该使用 AES-128 清除密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 两个系统之间的主要区别是，通过 AES 加密，内容密钥通过 TLS 传输到客户端，以便密钥在传输过程中加密，但无需任何其他加密（"在明确"中）。 因此，客户端播放器可以访问用于解密内容的密钥，并且可以在客户端上的网络跟踪中以纯文本形式查看。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，员工查看公司内分发的加密公司视频）。

与 AES-128 清除密钥相比，PlayReady、Widevine 和 FairPlay 等 DRM 系统都为用于解密内容的密钥提供了额外的加密级别。 除了 TLS 提供的任何传输级加密外，内容密钥还加密到受 DRM 运行时保护的密钥。 此外，解密是操作系统级别在安全的环境中处理的，在这样的环境中，恶意用户更难进行攻击。 对于查看器可能不是受信任方且需要最高级别的安全性的用例，我们建议 DRM。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情况下仅向具有特定权限的用户显示视频？

您不必使用任何特定的令牌提供程序，如 Azure 活动目录 （Azure AD）。 您可以使用非对称密钥加密创建自己的[JWT](https://jwt.io/)提供程序（所谓的安全令牌服务或 STS）。 在自定义 STS 中，可以根据业务逻辑添加声明。

确保颁发者、受众和声明在所有内容与 中使用的`ContentKeyPolicyRestriction``ContentKeyPolicy`值之间完全匹配。

有关详细信息，请参阅[使用媒体服务动态加密保护内容](content-protection-overview.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，我是如何以及从哪里获得 JWT 令牌的？

对于生产，您需要有安全令牌服务（即 Web 服务），该服务在 HTTPS 请求时发出 JWT 令牌。 对于测试，可以使用`GetTokenAsync`[Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)中定义的方法中显示的代码。

玩家在用户经过身份验证后向 STS 发出此类令牌的请求，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

有关使用对称键或非对称键运行 STS 的示例，请参阅[JWT 工具](https://aka.ms/jwt)。 有关使用此类 JWT 令牌基于 Azure 媒体播放器的播放器的示例，请参阅[Azure 媒体测试工具](https://aka.ms/amtest)。 （展开**player_settings**链接以查看令牌输入。

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是使用安全令牌服务。 在 STS 中，根据用户配置文件添加不同的声明（如"高级用户"、"基本用户"、"免费试用用户"）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 对于不同的内容或资产，`ContentKeyPolicyRestriction`将具有相应的`RequiredClaims`值。

使用 Azure 媒体服务 API 配置许可证/密钥传递并加密资产（[如本示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示）。

有关详细信息，请参阅：

- [内容保护概述](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>我应该使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 但是，HTTP 是流式处理的首选，因此您需要考虑包含混合内容的问题：

* 浏览器不允许混合内容。 但是，像 Silverlight 和 OSMF 插件这样的插件，适用于平滑和 DASH 确实允许它。 混合内容是一个安全问题，因为注入恶意 JavaScript 的能力受到威胁，这可能会使客户数据面临风险。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 当您播放混合内容时，SilverlightSS 技术需要清除混合内容警告。 FlashSS 技术无需混合内容警告即可处理混合内容。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

### <a name="what-about-live-streaming"></a>如何使用实时流？

通过将与程序关联的资产视为 VOD 资产，可以使用完全相同的设计和实现来帮助保护媒体服务中的实时流。 要提供实时内容的多 DRM 保护，在将资产与实时输出关联之前，对资产应用与 VOD 资产相同的设置/处理。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？

通常，客户在自己的数据中心或由 DRM 服务提供商托管的许可证服务器场中投资。 使用媒体服务内容保护，可以在混合模式下操作。 内容可以在媒体服务中托管和动态保护，而 DRM 许可证由媒体服务外部的服务器提供。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 您需要有一个 STS 才能发出这样的 JWT。 
* 不再需要在媒体服务中配置许可证传送服务。 配置`ContentKeyPolicy`时，您需要提供许可证获取 URL（用于 PlayReady、Widevine 和 FairPlay）。

> [!NOTE]
> Widevine 是 Google 提供的服务，受 Google 的服务条款和隐私政策的约束。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，可以使用 Azure[门户](https://portal.azure.com/)：

* 在媒体服务 v3 中管理[实时事件](live-events-outputs-concept.md)。 
* 查看（未管理）v3[资产](assets-concept.md)。 
* [获取有关访问 API 的信息](access-api-portal.md)。 

对于所有其他管理任务（例如，[转换和作业](transforms-jobs-concept.md)以及[内容保护](content-protection-overview.md)），请使用[REST API、Azure](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)或受支持的[SDK](media-services-apis-overview.md#sdks)之一。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

该`AssetFile`概念从媒体服务 API 中删除，以将媒体服务与存储 SDK 依赖项分开。 现在，Azure 存储（而不是媒体服务）保留属于存储 SDK 中的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在，我们建议您使用服务器端存储加密（默认情况下处于打开状态）。 有关详细信息，请参阅[Azure 存储服务加密，了解静态数据](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="offline-streaming"></a>离线流式处理

### <a name="fairplay-streaming-for-ios"></a>iOS 的公平播放流式处理

以下常见问题有助于为 iOS 解决脱机 FairPlay 流式处理问题。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>为什么在脱机模式期间只播放音频而不播放视频？

此行为似乎是示例应用专门设计的。 当在脱机模式下存在备用音轨（HLS 就是这种情况），iOS 10 和 iOS 11 默认为备用音轨。要补偿 FPS 脱机模式的此行为，请从流中删除备用音轨。 要在媒体服务上执行此操作，添加动态清单筛选器**仅音频 _false**。 换句话说，HLS URL 以 **.ism/清单（格式_m3u8-aapl，仅音频=false）** 结尾。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？

根据内容交付网络的缓存密钥设计，可能会缓存内容。 请清除缓存。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>除了 iOS 10 之外，iOS 11 上是否支持 FPS 离线模式？

是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？

从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 设备上的已下载/脱机文件结构是什么？

iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 

名称以破折号结尾后跟数字的第一个文件夹包含视频内容。 数值是视频再现的峰值带宽。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹`Data`包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![公平播放 iOS 示例应用的脱机文件结构](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

下面是一个示例 boot.xml 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>适用于安卓的宽文流

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何为某些客户端/用户提供持久许可证（启用脱机许可证），为其他客户端/用户和非持久许可证（脱机禁用）？ 我是否要复制内容并使用单独的内容键？

由于媒体服务 v3 允许资产具有多个`StreamingLocator`实例，因此您可以具有：

* 一`ContentKeyPolicy`个实例`license_type = "persistent"``ContentKeyPolicyRestriction`，其中有`"persistent"`声明，其`StreamingLocator`。
* 另`ContentKeyPolicy`一个`license_type="nonpersistent"`实例`ContentKeyPolicyRestriction`， 与`"nonpersistent`声明 的`StreamingLocator`" ， 及其 。
* 具有`StreamingLocator`不同`ContentKey`值的两个实例。

根据自定义 STS 的业务逻辑，在 JWT 令牌中发出不同的声明。 使用该令牌，只能获得相应的许可证，并且只能播放相应的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和媒体服务 DRM 安全级别之间的映射是什么？

谷歌的"威文DRM架构概述"定义了三个安全级别。 但是[，Widevine 许可证模板上的 Azure 媒体服务文档](widevine-license-template-overview.md)概述了五个安全级别（播放的客户端鲁棒性要求）。 本节介绍安全级别如何映射。

这两组安全级别都由 Google Widevine 定义。 区别在于使用级别：体系结构或 API。 Widevine API 中使用了这五种安全级别。 包含`content_key_specs`的对象`security_level`被反序列化，并通过 Azure 媒体服务 Widevine 许可证服务传递给 Widevine 全球交付服务。 下表显示了两组安全级别之间的映射。

| **在 Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1：** 所有内容处理、加密和控制都在受信任的执行环境 （TEE） 中执行。 在某些实现模型中，安全处理可能在不同的芯片中执行。|**security_level=5**：媒体的加密、解码和所有处理（压缩和未压缩）必须在硬件支持的 TEE 中处理。<br/><br/>**security_level_4**： 内容的加密和解码必须在硬件支持的 TEE 中执行。|
**安全级别 2**：加密（但不视频处理）在 TEE 中执行。 解密的缓冲区将返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但是，在级别 2 中，加密信息仍仅在 TEE 内处理。| **security_level=3**：密钥材料和加密操作必须在硬件支持的 TEE 中执行。 |
| **安全级别 3**：设备上没有 TEE。 可以采取适当的措施来保护主机操作系统上的加密信息和解密内容。 级别 3 实现可能还包括硬件加密引擎，但仅增强性能，而不是安全性。 | **security_level=2**：软件加密和模糊解码器是必需的。<br/><br/>**security_level=1**：需要基于软件的白盒加密。|

#### <a name="why-does-content-download-take-so-long"></a>为什么下载内容需要很长时间？

可通过两种方法提高下载速度：

* 启用内容交付网络，以便用户更有可能点击该网络，而不是内容下载的原点/流式处理终结点。 如果用户命中流式处理终结点，则每个 HLS 段或 DASH 片段都会动态打包和加密。 即使此延迟对于每个段或片段以毫秒为单位，但当您具有一小时的视频时，累积的延迟可能很大，并导致下载时间更长。
* 为用户提供选择性下载视频质量图层和音轨（而不是所有内容）的选项。 对于脱机模式，下载所有质量图层没有意义。 可通过两种方式实现此目的：

  * 客户端控制：播放器应用自动选择或用户选择视频质量图层和要下载的音轨。
  * 服务控制：您可以使用 Azure 媒体服务中的动态清单功能创建（全局）筛选器，该筛选器将 HLS 播放列表或 DASH MPD 限制为单个视频质量图层和选定的音轨。 然后，向用户显示的下载 URL 将包括此筛选器。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
