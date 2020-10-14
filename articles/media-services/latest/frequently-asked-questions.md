---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文解答有关 Azure 媒体服务 v3 的常见问题。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: aa33319f146ac57ab4d253696c94308a43b5db2e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019607"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒体服务 v3 常见问题解答

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文解答有关 Azure 媒体服务 v3 的常见问题。

## <a name="general"></a>常规

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Azure 门户针对媒体服务 v3 有哪些限制？

可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：管理 v3 实时事件、查看 v3 资产和作业、获取有关访问 API 的信息以及加密内容。 <br/>对于所有其他管理任务 (例如，管理转换和作业或分析 v3 内容) ，请使用 [REST API](/rest/api/media/accountfilters)、 [CLI](/cli/azure/ams)或其中一种受支持的 [sdk](media-services-apis-overview.md#sdks)。

如果视频以前是使用媒体服务 v3 API 上传到媒体服务帐户的，或者该内容是基于实时输出生成的，则 Azure 门户中不会显示“编码”、“分析”或“加密”按钮  。 使用媒体服务 v3 API 来执行这些任务。  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>如何流式传输到 Apple iOS 设备？

确保在路径的末尾（在 URL 的“/manifest”部分之后）有“(format=m3u8-aapl)”，目的是告知流式处理源服务器返回 HTTP Live Streaming (HLS) 内容，以便在 Apple iOS 本机设备上使用 。 有关详细信息，请参阅[传送内容](dynamic-packaging-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体预留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，建议为你的帐户预配 10 个 S3 媒体预留单位 (MRU)。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)创建支持票证。

有关详细信息，请参阅[调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个转换描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](/rest/api/media/jobs)是针对媒体服务的实际请求，目的是将转换应用到输入视频或音频内容。 创建转换后，可以使用媒体服务 API 或任何已发布的 SDK 来提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已经上传、编码并发布了视频。 为什么在我尝试对视频进行流式处理时，它不播放？

最常见的原因之一是，你播放时使用的流式处理终结点未处于“正在运行”状态。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用下一链接来枚举集合，而不依赖特定的页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[与 v2 API 之间的功能差距](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](media-services-account-concept.md)。

## <a name="live-streaming"></a>实时传送视频流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>广播完成后如何停止实时传送流？

你可以从客户端或服务器端来实现。

#### <a name="client-side"></a>客户端

当用户关闭浏览器时，Web 应用程序应该提示用户是否要结束广播。 这是 Web 应用程序可以处理的浏览器事件。

#### <a name="server-side"></a>服务器端

可通过订阅 Azure 事件网格事件来监视实时事件。 有关详细信息，请参阅 [EventGrid 事件架构](media-services-event-schemas.md#live-event-types)。

可以：

* [订阅](reacting-to-media-services-events.md)流式传输级别 [Microsoft.Media.LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) 事件，并监视一段时间内没有重新连接来停止和删除实时事件。
* [订阅](reacting-to-media-services-events.md)跟踪级别[检测信号](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有跟踪的传入比特率下降到 0，或者最后时间戳不再增大，则可以安全地关闭实时事件。 每个跟踪每隔 20 秒出现一次检测信号事件，因此可能有点冗长。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>如何在实时传送流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）和 OBS Studio（免费应用））的功能。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>应使用 AES-128 明文密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两个系统之间的主要差别在于，使用 AES 加密时，内容密钥将通过 TLS 传输到客户端，这样，密钥将经过传输中加密，但不会经过任何进一步的加密（“明文加密”）。 因此，用于解密内容的密钥可由客户端播放器访问，并且可以在客户端上的网络跟踪中以纯文本形式显示。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，加密员工观看的在公司内部分发的公司视频）。

DRM 系统（如 PlayReady、Widevine 和 FairPlay）在用于解密内容的密钥上提供额外的加密级别，与 AES-128 明文密钥相比。 内容密钥将会加密成受 DRM 运行时保护的密钥，此外还会进行 TLS 提供的任何传输级加密。 此外，解密是在安全的环境中在操作系统级别处理的，在这样的环境中，恶意用户更难进行攻击。 在观看者可能不是受信任方且需要更高等级的安全性的用例中，建议使用 DRM。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何在不使用 Azure AD 的情况下仅向具有特定权限的用户显示视频？

无需使用任何特定的令牌提供程序，例如 Azure Active Directory (Azure AD)。 可以使用非对称密钥加密创建自己的 [JWT](https://jwt.io/) 提供程序（所谓的“安全令牌服务”，简称 STS）。 在自定义 STS 中，可以根据业务逻辑添加声明。

确保颁发者、受众和声明在 JWT 中的内容和 `ContentKeyPolicy` 中使用的 `ContentKeyPolicyRestriction` 值之间完全匹配。

有关详细信息，请参阅[使用媒体服务动态加密保护内容](content-protection-overview.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>在使用 JWT 令牌请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

在生产环境中，需要获取安全令牌服务（一个 Web 服务），以便根据 HTTPS 请求颁发 JWT 令牌。 对于测试，可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 定义的 `GetTokenAsync` 方法中所示的代码。

对用户进行身份验证后，播放器会向 STS 发出请求以获取此类令牌，并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

有关使用对称密钥或非对称密钥运行 STS 的示例，请参阅 [JWT 工具](https://aka.ms/jwt)。 有关使用此类 JWT 令牌的基于 Azure Media Player 的播放器示例，请参阅 [Azure 媒体测试工具](https://aka.ms/amtest)。 （展开“player_settings”链接可查看令牌输入。）

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授权使用 AES 加密流式传输视频的请求？

正确的方法是使用安全令牌服务。 在 STS 中，根据用户配置文件添加不同的声明（例如“高级用户”、“基本用户”、“免费试用版用户”）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 对于不同的内容或资产，`ContentKeyPolicyRestriction` 会包含相应的 `RequiredClaims` 值。

使用 Azure 媒体服务 API 来配置许可证/传送密钥以及加密资产（如[此示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)中所示）。

有关详细信息，请参阅：

- [内容保护概述](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>应该使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 不过，最好是使用 HTTP 进行流式传输，因此需要考虑这些混合内容问题：

* 浏览器不允许混合内容。 但是 Silverlight 等插件和适用于平滑流与 DASH 的 OSMF 插件允许混合内容。 混合内容是一个安全隐患，因为存在插入恶意 JavaScript 的威胁，使客户数据处于风险之中。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 播放混合内容时，SilverlightSS 技术需要清除混合内容警告。 FlashSS 技术在没有混合内容警告的情况下处理混合内容。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

### <a name="what-about-live-streaming"></a>如何使用实时流？

可以使用完全相同的设计和实现来帮助保护媒体服务中的实时传送视频流，方法是将与节目关联的资产视为 VOD 资产。 若要为实时内容提供多重 DRM 保护，请在将资产关联到实时输出之前，将相同的设置/处理应用到资产，就如同它是 VOD 资产一样。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？

通常，客户可能已在自己的数据中心或由 DRM 服务提供商托管的位置投资了许可证服务器场。 使用媒体服务内容保护，可以在混合模式下操作。 可以在媒体服务中托管和动态保护内容，而 DRM 许可证由 Azure 媒体服务外部的服务器传送。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 需要有 STS 才能颁发此类 JWT。 
* 不再需要在媒体服务中配置许可证传送服务。 你需要在配置时为 PlayReady、Widevine 和 FairPlay) 提供许可证获取 Url (`ContentKeyPolicy` 。

> [!NOTE]
> Widevine 是 Google 提供的一项服务，受 Google 的服务条款和隐私策略约束。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：

* 在媒体服务 v3 中管理[实时事件](live-events-outputs-concept.md)。 
* 查看（而不是管理）v3 [资产](assets-concept.md)。 
* [获取有关访问 API 的信息](./access-api-howto.md)。 

对于其他所有管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用 [REST API](/rest/api/media/)、[Azure CLI](/cli/azure/ams) 或某个受支持的 [SDK](media-services-apis-overview.md#sdks)。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

已从媒体服务 API 中删除了 `AssetFile` 概念，以便将媒体服务与存储 SDK 依赖项分开。 现在由 Azure 存储而非媒体服务来保存属于存储 SDK 的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在建议使用服务器端存储加密（在默认情况下为打开状态）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](../../storage/common/storage-service-encryption.md)。

## <a name="offline-streaming"></a>脱机流式处理

### <a name="fairplay-streaming-for-ios"></a>适用于 iOS 的 FairPlay 流式处理

以下常见问题解答可帮助你排查适用于 iOS 的脱机 FairPlay 流式处理的问题。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>为什么在脱机模式期间只播放音频而不播放视频？

此行为似乎是示例应用专门设计的。 存在备用音频曲目时（这适用于 HLS），在脱机模式期间，iOS 10 和 iOS 11 都默认播放备用音频曲目。为了补偿 FPS 脱机模式的此行为，需要从流删除备用音频曲目。 若要在媒体服务中完成此操作，请添加动态清单筛选器 audio-only=false。 换言之，HLS URL 将以 .ism/manifest(format=m3u8-aapl,audio-only=false) 结尾。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？

根据内容分发网络的缓存键设计，可能会缓存该内容。 请清除缓存。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>除 iOS 10 之外，iOS 11 是否也支持 FPS 脱机模式？

是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？

从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 设备上的已下载/脱机文件结构是什么？

iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 

第一个文件夹（文件名以破折号加数字结尾）包含视频内容。 数值是峰值带宽视频呈现形式。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹（文件名为 `Data`）包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![FairPlay iOS 示例应用的脱机文件结构](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

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

### <a name="widevine-streaming-for-android"></a>适用于 Android 的 Widevine 流式处理

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何为某些客户端/用户和非持久性许可证 (脱机禁用) 脱机的)  (提供永久性许可证？ 是否需要复制内容并使用单独的内容密钥？

由于媒体服务 v3 允许资产具有多个 `StreamingLocator` 实例，因此您可以：

* 一个实例，其中包含 `ContentKeyPolicy` `license_type = "persistent"` 和的 `ContentKeyPolicyRestriction` 声明 `"persistent"` `StreamingLocator` 。
* 具有的另一个 `ContentKeyPolicy` 实例， `license_type="nonpersistent"` `ContentKeyPolicyRestriction` 其声明为 `"nonpersistent` "and" `StreamingLocator` 。
* `StreamingLocator`具有不同值的两个实例 `ContentKey` 。

根据自定义 STS 的业务逻辑，在 JWT 令牌中发出不同的声明。 使用该令牌，只能获得相应的许可证，并且只能播放相应的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和 Media Services DRM 安全级别之间的映射是什么？

Google 的 "Widevine DRM 体系结构概述" 定义了三个安全级别。 但是， [Widevine 许可证模板上的 Azure 媒体服务文档概述了](widevine-license-template-overview.md) 五个安全级别 (客户端可靠性要求) 。 本部分介绍如何映射安全级别。

两组安全级别都是由 Google Widevine 定义的。 不同之处在于使用级别：体系结构或 API。 Widevine API 中使用了这五种安全级别。 `content_key_specs`对象（包含 `security_level` ）通过 Azure 媒体服务 Widevine 许可证服务进行反序列化并传递到 Widevine global 传递服务。 下表显示了两组安全级别之间的映射。

| **在 Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1**：所有内容处理、加密和控制均在受信任的执行环境中执行， (t) 。 在某些实现模型中，可能会在不同的芯片中执行安全处理。|**security_level = 5**：必须在硬件支持的程序集中处理对媒体 (压缩和未压缩的) 的加密、解码和所有处理。<br/><br/>**security_level = 4**：内容的加密和解码必须在硬件支持的中进行。|
**安全级别 2**：加密 (但不是在中执行的视频处理) 。 已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但在第2级，只能在该 t 内处理加密信息。| **security_level = 3**：密钥材料和加密操作必须在硬件支持的中执行。 |
| **安全级别 3**：设备上没有。 可以采取适当的措施来保护主机操作系统上的加密信息和解密内容。 第3级实现也可能包含硬件加密引擎，但这只会提高性能，而不能增强安全性。 | **security_level = 2**：需要软件加密和模糊处理解码器。<br/><br/>**security_level = 1**：需要基于软件的白框加密。|

#### <a name="why-does-content-download-take-so-long"></a>为什么下载内容需要很长时间？

可通过两种方法提高下载速度：

* 启用内容交付网络，以便用户更有可能受到点击，而不是使用源/流式处理终结点来下载内容。 如果用户点击流式处理终结点，则会动态打包和加密每个 HLS 段或虚线片段。 即使对于每个段或片段，此延迟时间都为毫秒，但当你有一个小时长的视频时，累计延迟可能会很大，并会导致下载较长的时间。
* 让用户有选择地下载视频质量层和音频轨道，而不是所有内容。 对于脱机模式，下载所有质量层并无任何需要。 可通过两种方式实现此目的：

  * 由客户端控制：播放机应用自动选择或用户选择、视频质量层和要下载的音频轨道。
  * 服务控制：可以使用 Azure 媒体服务中的动态清单功能创建 (全局) 筛选器，它将 HLS 播放列表或短划线 MPD 限制为单个视频质量层和选定的音频轨迹。 然后，呈现给用户的下载 URL 将包含此筛选器。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)