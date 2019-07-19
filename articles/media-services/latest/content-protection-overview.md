---
title: 使用 Media Services 动态加密保护内容-Azure |Microsoft Docs
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
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310305"
---
# <a name="content-protection-with-dynamic-encryption"></a>采用动态加密的内容保护

可以使用 Azure 媒体服务在媒体从计算机离开到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 

在媒体服务 v3 中, 内容密钥与流式处理定位符相关联 (请参阅[此示例](protect-with-aes128.md))。 如果使用媒体服务密钥传送服务, 可以让 Azure 媒体服务生成内容密钥。 如果你使用的是自己的密钥传送服务, 或者如果你需要处理需要在两个数据中心具有相同内容密钥的高可用性方案, 则应自行生成内容密钥。

播放器请求流时，媒体服务将通过 AES 明文密钥或 DRM 加密使用指定的密钥来动态加密内容。 为了解密流，播放器将从媒体服务密钥传送服务或者指定的密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

可以使用 REST API 或媒体服务客户端库来配置许可证和密钥的授权与身份验证策略。

下图阐释了媒体服务内容保护工作流： 

![保护内容](./media/content-protection/content-protection.svg)

&#42; *动态加密支持 AES-128“明文密钥”、CBCS 和 CENC。有关详细信息，请参阅[此处](#streaming-protocols-and-encryption-types)的支持矩阵。*

本文介绍与了解使用媒体服务进行内容保护相关的概念和术语。

## <a name="main-components-of-a-content-protection-system"></a>内容保护系统的主要组件

若要成功完成“内容保护”系统/应用程序设计中，需要全面了解工作范围。 以下列表概述了需要实施的三个部分。 

1. Azure 媒体服务代码
  
   [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)示例演示如何使用 .Net 通过媒体服务 v3 实现多 DRM 系统。 它还演示了如何使用媒体服务许可证/密钥传送服务。 可以使用多个加密类型（AES-128、PlayReady、Widevine、FairPlay）来加密每个资产。 请参阅[流式处理协议和加密类型](#streaming-protocols-and-encryption-types)，以了解有效的组合方式。
  
   该示例演示如何：

   1. 创建和配置[内容密钥策略](content-key-policy-concept.md)。 你可以创建**内容密钥策略**来配置如何将内容密钥 (提供对资产的安全访问) 传递给最终客户端。    

      * 定义许可证交付授权，指定基于 JWT 中的声明执行的授权检查逻辑。
      * 配置[PlayReady](playready-license-template-overview.md)、 [Widevine](widevine-license-template-overview.md)和/或[FairPlay](fairplay-license-overview.md)许可证。 使用这些模板可为使用的每个 DRM 配置权利和权限。

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. 创建配置为流式传输加密资产的[流式处理定位符](streaming-locators-concept.md)。 
  
      **流式处理定位符**必须与[流式处理策略](streaming-policy-concept.md)相关联。 在此示例中, 我们将 StreamingPolicyName 设置为 "Predefined_MultiDrmCencStreaming" 策略。 应用 PlayReady 和 Widevine 加密时, 会根据配置的 DRM 许可证将密钥传递到播放客户端。 如果还要使用 CBCS (FairPlay) 加密流，请使用“Predefined_MultiDrmStreaming”。
      
      流式处理定位符还与定义的**内容密钥策略**相关联。
    
   3. 创建测试令牌。

      **GetTokenAsync** 方法演示如何创建测试令牌。
   4. 生成流 URL。

      **GetDASHStreamingUrlAsync** 方法演示如何生成流 URL。 在本例中，URL 流式传输 **DASH** 内容。

2. 使用 AES 或 DRM 客户端的播放器。 基于播放器 SDK 的视频播放器应用（本机或基于浏览器）需要满足以下要求：
   * 播放器 SDK 支持所需的 DRM 客户端
   * 播放器 SDK 支持所需的流式处理协议：平滑流式处理、DASH 和/或 HLS
   * 播放器 SDK 需要能够处理许可证获取请求中 JWT 令牌的传递
  
     可以使用 [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 指定要在不同的 DRM 平台上使用哪种 DRM 技术。

     若要测试 AES 或 CENC (Widevine 和/或 PlayReady) 加密内容，可以使用 [Azure Media Player](https://aka.ms/azuremediaplayer)。 请务必单击“高级选项”，并选择加密选项。

     若要测试 FairPlay 加密内容，请使用[此测试播放器](https://aka.ms/amtest)。 该播放器支持 Widevine、PlayReady 和 FairPlay DRM，以及 AES-128 明文密钥加密。 
    
     需要选择适当的浏览器来测试不同的 DRM：使用 Chrome/Opera/Firefox 测试 Widevine，使用 Microsoft Edge/IE11 测试 PlayReady，使用 macOS 版 Safari 测试 FairPlay。

3. 安全令牌服务 (STS)，颁发 JSON Web 令牌 (JWT) 作为用于访问后端资源的访问令牌。 可以使用 AMS 许可证传送服务作为后端资源。 STS 必须定义以下信息：

   * 颁发者和受众（或范围）
   * 与内容保护中的业务要求相关的声明
   * 签名的对称或非对称验证
   * 密钥滚动更新支持（如有必要）

     可以使用[此 STS 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)测试 STS，此工具支持所有 3 种类型的验证密钥：对称、非对称，或者带密钥滚动更新的 Azure AD。 

> [!NOTE]
> 强烈建议在移到下一部分之前，将重点放在每个部分的完整测试上。 若要测试“内容保护”系统，请使用上述列表中指定的工具。  

## <a name="streaming-protocols-and-encryption-types"></a>流式处理协议和加密类型

可以通过 Azure 媒体服务传送使用 AES 明文密钥或 DRM 加密（利用 PlayReady、Widevine 或 FairPlay）动态加密的内容。 当前可以加密 HTTP Live Streaming (HLS)、MPEG DASH 和平滑流式处理格式。 每个协议支持以下加密方法：

### <a name="hls"></a>HLS

HLS 协议支持以下容器格式和加密方案。

|容器格式|加密方案|URL 示例|
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBC (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBC (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

以下设备支持 HLS/CMAF + FairPlay (包括 HEVC/.H):

* iOS v11 或更高 
* iPhone 8 或更高版本
* Intel 7 代 CPU 的 MacOS 高塞拉利昂

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-短划线协议支持以下容器格式和加密方案。

|容器格式|加密方案|URL 示例
|---|---|---|
|All|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>平滑流

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
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>控制内容访问

可以通过配置内容密钥策略来控制谁有权访问内容。 媒体服务支持通过多种方式对发出密钥请求的用户进行授权。 必须配置内容密钥策略。 客户端（播放器）必须符合该策略才能将密钥传送到客户端。 内容密钥策略可以采用**开放**或**令牌**限制。 

使用令牌限制的内容密钥策略时，内容密钥仅发送到在密钥/许可证请求中提供了有效 JSON Web 令牌 (JWT) 或简单 Web 令牌 (SWT) 的客户端。 此令牌必须是由安全令牌服务 (STS) 颁发的。 可以使用 Azure Active Directory 作为 STS，也可以部署自定义 STS。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，而且令牌中的声明与为密钥/许可证配置的声明相匹配，则媒体服务密钥传送服务会将所请求的密钥/许可证返回到客户端。

配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的安全令牌服务。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务会验证令牌中的这些值是否与模板中的值匹配。

客户通常使用自定义 STS 在令牌中包含自定义声明, 以便在具有不同 DRM 许可证参数的不同 ContentKeyPolicyOptions (订阅许可证与租赁许可证) 之间进行选择, 或包含表示内容密钥的声明令牌授予访问权限的密钥的标识符。

### <a name="token-replay-prevention"></a>令牌重播防护

使用*令牌重播防护*功能, 媒体服务客户可以设置一个限制, 该限制可用于请求密钥或许可证。 客户可以在令牌中添加类型`urn:microsoft:azure:mediaservices:maxuses`的声明, 其中值是令牌可用于获取许可证或密钥的次数。 对密钥传递具有相同令牌的所有后续请求将返回未经授权的响应。 请参阅如何在[DRM 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)中添加声明。
 
#### <a name="considerations"></a>注意事项

* 客户必须能够控制令牌生成。 声明需要置于令牌本身中。
* 使用此功能时, 将在收到请求时, 使用过期时间超过一小时的令牌请求, 但会拒绝未经授权的响应。
* 令牌由其签名唯一标识。 对有效负载的任何更改 (例如, 更新到过期时间或声明) 都将更改令牌的签名, 并将计为之前尚未遇到密钥传递的新令牌。
* 如果令牌超出`maxuses`了客户设置的值, 则播放将失败。
* 此功能可用于所有现有受保护的内容 (只需更改颁发的令牌)。
* 此功能适用于 JWT 和 SWT。

## <a name="custom-key-and-license-acquisition-url"></a>自定义密钥和许可证获取 URL

如果要指定其他密钥和许可证传送服务 (而不是媒体服务), 请使用以下模板。 模板中有两个可替换字段, 可以在多个资产之间共享流式处理策略, 而不是为每个资产创建流策略。 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate-用于向最终用户播放机传递密钥的自定义服务的 URL。 使用 Azure 媒体服务发出密钥时不需要。 该模板支持可替换标记, 服务会在运行时通过特定于请求的值进行更新。  当前支持的令牌值为 {AlternativeMediaId}, 它将替换为 StreamingLocatorId 的值, 而 {ContentKeyId} 将替换为所请求的密钥的值。
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate-用于将许可证传送到最终用户播放机的自定义服务的 URL。 使用 Azure 媒体服务颁发许可证时不需要。 该模板支持可替换标记, 服务会在运行时通过特定于请求的值进行更新。 当前支持的令牌值为 {AlternativeMediaId}, 它将替换为 StreamingLocatorId 的值, 而 {ContentKeyId} 将替换为所请求的密钥的值。 
* StreamingPolicyWidevineConfiguration。 CustomLicenseAcquisitionUrlTemplate-仅适用于 Widevine。 
* StreamingPolicyFairPlayConfiguration。 CustomLicenseAcquisitionUrlTemplate-仅适用于 FairPlay。  

例如：

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

具有正在请求的键的值`AlternativeMediaId` , 如果要将请求映射到你的实体, 则可以使用。 `ContentKeyId` 例如, `AlternativeMediaId`可用于帮助您查找权限。

有关使用自定义密钥和许可证获取 Url 的 REST 示例, 请参阅[流式处理策略-创建](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>故障排除

如果收到`MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`错误消息, 请确保指定适当的流策略。

如果收到以`_NOT_SPECIFIED_IN_URL`结尾的错误, 请确保在 URL 中指定加密格式。 例如， `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)` 。 请参阅[流式处理协议和加密类型](#streaming-protocols-and-encryption-types)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

* [使用 AES 加密进行保护](protect-with-aes128.md)
* [使用 DRM 提供保护](protect-with-drm.md)
* [使用访问控制设计多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)
* [存储端加密](storage-account-concept.md#storage-side-encryption)
* [常见问题](frequently-asked-questions.md)

