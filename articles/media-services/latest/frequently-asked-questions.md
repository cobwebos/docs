---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文提供了有关 Azure 媒体服务 v3 的常见问题的解答。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309190"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒体服务 v3 常见问题解答

本文提供了有关 Azure 媒体服务 v3 的常见问题的解答。

## <a name="general"></a>常规

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure 角色可对 Azure 媒体服务资源执行哪些操作？ 

请参阅[媒体服务帐户的基于角色的访问控制 (RBAC)](rbac-overview.md)。

### <a name="how-do-i-stream-to-apple-ios-devices"></a>向 Apple iOS 设备如何实现流？

请确保路径的末尾（**格式 = m3u8-aapl-v3-流式处理 m3u8-aapl-v3）** 位于路径的末尾（在 URL 的 **/manifest**部分之后），以告知流式处理源服务器返回 HTTP Live Streaming （HLS）内容，以便在 Apple iOS 本机设备上使用。 有关详细信息，请参阅[传送内容](dynamic-packaging-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体预留单位？

对于媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，我们建议预配 10 S3 媒体保留单位（Mru）的帐户。 如果需要超过10个 S3 Mru，请使用[Azure 门户](https://portal.azure.com/)来打开支持票证。

有关详细信息，请参阅[缩放媒体处理](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个转换描述了用于处理视频或音频文件的脚本或任务工作流。 [作业](https://docs.microsoft.com/rest/api/media/jobs)是媒体服务向输入视频或音频内容应用转换的实际请求。 创建转换后，可以通过使用媒体服务 Api 或任何已发布的 Sdk 来提交作业。 有关详细信息，请参阅[转换和作业](transforms-jobs-concept.md)。

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>我已经上传、编码并发布了视频。 为什么当我尝试对视频进行流式处理时，视频无法播放？

最常见的原因之一是，你没有尝试播放的流式处理终结点正在运行状态。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

使用分页时，应始终使用 "下一步" 链接来枚举集合，而不是依赖于特定页面大小。 有关详细信息和示例，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>目前有哪些功能在 Azure 媒体服务 v3 中不可用？

有关详细信息，请参阅[针对 V2 api 的功能差距](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>如何在订阅之间移动媒体服务帐户？  

有关详细信息，请参阅[在订阅之间移动媒体服务帐户](media-services-account-concept.md)。

## <a name="live-streaming"></a>实时传送视频流 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>广播完成后如何实现停止实时流？

你可以从客户端或服务器端对其进行处理。

#### <a name="client-side"></a>客户端

如果 web 应用程序要在关闭浏览器时结束广播，则应该提示用户。 这是你的 web 应用程序可以处理的浏览器事件。

#### <a name="server-side"></a>服务器端

可以通过订阅 Azure 事件网格事件来监视实时事件。 有关详细信息，请参阅[EventGrid 事件架构](media-services-event-schemas.md#live-event-types)。

可以：

* [订阅](reacting-to-media-services-events.md)流级别的[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected)事件，并监视是否有一段时间没有重新传输进入，停止并删除实时事件。
* [订阅](reacting-to-media-services-events.md)跟踪级[检测信号](media-services-event-schemas.md#liveeventingestheartbeat)事件。 如果所有磁道的传入比特率下降到0，或者最后时间戳不再增加，则可以安全地关闭实时事件。 每个轨迹每隔20秒出现一次检测信号事件，因此可能有点冗长。

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>实时流期间如何实现插入中断/视频和图像清单？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用程序都提供了切换源的能力，包括 Telestream Wirecast、切换器（在 iOS 上）和 OBS Studio （免费应用）。

## <a name="content-protection"></a>内容保护

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>我应该使用 AES-128 明文密钥加密还是 DRM 系统？

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两个系统之间的主要区别在于，通过 AES 加密，内容密钥通过 TLS 传输到客户端，因此密钥在传输过程中已加密，但没有任何其他加密（"明文"）。 因此，客户端播放机可以访问用于解密内容的密钥，并且可以在客户端上以纯文本形式在网络跟踪中查看。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，员工查看公司内分发的加密公司视频）。

DRM 系统（如 PlayReady、Widevine 和 FairPlay）在用于解密内容的密钥上提供额外的加密级别，与 AES-128 明文密钥相比。 内容密钥加密为受 DRM 运行时保护的密钥，以及 TLS 提供的任何传输级加密。 此外，解密是操作系统级别在安全的环境中处理的，在这样的环境中，恶意用户更难进行攻击。 建议将 DRM 用于查看者可能不是受信任方，并且需要最高级别的安全。

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>如何实现仅向具有特定权限的用户显示视频，而不使用 Azure AD？

无需使用任何特定的令牌提供程序，如 Azure Active Directory （Azure AD）。 可以通过使用非对称密钥加密来创建自己的[JWT](https://jwt.io/)提供程序（称为安全令牌服务或 STS）。 在自定义 STS 中，可以根据业务逻辑添加声明。

请确保颁发者、受众和声明完全匹配 JWT 的内容与中`ContentKeyPolicyRestriction` `ContentKeyPolicy`使用的值之间的差异。

有关详细信息，请参阅[使用媒体服务动态加密保护内容](content-protection-overview.md)。

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>使用请求许可证或密钥之前，如何以及在何处获取 JWT 令牌？

对于生产环境，需要具有安全令牌服务（即 web 服务），该服务会在 HTTPS 请求时发出 JWT 令牌。 对于测试，可以使用`GetTokenAsync` [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)中定义的方法中所示的代码。

在对用户进行身份验证后，播放机发出请求，以 STS 此类令牌并将其分配为令牌的值。 可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)。

有关使用对称密钥或非对称密钥运行 STS 的示例，请参阅[JWT 工具](https://aka.ms/jwt)。 有关使用此类 JWT 令牌的 Azure Media Player 播放机的示例，请参阅[Azure 媒体测试工具](https://aka.ms/amtest)。 （展开**player_settings**链接可查看令牌输入。）

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何实现授权通过 AES 加密流式传输视频的请求？

正确的方法是使用安全令牌服务。 在 STS 中，根据用户配置文件添加不同的声明（例如 "高级用户"、"基本用户"、"免费试用用户"）。 在 JWT 中添加不同的声明后，用户可以查看不同的内容。 对于不同的内容或资产`ContentKeyPolicyRestriction` ，将具有相应`RequiredClaims`的值。

使用 Azure 媒体服务 Api 配置许可证/密钥传递和加密资产（如[本示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)中所示）。

有关详细信息，请参见:

- [内容保护概述](content-protection-overview.md)
- [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>我应该使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 但是，HTTP 对于流式处理是首选的，因此需要考虑混合内容的这些问题：

* 浏览器不允许混合内容。 但像 Silverlight 这样的插件和 OSMF 插件的平滑和破折号允许它。 混合内容是一种安全问题，因为能够注入恶意 JavaScript，这可能会使客户数据处于风险之中。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 播放混合内容时，SilverlightSS 技术需要清除混合内容警告。 FlashSS 技术处理混合内容时，不会出现混合内容警告。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

### <a name="what-about-live-streaming"></a>如何使用实时流？

您可以使用完全相同的设计和实现方式，通过将与程序关联的资产视为 VOD 资产，来帮助保护媒体服务中的实时流。 若要提供实时内容的多 DRM 保护，请在将资产与实时输出关联之前，对资产应用相同的设置/处理方式，就像它是 VOD 资产一样。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？

通常，客户在其自己的数据中心或由 DRM 服务提供商托管的数据中心中投资了许可证服务器场。 使用媒体服务内容保护，可以在混合模式下操作。 内容可以在媒体服务中托管和动态保护，而 DRM 许可证由媒体服务外部的服务器传送。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 需要有 STS 才能颁发此类 JWT。 
* 不再需要在媒体服务中配置许可证传送服务。 在配置`ContentKeyPolicy`时，需要提供许可证获取 url （适用于 PlayReady、Widevine 和 FairPlay）。

> [!NOTE]
> Widevine 是 Google 提供的一项服务，受 Google 的服务条款和隐私策略约束。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

目前，可以使用[Azure 门户](https://portal.azure.com/)执行以下操作：

* 管理媒体服务 v3 中的[实时事件](live-events-outputs-concept.md)。 
* 查看（不管理） v3[资产](assets-concept.md)。 
* [获取有关访问 api 的信息](access-api-portal.md)。 

对于所有其他管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用[REST API](https://docs.microsoft.com/rest/api/media/)、 [Azure CLI](https://aka.ms/ams-v3-cli-ref)或其中一个受支持的[sdk](media-services-apis-overview.md#sdks)。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

从`AssetFile`媒体服务 API 中移除了从存储 SDK 依赖项分离媒体服务的概念。 现在，Azure 存储（而不是媒体服务）会保留存储 SDK 中的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](media-services-v2-vs-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

我们现在建议使用服务器端存储加密（默认为启用）。 有关详细信息，请参阅[Azure 存储服务加密静态数据](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="offline-streaming"></a>脱机流式处理

### <a name="fairplay-streaming-for-ios"></a>适用于 iOS 的 FairPlay 流式处理

以下常见问题可帮助排查适用于 iOS 的脱机 FairPlay 流式处理问题。

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>为什么在脱机模式期间只播放音频而不播放视频？

此行为似乎是示例应用专门设计的。 在脱机模式下出现备用音频轨（这是 HLS）时，iOS 10 和 iOS 11 默认为备用音频轨迹。若要补偿 FPS 脱机模式的此行为，请从流中删除备用音频轨。 若要在媒体服务中执行此操作，请添加动态清单筛选器**仅音频 = false**。 换句话说，HLS URL 以 **. ism/manifest 结尾（格式 = m3u8-aapl-v3-流式处理 m3u8-aapl-v3，仅音频 = false）**。 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？

根据内容交付网络的缓存密钥设计，可能会缓存内容。 请清除缓存。

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Ios 11 上是否支持 FPS 脱机模式以及 iOS 10？

是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？

从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 设备上的已下载/脱机文件结构是什么？

iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 

名称以破折号结尾的第一个文件夹包含视频内容。 该数值是视频呈现形式的最大带宽。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 名为`Data`的第三个文件夹包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![FairPlay iOS 示例应用程序的脱机文件结构](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

下面是一个示例 boot .xml 文件：

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>如何为其他客户端/用户和非永久许可证（脱机禁用）提供永久性许可证（已启用脱机）？ 是否需要复制内容并使用单独的内容密钥？

由于媒体服务 v3 允许资产具有多个`StreamingLocator`实例，因此您可以：

* 一个`ContentKeyPolicy`实例`license_type = "persistent"` `ContentKeyPolicyRestriction` ，其中包含和的`"persistent"`声明`StreamingLocator`。
* 具有`ContentKeyPolicy` `license_type="nonpersistent"`的另一个`ContentKeyPolicyRestriction`实例，其`"nonpersistent` `StreamingLocator`声明为 "and"。
* 具有`StreamingLocator`不同`ContentKey`值的两个实例。

根据自定义 STS 的业务逻辑，在 JWT 令牌中发出不同的声明。 使用该令牌，只能获得相应的许可证，并且只能播放相应的 URL。

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine 和 Media Services DRM 安全级别之间的映射是什么？

Google 的 "Widevine DRM 体系结构概述" 定义了三个安全级别。 但是， [Widevine 许可证模板上的 Azure 媒体服务文档概述了](widevine-license-template-overview.md)五个安全级别（播放的客户端稳定性要求）。 本部分介绍如何映射安全级别。

两组安全级别都是由 Google Widevine 定义的。 不同之处在于使用级别：体系结构或 API。 Widevine API 中使用了这五种安全级别。 `content_key_specs`对象（包含`security_level`）通过 Azure 媒体服务 Widevine 许可证服务进行反序列化并传递到 Widevine global 传递服务。 下表显示了两组安全级别之间的映射。

| **在 Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1**：所有内容处理、加密和控制均在受信任的执行环境（t）内执行。 在某些实现模型中，可能会在不同的芯片中执行安全处理。|**security_level = 5**：必须在硬件支持的 t-sql 内处理对媒体（压缩和未压缩）的加密、解码和所有处理。<br/><br/>**security_level = 4**：内容的加密和解码必须在硬件支持的中进行。|
**安全级别 2**：在中执行加密（但不是视频处理）。 已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但在第2级，只能在该 t 内处理加密信息。| **security_level = 3**：密钥材料和加密操作必须在硬件支持的中执行。 |
| **安全级别 3**：设备上没有。 可以采取适当的措施来保护主机操作系统上的加密信息和解密内容。 第3级实现也可能包含硬件加密引擎，但这只会提高性能，而不能增强安全性。 | **security_level = 2**：需要软件加密和模糊处理解码器。<br/><br/>**security_level = 1**：需要基于软件的白框加密。|

#### <a name="why-does-content-download-take-so-long"></a>为什么下载内容需要很长时间？

可通过两种方法提高下载速度：

* 启用内容交付网络，以便用户更有可能受到点击，而不是使用源/流式处理终结点来下载内容。 如果用户点击流式处理终结点，则会动态打包和加密每个 HLS 段或虚线片段。 即使对于每个段或片段，此延迟时间都为毫秒，但当你有一个小时长的视频时，累计延迟可能会很大，并会导致下载较长的时间。
* 让用户有选择地下载视频质量层和音频轨道，而不是所有内容。 对于脱机模式，下载所有质量层并无任何需要。 可通过两种方式实现此目的：

  * 由客户端控制：播放机应用自动选择或用户选择、视频质量层和要下载的音频轨道。
  * 服务控制：可以使用 Azure 媒体服务中的动态清单功能创建（全局）筛选器，它将 HLS 播放列表或短划线 MPD 限制为单个视频质量层和选定的音频轨迹。 然后，呈现给用户的下载 URL 将包含此筛选器。

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
