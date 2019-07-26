---
title: 使用媒体服务动态加密来保护内容-Azure |Microsoft Docs
description: 本文概述了使用动态加密的内容保护。 它还讨论流式处理协议和加密类型。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 174184993e40b60dc89022d360f0c09fb31bc60b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501270"
---
# <a name="protect-your-content-by-using-media-services-dynamic-encryption"></a>使用媒体服务动态加密来保护内容

你可以使用 Azure 媒体服务来帮助保护你的媒体, 从离开计算机到存储、处理和传送的整个过程。 借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。  

在媒体服务 v3 中, 内容密钥与流式处理定位符相关联 (请参阅[此示例](protect-with-aes128.md))。 如果使用媒体服务密钥传送服务, 可以让 Azure 媒体服务生成内容密钥。 如果你使用的是自己的密钥传送服务, 或者如果你需要处理需要在两个数据中心具有相同内容密钥的高可用性方案, 则应自行生成内容密钥。

播放器请求流时，媒体服务将通过 AES 明文密钥或 DRM 加密使用指定的密钥来动态加密内容。 为了解密流，播放器将从媒体服务密钥传送服务或者指定的密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

可以使用 REST API 或媒体服务客户端库来配置许可证和密钥的授权与身份验证策略。

下图说明了媒体服务内容保护的工作流:

![媒体服务内容保护工作流](./media/content-protection/content-protection.svg)
  
&#42;*动态加密支持 AES-128 明文密钥、cbc 和 CENC。有关详细信息, 请参阅[支持矩阵](#streaming-protocols-and-encryption-types)。*

本文介绍可帮助你了解媒体服务的内容保护的概念和术语。

## <a name="main-components-of-a-content-protection-system"></a>内容保护系统的主要组件

若要成功完成内容保护系统, 需要充分了解工作量范围。 以下部分概述了需要实现的三个部件。 

> [!NOTE]
> 在转到下一部分之前, 强烈建议你重点关注并全面测试以下各部分中的每个部分。 若要测试你的内容保护系统, 请使用部分中指定的工具。

### <a name="media-services-code"></a>媒体服务代码
  
[DRM 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)演示如何使用 .Net 通过媒体服务 v3 实现多 DRM 系统。 它还演示了如何使用媒体服务许可证/密钥传送服务。   
  
可以使用多个加密类型（AES-128、PlayReady、Widevine、FairPlay）来加密每个资产。 若要查看合并有何意义, 请参阅[流式处理协议和加密类型](#streaming-protocols-and-encryption-types)。

该示例演示如何：

1. 创建和配置[内容密钥策略](content-key-policy-concept.md)。    

   你可以创建内容密钥策略来配置内容密钥 (提供对资产的安全访问) 如何传递到最终客户端:  
 
   * 定义许可证传送授权。 基于 JSON Web 令牌 (JWT) 中的声明指定授权检查的逻辑。
   * 配置[PlayReady](playready-license-template-overview.md)、 [Widevine](widevine-license-template-overview.md)和/或[FairPlay](fairplay-license-overview.md)许可证。 模板使你可以为每个 Drm 配置权限和权限。

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```
2. 创建一个已配置为流式传输加密资产的[流式处理定位符](streaming-locators-concept.md)。 
  
   流式处理定位符必须与[流式处理策略](streaming-policy-concept.md)相关联。 在此示例中, 我们`StreamingLocator.StreamingPolicyName`将设置为 "Predefined_MultiDrmCencStreaming" 策略。 
      
   应用 PlayReady 和 Widevine 加密, 并根据配置的 DRM 许可证将密钥传递到播放客户端。 如果还想要使用 CBC (FairPlay) 加密流, 请使用 "Predefined_MultiDrmStreaming" 策略。

   流式处理定位符还与您定义的内容密钥策略相关联。
3. 创建测试令牌。

   `GetTokenAsync`方法演示如何创建测试令牌。
4. 生成流 URL。

   `GetDASHStreamingUrlAsync`方法演示如何生成流 URL。 在这种情况下, 该 URL 将流式传输短划线内容。

### <a name="player-with-an-aes-or-drm-client"></a>使用 AES 或 DRM 客户端的播放器 

基于播放器 SDK 的视频播放器应用（本机或基于浏览器）需要满足以下要求：

* 播放机 SDK 支持所需的 DRM 客户端。
* 播放器 SDK 支持所需的流式处理协议：光滑、短线和/或 HLS。
* 播放机 SDK 可以处理许可证获取请求中的 JWT 令牌。

可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 指定要在不同的 DRM 平台上使用哪种 DRM 技术。

若要测试 AES 或 CENC (Widevine 和/或 PlayReady) 加密内容，可以使用 [Azure Media Player](https://aka.ms/azuremediaplayer)。 请确保选择 "**高级选项**" 并检查加密选项。

若要测试 FairPlay 加密内容，请使用[此测试播放器](https://aka.ms/amtest)。 播放机支持 Widevine、PlayReady 和 FairPlay Drm, 以及 AES-128 明文密钥加密。 

选择正确的浏览器来测试不同的 Drm:

* Widevine 的 Chrome、Opera 或 Firefox
* 用于 PlayReady 的 Microsoft Edge 或 Internet Explorer 11
* 针对 FairPlay 的 macOS 上的 Safari

### <a name="security-token-service"></a>安全令牌服务

Security token service (STS) 发出 JWT 作为访问令牌, 以进行后端资源访问。 可以使用 Azure 媒体服务许可证/密钥传送服务作为后端资源。 STS 必须定义以下信息：

* 颁发者和受众（或范围）
* 与内容保护中的业务要求相关的声明
* 签名的对称或非对称验证
* 密钥滚动更新支持（如有必要）

您可以使用[此 sts 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)来测试 STS。 它支持以下三种类型的验证密钥: 对称、不对称或 Azure Active Directory (Azure AD) 以及密钥滚动更新。 

## <a name="streaming-protocols-and-encryption-types"></a>流式处理协议和加密类型

可以通过 Azure 媒体服务传送使用 AES 明文密钥或 DRM 加密（利用 PlayReady、Widevine 或 FairPlay）动态加密的内容。 当前可以加密 HTTP Live Streaming (HLS)、MPEG DASH 和平滑流式处理格式。 每个协议都支持以下加密方法。

### <a name="hls"></a>HLS

HLS 协议支持以下容器格式和加密方案。

|容器格式|加密方案|URL 示例|
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBC (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBC (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

以下设备支持 HLS/CMAF + FairPlay (包括 HEVC/.H):

* iOS 11 或更高版本 
* iPhone 8 或更高版本
* Intel 7 代 CPU 的 MacOS 高塞拉利昂

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-短划线协议支持以下容器格式和加密方案。

|容器格式|加密方案|URL 示例
|---|---|---|
|全部|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>平滑流式处理

平滑流式处理协议支持以下容器格式和加密方案。

|Protocol|容器格式|加密方案|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>浏览器

常见浏览器支持以下 DRM 客户端:

|浏览者|加密|
|---|---|
|Chrome|Widevine|
|Microsoft Edge、Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>控制内容访问

可以通过配置内容密钥策略来控制谁有权访问内容。 媒体服务支持通过多种方式对发出密钥请求的用户进行授权。 必须配置内容密钥策略。 客户端（播放器）必须符合该策略才能将密钥传送到客户端。 内容密钥策略可以采用*开放*或*令牌*限制。 

如果要向没有授权的任何人颁发许可证, 则可以使用开放限制的内容密钥策略。 例如, 如果你的收入基于 ad 而不是基于订阅。  

使用令牌限制内容密钥策略时, 内容密钥仅发送到在许可证/密钥请求中提供有效 JWT 令牌或简单 web 令牌的客户端。 此令牌必须由 STS 颁发。 

你可以使用 Azure AD 作为 STS 或部署自定义 STS。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果满足以下两个条件, 媒体服务许可证/密钥传送服务会将请求的许可证或密钥返回给客户端:

* 标记有效。 
* 令牌中的声明与为许可证或密钥配置的声明相匹配。

配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的 STS。 受众 (有时称为范围) 描述令牌或令牌授权访问的资源的意图。 媒体服务许可证/密钥传送服务将验证令牌中的这些值是否与模板中的值匹配。

### <a name="token-replay-prevention"></a>令牌重播防护

使用*令牌重播防护*功能, 媒体服务客户可以设置一个限制, 该限制可用于请求密钥或许可证。 客户可以在令牌中添加类型`urn:microsoft:azure:mediaservices:maxuses`的声明, 其中值是令牌可用于获取许可证或密钥的次数。 对密钥传递具有相同令牌的所有后续请求将返回未经授权的响应。 请参阅如何在[DRM 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)中添加声明。
 
#### <a name="considerations"></a>注意事项

* 客户必须能够控制令牌生成。 声明需要置于令牌本身中。
* 使用此功能时, 将在收到请求时, 使用过期时间超过一小时的令牌请求, 但会拒绝未经授权的响应。
* 令牌由其签名唯一标识。 对有效负载的任何更改 (例如, 更新到过期时间或声明) 都将更改令牌的签名, 并将计为之前尚未遇到密钥传递的新令牌。
* 如果令牌超出`maxuses`了客户设置的值, 则播放将失败。
* 此功能可用于所有现有受保护的内容 (只需更改颁发的令牌)。
* 此功能适用于 JWT 和 SWT。

## <a name="using-a-custom-sts"></a>使用自定义 STS

客户可以选择使用自定义 STS 来提供令牌。 原因包括：

* 客户使用的 IDP 不支持 STS。 在此情况下，可以选择自定义 STS。
* 客户在集成 STS 与客户的订户计费系统时可能需要更多弹性或更紧密的控制。 例如，MVPD 运营商可能提供多个 OTT 订户套餐，如高级、基本和运动。 运营商可能想要让令牌中的声明与订户套餐匹配，这样，只有特定套餐中的内容可供使用。 在此情况下，自定义 STS 可提供所需的弹性和控制度。
* 在令牌中包含自定义声明, 以便在不同的 ContentKeyPolicyOptions 之间选择不同的 DRM 许可证参数 (订阅许可证与租赁许可证)。
* 包含声明, 该声明表示令牌授予访问权限的密钥的内容密钥标识符。

使用自定义 STS 时，必须进行两项更改：

* 为资产配置许可证传送服务时，需要指定自定义 STS 用于验证的安全密钥，而不是来自 Azure AD 的当前密钥。
* 生成 JTW 令牌时，需要指定安全密钥，而不是 Azure AD 中当前 X509 证书的私钥。

有两种类型的安全密钥：

* 对称密钥：使用同一密钥来生成和验证 JWT。
* 非对称密钥：使用 X509 证书中的私钥-公钥对，私钥用于加密/生成 JWT，公钥用于验证令牌。

如果使用 .NET Framework/C# 作为开发平台，用于非对称安全密钥的 X509 证书的密钥长度必须至少为 2048。 这是 .NET Framework 中 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 类的要求。 否则，将引发以下异常：IDX10630:用于签名的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小于 '2048' 位。

## <a name="custom-key-and-license-acquisition-url"></a>自定义密钥和许可证获取 URL

如果要指定不同的许可证/密钥传送服务 (而不是媒体服务), 请使用以下模板。 模板中有两个可替换字段, 可以在多个资产之间共享流式处理策略, 而不是为每个资产创建流策略。 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`：向最终用户播放机传递密钥的自定义服务的 URL 模板。 使用 Azure 媒体服务发出密钥时, 无需执行此方法。 

   该模板支持可替换标记, 服务会在运行时通过特定于请求的值进行更新。  当前支持的令牌值为:
   * `{AlternativeMediaId}`, 它被替换为 StreamingLocatorId. AlternativeMediaId 的值。
   * `{ContentKeyId}`, 它将替换为所请求的密钥的标识符的值。
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`：向最终用户播放器提供许可证的自定义服务的 URL 模板。 使用 Azure 媒体服务颁发许可证时不需要此方法。 

   该模板支持可替换标记, 服务会在运行时通过特定于请求的值进行更新。 当前支持的令牌值为:  
   * `{AlternativeMediaId}`, 它被替换为 StreamingLocatorId. AlternativeMediaId 的值。
   * `{ContentKeyId}`, 它将替换为所请求的密钥的标识符的值。 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`：与上一个模板相同, 仅适用于 Widevine。 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`：与上一个模板相同, 仅适用于 FairPlay。  

例如：

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`具有请求的键的值。 如果要将`AlternativeMediaId`请求映射到一侧的实体, 则可以使用。 例如, `AlternativeMediaId`可用于帮助您查找权限。

 有关使用自定义许可证/密钥获取 Url 的 REST 示例, 请参阅[流式处理策略-创建](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)。

## <a name="troubleshoot"></a>故障排除

如果收到`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`错误, 请确保指定适当的流策略。

如果收到以`_NOT_SPECIFIED_IN_URL`结尾的错误, 请确保在 URL 中指定加密格式。 例如 `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`。 请参阅[流式处理协议和加密类型](#streaming-protocols-and-encryption-types)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [使用 AES 加密进行保护](protect-with-aes128.md)
* [使用 DRM 提供保护](protect-with-drm.md)
* [使用访问控制设计多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)
* [存储端加密](storage-account-concept.md#storage-side-encryption)
* [常见问题](frequently-asked-questions.md)

