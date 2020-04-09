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
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886817"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒体服务 v3 常见问题解答

本文将解答 Azure 媒体服务 (AMS) v3 常见问题。

## <a name="general"></a>常规

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-you-stream-to-apple-ios-devices"></a>如何流式传输到 Apple iOS 设备？

请确保您的路径末尾（URL 的"/清单"部分之后）有"（格式_m3u8-aapl）"，告诉流源服务器返回 HLS 内容，供 Apple iOS 本机设备上使用（有关详细信息，请参阅[交付内容](dynamic-packaging-overview.md)）。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体预留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](https://docs.microsoft.com/rest/api/media/jobs)是针对媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>我已经上传、编码并发布了视频。 为什么在我尝试对视频进行流式处理时，它不播放？

最常见的原因之一是，没有“正在运行”状态下从其播放的流式处理终结点。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一链接来枚举集合，而不依赖特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[与 v2 API 之间的功能差距](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](media-services-account-concept.md)。

## <a name="live-streaming"></a>实时传送视频流 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>广播完成后如何停止直播？

可以从客户端或服务器端接近它。

#### <a name="client-side"></a>客户端

如果用户要关闭浏览器，则 Web 应用程序应提示用户结束广播。 这是 Web 应用程序可以处理的浏览器事件。

#### <a name="server-side"></a>服务器端

您可以通过订阅事件网格事件来监视实时事件。 有关详细信息，请参阅[事件网格事件架构](media-services-event-schemas.md#live-event-types)。

* 您可以[订阅](reacting-to-media-services-events.md)流级[Microsoft.Media.LiveEventEncoder 断开连接](media-services-event-schemas.md#liveeventencoderdisconnected)，并监视一段时间内没有重新连接来停止和删除实时事件。
* 或者，您可以[订阅](reacting-to-media-services-events.md)轨道级别[检测信号](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有轨道的传入比特率降至 0;则为 0。或上次时间戳不再增加，那么您也可以安全地关闭实时事件。 每个轨道每 20 秒都会出现一次检测信号事件，因此可能有点冗长。

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在实时流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）、OBS Studio（免费应用）等）的功能。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>应使用 AES-128 明文密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两个系统之间的主要差别在于，使用 AES 加密时，内容密钥将通过 TLS 传输到客户端，这样，密钥将经过传输中加密，但不会经过任何进一步的加密（“明文加密”）。 因此，用于解密内容的密钥可由客户端播放器访问，并且可以在客户端上的网络跟踪中以纯文本形式显示。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，加密员工观看的在公司内部分发的公司视频）。

与 AES-128 清除密钥相比，PlayReady、Widevine 和 FairPlay 等 DRM 系统都为解密内容的密钥提供了额外的加密级别。 内容密钥将会加密成受 DRM 运行时保护的密钥，此外，还会受到 TLS 提供的任何传输级加密。 此外，解密是操作系统级别在安全的环境中处理的，在这样的环境中，恶意用户更难进行攻击。 在查看者可能不是受信任方且需要更高等级的安全性的用例中，建议使用 DRM。

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情况下仅向具有特定权限的用户显示视频？

无需使用任何特定的令牌提供程序（例如 Azure AD）。 可以使用非对称密钥加密创建自己的 [JWT](https://jwt.io/) 提供程序（所谓 STS，安全令牌服务）。 在自定义 STS 中，可以根据业务逻辑添加声明。

确保颁发者、受众和声明在 JWT 中的内容和 ContentKeyPolicy 中使用的 ContentKeyPolicyRestriction 之间完全匹配。

有关详细信息，请参阅[使用媒体服务动态加密保护内容](content-protection-overview.md)。

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

1. 在生产环境中，需要获取安全令牌服务 (STS)（Web 服务），以便根据 HTTPS 请求颁发 JWT 令牌。 对于测试，可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 定义的 **GetTokenAsync** 方法中所示的代码。
2. 对用户进行身份验证后，播放器需要向 STS 发出请求以获取此类令牌，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

* 有关运行 STS 的示例，请使用对称键和非对称键，请参阅[https://aka.ms/jwt](https://aka.ms/jwt)。 
* 有关基于 Azure 媒体播放器使用此类 JWT 令牌的播放器的示例，[https://aka.ms/amtest](https://aka.ms/amtest)请参阅（展开"player_settings"链接以查看令牌输入）。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是利用 STS（安全令牌服务）：

在 STS 中，根据用户配置文件添加不同的声明（例如“高级用户”、“基本用户”、“免费试用版用户”）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 当然，对于不同的内容/资产，ContentKeyPolicyRestriction 包含相应的 RequiredClaims。

使用 Azure 媒体服务 API 配置许可证/密钥传递并加密资产（[如本示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示）。

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

> [!NOTE]
> Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，可以使用 Azure[门户](https://portal.azure.com/)：

* 管理媒体服务 v3[实时事件](live-events-outputs-concept.md)， 
* 视图（未管理） v3[资产](assets-concept.md)， 
* [获取有关访问 API 的信息](access-api-portal.md)。 

对于所有其他管理任务（例如，[转换、作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用[REST API](https://docs.microsoft.com/rest/api/media/) [、CLI](https://aka.ms/ams-v3-cli-ref)或受支持的[SDK](media-services-apis-overview.md#sdks)之一。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

已从 AMS API 中删除 AssetFile，以便将媒体服务与存储 SDK 依赖项分开。 现在由存储而非媒体服务来保存属于存储的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在建议使用服务器端存储加密（在默认情况下为打开状态）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="offline-streaming"></a>离线流式处理

### <a name="fairplay-streaming-for-ios"></a>iOS 的公平播放流式处理

以下常见问题为 iOS 的脱机 FairPlay 流提供故障排除帮助：

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>为什么在脱机模式期间只播放音频而不播放视频？

此行为似乎是示例应用专门设计的。 当在脱机模式下存在备用音轨（HLS 就是这种情况），iOS 10 和 iOS 11 默认为备用音轨。要补偿 FPS 脱机模式的此行为，请从流中删除备用音轨。 若要在媒体服务中完成此操作，请添加动态清单筛选器“audio-only=false”。 换言之，HLS URL 将以 .ism/manifest(format=m3u8-aapl,audio-only=false) 结尾。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？

根据内容分发网络 (CDN) 缓存键的设计，可能会缓存该内容。 请清除缓存。

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>除 iOS 10 之外，iOS 11 是否也支持 FPS 脱机模式？

是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？

从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 设备上的已下载/脱机文件结构是什么？

iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 第一个文件夹（文件名以破折号加数字结尾）包含视频内容。 数值是“PeakBandwidth”视频呈现形式。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹（文件名为“Data”）包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![脱机 FairPlay iOS 示例应用文件结构](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

示例 boot.xml 文件：

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>如何为某些客户端/用户传送永久许可证（允许脱机）并为其他人传送非永久许可证（禁用脱机）？ 是否必须复制内容并使用单独的内容密钥？

由于媒体服务 v3 允许资产具有多个 StreamingLocator。 你可以有

* 一个带有 license_type =“持久性”的 ContentKeyPolicy，带有声明“持久性”的 ContentKeyPolicyRestriction 及其 StreamingLocator；
* 另一个带有 license_type=“非持久性”的 ContentKeyPolicy，带有声明“非持久性”的 ContentKeyPolicyRestriction 及其 StreamingLocator。
* 两个 StreamingLocator 具有不同的 ContentKey。

根据自定义 STS 的业务逻辑，在 JWT 令牌中发出不同的声明。 使用该令牌，只能获得相应的许可证，并且只能播放相应的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和媒体服务 DRM 安全级别之间的映射是什么？

谷歌的"威文DRM架构概述"定义了三个不同的安全级别。 但是，在 [Widevine 许可证模板上的 Azure 媒体服务文档](widevine-license-template-overview.md)中，概述了五种不同的安全级别。 本节介绍安全级别如何映射。

Google 的"威文 DRM 架构审查"文档定义了以下三个安全级别：

* 安全级别 1：所有内容处理、加密和控制操作均在受信任的执行环境 (TEE) 中进行。 在某些实现模型中，可在不同的芯片中执行安全处理。
* 安全级别 2：在 TEE 中执行加密（但不是视频处理）：已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但是，在级别 2，加密信息仍仅在 TEE 中处理。
* 安全级别 3：设备上没有 TEE。 可采取相应措施来保护主机操作系统中的加密信息和已解密内容。 级别 3 实现还包括硬件加密引擎，但只能增强性能，而不能增强安全性。

同时，在 [Widevine 许可证模板上的 Azure 媒体服务文档](widevine-license-template-overview.md)中，content_key_specs 的 security_level 属性可以具有以下五个不同的值（播放的客户端稳定性要求）：

* 需要基于软件的白盒加密。
* 需要软件加密和模糊处理解码器。
* 密钥材料和加密操作必须在硬件支持的 TEE 中执行。
* 内容加密和解码必须在硬件支持的 TEE 中执行。
* 加密、解码与媒体（压缩和未压缩）的所有处理必须在硬件支持的 TEE 中处理。

这两种安全级别均由 Google Widevine 定义。 不同之处是其使用情况级别：体系结构级别或 API 级别。 Widevine API 中使用了这五种安全级别。 ontent_key_specs 对象包含 security_level，该对象被反序列化，并通过 Azure 媒体服务 Widevine 许可证服务传递给 Widevine 全球传送服务。 下表显示两组安全级别之间的映射。

| **Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1：** 所有内容处理、加密和控制都在受信任的执行环境 （TEE） 中执行。 在某些实现模型中，可在不同的芯片中执行安全处理。|**security_level=5**：加密、解码与媒体（压缩和未压缩）的所有处理必须在硬件支持的 TEE 中处理。<br/><br/>**security_level=4**：内容加密和解码必须在硬件支持的 TEE 中执行。|
**安全级别 2**：在 TEE 中执行加密（但不是视频处理）：已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但是，在级别 2，加密信息仍仅在 TEE 中处理。| **security_level=3**：密钥材料和加密操作必须在硬件支持的 TEE 中执行。 |
| **安全级别 3**：设备上没有 TEE。 可采取相应措施来保护主机操作系统中的加密信息和已解密内容。 级别 3 实现还包括硬件加密引擎，但只能增强性能，而不能增强安全性。 | **security_level=2**：软件加密和模糊解码器是必需的。<br/><br/>**security_level=1**：需要基于软件的白盒加密。|

#### <a name="why-does-content-download-take-so-long"></a>为什么下载内容需要很长时间？

可通过两种方法提高下载速度：

* 启用 CDN，使最终用户更容易命中内容下载的 CDN，而不是源/流式处理终结点。 如果用户命中流式处理终结点，会动态打包和加密每个 HLS 段或 DASH 片段。 即使每个段/片段的延迟时间都只有几毫秒，如果视频时间长达一个小时，累积延迟可能会很长，从而导致下载时间变长。
* 让最终用户能够选择性地下载视频质量层和音轨，而不是所有内容。 对于脱机模式，无需下载所有的质量层。 可通过两种方式实现此目的：

   * 客户端控制：播放器应用自动选择要下载的视频质量层和音轨，或由用户选择；
   * 服务控制：可使用 Azure 媒体服务中的动态清单功能创建（全局）筛选器，将 HLS 播放列表或 DASH MPD 限制为单个视频质量层和所选音轨。 然后，向最终用户呈现的下载 URL 会包括此筛选器。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
