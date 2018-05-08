---
title: 使用 Azure 门户配置内容密钥授权策略 | Microsoft Docs
description: 了解如何配置内容密钥的授权策略。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 33b958b97a5883d585bbfda167db35107c0c5997
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="configure-a-content-key-authorization-policy"></a>配置内容密钥授权策略
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>概述
 可以使用 Azure 媒体服务传送受高级加密标准 (AES)（使用 128 位加密密钥）或受 [PlayReady 数字版权管理 (DRM)](https://www.microsoft.com/playready/overview/) 保护的 MPEG-DASH 流、平滑流式处理流和 HTTP 实时流式处理 (HLS) 流。 使用媒体服务，还可以传送通过 Widevine DRM 加密的 DASH 流。 PlayReady 和 Widevine 都是按通用加密 (ISO/IEC 23001-7 CENC) 规范加密的。

媒体服务还提供了一个密钥\许可证传送服务，客户端可从中获取 AES 密钥或 PlayReady/Widevine 许可证，以用于播放加密的内容。

本文介绍了如何使用 Azure 门户配置内容密钥授权策略。 以后，可以使用该密钥来动态加密内容。 当前可以对 HLS、MPEG DASH 和平滑流式处理格式进行加密。 无法对渐进式下载进行加密。

当播放器请求已设置为动态加密的流时，媒体服务将使用所配置的密钥通过 AES 或 DRM 加密来对内容进行动态加密。 为解密流，播放器从密钥传送服务请求密钥。 为了确定用户是否被授权获取密钥，服务将评估你为密钥指定的授权策略。

如果计划创建多个内容密钥，或者想要指定除媒体服务密钥传送服务以外的密钥\许可证传送服务 URL，请使用媒体服务 .NET SDK 或 REST API。 有关详细信息，请参阅：

* [使用媒体服务 .NET SDK 配置内容密钥授权策略](media-services-dotnet-configure-content-key-auth-policy.md)
* [使用媒体服务 REST API 配置内容密钥授权策略](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>需要注意的一些事项
* 创建媒体服务帐户时，会将一个处于“已停止”状态的默认流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，流式处理终结点必须处于“正在运行”状态。 
* 资产必须包含一组自适应比特率 MP4 或自适应比特率平滑流文件。 有关详细信息，请参阅[对资产进行编码](media-services-encode-asset.md)。
* 密钥传送服务将 ContentKeyAuthorizationPolicy 及其相关对象（策略选项和限制）缓存 15 分钟。 可以创建 ContentKeyAuthorizationPolicy 并指定使用令牌限制，对其进行测试，然后更新策略以开放限制。 在策略切换到开放版本之前，此过程需要花费大约 15 分钟。
* 媒体服务流式处理终结点将预检响应中 CORS Access-Control-Allow-Origin 标头的值设置为通配符“\*”。 此值适用于大多数播放器，其中包括 Azure Media Player、Roku、JWPlayer 等。 但是，这不适用于一些使用 dash.js 的播放器，因为将凭据模式设置为“包含”之后，dash.js 中的 XMLHttpRequest 不允许将通配符“\*”作为 Access-Control-Allow-Origin 的值。 作为 dash.js 中这一限制的解决办法，如果你将客户端承载在单个域中，则媒体服务可以指定预检响应标头中的域。 若需帮助，请通过 Azure 门户打开支持票证。

## <a name="configure-the-key-authorization-policy"></a>配置密钥授权策略
若要配置密钥授权策略，请选择“内容保护”页。

媒体服务支持通过多种方式对发出密钥请求的用户进行身份验证。 内容密钥授权策略可以具有“开放”、“令牌”或 IP 授权限制。 （可以使用 REST 或 .NET SDK 配置 IP。）

### <a name="open-restriction"></a>开放限制
“开放”限制意味着系统会将密钥传送到发出密钥请求的任何用户。 此限制可能适用于测试用途。

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>令牌限制
若要选择令牌限制策略，请选择“令牌”按钮。

令牌受限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用简单 Web 令牌 ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 格式和 JSON Web 令牌 (JWT) 格式的令牌。 有关详细信息，请参阅 [JWT 身份验证](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。

媒体服务不提供 STS。 可以创建自定义 STS 或使用 Azure 访问控制服务来颁发令牌。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，并且令牌中的声明与为内容密钥配置的声明相匹配，则媒体服务密钥传送服务会将加密密钥返回到客户端。 有关详细信息，请参阅[使用 Azure 访问控制服务颁发令牌](http://mingfeiy.com/acs-with-key-services)。

配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的 STS。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务会验证令牌中的这些值是否与模板中的值匹配。

### <a name="playready"></a>PlayReady
使用 PlayReady 保护内容时，需要在授权策略中指定的项目之一是用于定义 PlayReady 许可证模板的 XML 字符串。 默认情况下，已设置以下策略：

    <PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

可以选择“导入策略 xml”按钮并提供遵循在[媒体服务 PlayReady 许可证模板概述](media-services-playready-license-template-overview.md)中定义的 XML 架构的一个不同 XML。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

