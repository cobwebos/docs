---
title: "使用 Axinom 将 Widevine 许可证传送到 Azure 媒体服务 | Microsoft Docs"
description: "本文介绍如何使用 Azure 媒体服务(AMS) 传送 AMS 使用 PlayReady 和 Widevine DRM 动态加密的流。 PlayReady 许可证来自媒体服务 PlayReady 许可证服务器，而 Widevine 许可证则由 Axinom 许可证服务器传送。"
services: media-services
documentationcenter: 
author: willzhan
manager: erikre
editor: 
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c02b75dc2c783b96dc9e1256051b8c6df290d425
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>使用 Axinom 将 Widevine 许可证传送到 Azure 媒体服务
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>概述
Azure 媒体服务 (AMS) 已添加 Google Widevine 动态保护（有关详细信息，请参阅 [Mingfei 的博客](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)）。 此外，Azure 媒体播放器 (AMP) 还添加了 Widevine 支持（有关详细信息，请参阅 [AMP 文档](http://amp.azure.net/libs/amp/latest/docs/)）。 这是在配备了 MSE 和 EME 的现代浏览器上流式处理受 CENC 保护的 DASH 内容方面的一项重要成就，该保护是通过 multi-native-DRM（PlayReady 和 Widevine）来完成的。

从媒体服务 .NET SDK 版本 3.5.2 开始，媒体服务允许你配置 Widevine 许可证模板并获取 Widevine 许可证。 还可以通过以下 AMS 合作伙伴来交付 Widevine 许可证：[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/)。

本文介绍如何集成和测试由 Axinom 管理的 Widevine 许可证服务器。 具体而言，本文涵盖：  

* 使用 multi-DRM（PlayReady 和 Widevine）通过相应的许可证获取 URL 配置动态通用加密；
* 生成 JWT 令牌以满足许可证服务器要求；
* 开发 Azure Media Player 应用，以便通过 JWT 令牌身份验证处理许可证获取事项；

下图很好地描述了整个系统以及内容密钥、密钥 ID、密钥种子、JWT 令牌及其声明的流动情况。

![DASH 和 CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>内容保护
若要了解如何配置动态保护和密钥传递策略，请查看 Mingfei 的博客：[如何通过 Azure 媒体服务配置 Widevine 打包](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)。

你可以通过 multi-DRM 配置动态 CENC 保护，因为 DASH 流式处理具有下述两项特点：

1. 适用于 MS Edge 和 IE11 的 PlayReady 保护，可能存在令牌授权限制。 令牌限制策略必须附带由 Azure Active Directory 之类的安全令牌服务 (STS) 颁发的令牌；
2. 针对 Chrome 的 Widevine 保护，可能要求使用其他 STS 颁发的令牌进行令牌身份验证。 

请参阅 [JWT 令牌生成](media-services-axinom-integration.md#jwt-token-generation)部分，了解为何不能将 Azure Active Directory 用作 Axinom 的 Widevine 许可证服务器的 STS。

### <a name="considerations"></a>注意事项
1. 你必须使用 Axinom 指定的密钥种子 (8888000000000000000000000000000000000000) 以及你自己生成或选择的密钥 ID 来生成内容密钥，以便配置密钥传递服务。 Axinom 许可证服务器会颁发所有包含内容密钥的许可证，这些内容密钥基于同一密钥种子，而该种子可同时用于测试和生产。
2. 用于测试的 Widevine 许可证获取 URL：[https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)。 HTTP 和 HTTS 都是允许的。

## <a name="azure-media-player-preparation"></a>Azure Media Player 准备
AMP v1.4.0 支持播放使用 PlayReady 和 Widevine DRM 进行动态打包的 AMS 内容。
如果 Widevine 许可证服务器不需要令牌身份验证，则不需执行任何其他操作即可测试受 Widevine 保护的 DASH 内容。 例如，AMP 团队提供简单的[示例](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html)，在该示例中可以看到它在使用 PlayReady 的 Edge 和 IE11 中以及使用 Widevine 的 Chrome 中运行。
Axinom 提供的 Widevine 许可证服务器要求 JWT 令牌身份验证。 需要通过 HTTP 标头“X-AxDRM-Message”提交带许可证请求的 JWT 令牌。 为此，你需要在设置源之前在承载 AMP 的 Web 页中添加以下 javascript：

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

AMP 代码的其余部分为标准 AMP API，如[此处](http://amp.azure.net/libs/amp/latest/docs/)的 AMP 文档所述。

请注意，在 AMP 中的正式长期方法发布之前，上述 javascript 只能算是一个设置自定义授权标头的短期方法。

## <a name="jwt-token-generation"></a>生成 JWT 令牌
用于测试的 Axinom Widevine 许可证服务器要求 JWT 令牌身份验证。 此外，JWT 令牌中的声明之一是复杂的对象类型而不是基元数据类型。

遗憾的是，Azure AD 只能颁发基元类型的 JWT 令牌。 同样，.NET Framework API（System.IdentityModel.Tokens.SecurityTokenHandler 和 JwtPayload）只允许你输入复杂对象类型作为声明。 但是，这些声明仍会序列化为字符串。 因此，这两种类型都不能用于生成 Widevine 许可证请求所需的 JWT 令牌。

John Sheehan 的 [JWT Nuget 包](https://www.nuget.org/packages/JWT)符合要求，因此将使用该 Nuget 包。

下面是通过所需声明生成 JWT 令牌的代码，这些声明是 Axinom Widevine 许可证进行测试所需的：

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine 许可证服务器

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>注意事项
1. 虽然 AMS PlayReady 许可证交付服务要求使用“Bearer=”作为身份验证令牌的前缀，但 Axinom Widevine 许可证服务器不使用它。
2. Axinom 通信密钥可用作签名密钥。 请注意，该密钥为十六进制字符串，但在进行编码时，必须将其视为一系列字节而非字符串。 这是通过方法 ConvertHexStringToByteArray 实现的。

## <a name="retrieving-key-id"></a>检索密钥 ID
你可能已经注意到，在用于生成 JWT 令牌的代码中，密钥 ID 是必需的。 由于在加载 AMP 播放器之前需确保 JWT 令牌就绪，因此需检索密钥 ID 以生成 JWT 令牌。

当然，可以通过多种方式来获取密钥 ID。 例如，你可以将密钥 ID 与内容元数据一起存储在数据库中。 也可以从 DASH MPD（媒体演示文稿说明）文件检索密钥 ID。 下面的代码是针对后一种方式。

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>摘要
由于最近在 Azure 媒体服务内容保护和 Azure Media Player 中增加了 Widevine 支持，因此我们能够实现对 DASH + Multi-native-DRM (PlayReady + Widevine) 的流式处理，这既适用于 AMS 中的 PlayReady 许可证服务，也适用于 Axinom 提供的 Widevine 许可证服务器，面向以下现代浏览器：

* Chrome
* Windows 10 中的 Microsoft Edge
* Windows 8.1 和 Windows 10 中的 IE 11
* 同时还支持 Firefox（桌面版）和 Mac（非 iOS）上的 Safari，二者均可通过 Silverlight 使用，同一 URL 也适用于 Azure Media Player

在利用 Axinom Widevine 许可证服务器的迷你解决方案中，需要以下参数。 Axinom 还根据其 Widevine 服务器安装情况提供其他参数，密钥 ID 除外。

| 参数 | 使用方式 |
| --- | --- |
| 通信密钥 ID |必须包括，在 JWT 令牌中作为声明“com_key_id”的值（参见[此](media-services-axinom-integration.md#jwt-token-generation)部分）。 |
| 通信密钥 |必须用作 JWT 令牌的签名密钥（参见[此](media-services-axinom-integration.md#jwt-token-generation)部分）。 |
| 密钥种子 |必须在提供了内容密钥 ID 的情况下用于生成内容密钥（参见[此](media-services-axinom-integration.md#content-protection)部分）。 |
| Widevine 许可证获取 URL |必须用于配置资产传送策略，以便进行 DASH 流式处理（参见[此](media-services-axinom-integration.md#content-protection)部分）。 |
| 内容密钥 ID |必须包括，作为 JWT 令牌的授权消息声明值的一部分（参见[此](media-services-axinom-integration.md#jwt-token-generation)部分）。 |

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>致谢
我们诚挚地向以下人员表达谢意，是他们协助完成了本文档的创作工作：Kristjan Jõgi of Axinom、Mingfei Yan 和 Amit Rajput。


