---
title: "使用多重 DRM 的 CENC 和访问控制：Azure 与 Azure 媒体服务的参考设计和实现 | Microsoft Docs"
description: "了解如何为 Microsoft® 平滑流式处理客户端移植工具包授权。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 50bcb71cd4f52386e9ea428fc124ac30ae9a862b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>使用多重 DRM 的 CENC 和访问控制：Azure 与 Azure 媒体服务的参考设计和实现
 
## <a name="introduction"></a>介绍
众所周知，针对 OTT 或在线流解决方案设计和构建 DRM 子系统是相当复杂的任务。 运营商/在线视频提供商通常会将这部分业务外包给专门的 DRM 服务提供商。 本文档旨在陈述 OTT 或在线流解决方案中端到端 DRM 子系统的参考设计和实现。

本文档的目标读者是使用 OTT 或在线流/多屏解决方案的 DRM 子系统的工程师，或对于 DRM 子系统有兴趣的任何读者。 假设读者熟悉市场上的至少一种 DRM 技术，例如 PlayReady、Widevine、FairPlay 或 Adobe Access。

在 DRM 方面，我们还介绍使用多重 DRM 的 CENC（通用加密）。 在线流和 OTT 行业中的主要趋势是在各种客户端平台上使用 CENC 与多重原生 DRM，这是从以前对于各种客户端平台使用单个 DRM 和客户端 SDK 的趋势变化而来的。 使用 CENC 与多重原生 DRM 时，PlayReady 和 Widevine 都按照[通用加密 (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 规范加密。

使用 CENC 与多重 DRM 的好处如下：

1. 降低加密成本，因为对于不同平台使用其原生 DRM 进行单一加密处理；
2. 降低管理加密资产的成本，因为只需要一份加密资产；
3. 消除 DRM 客户端许可成本，因为原生 DRM 客户端在其原生平台上通常是免费提供的。

Microsoft 已成为 DASH 和 CENC 与其他一些主要行业播放器的积极推动者。 Microsoft Azure 媒体服务为 DASH 和 CENC 提供支持。 有关最新的通告，请参阅 Mingfei 的博客：[Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)（宣布已在 Azure 媒体服务中推出 Google Widevine 许可证传送服务）和 [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)（Azure 媒体服务已添加 Google Widevine 打包功能用于传送多重 DRM 流）。  

### <a name="overview-of-this-article"></a>本文的概述
本文的目标包括：

1. 提供使用 CENC 与多重 DRM 的 DRM 子系统的参考设计；
2. 提供 Microsoft Azure/Azure 媒体服务平台上的参考实现；
3. 讨论一些设计和实现主题。

在本文中，“多重 DRM”涵盖以下产品：

1. Microsoft PlayReady
2. Google Widevine
3. Apple FairPlay 

下表汇总了每个 DRM 支持的原生平台/原生应用和浏览器。

| **客户端平台** | **原生 DRM 支持** | **浏览器/应用** | **流格式** |
| --- | --- | --- | --- |
| **智能电视、操作员 STB、OTT STB** |主要为 PlayReady，和/或 Widevine，和/或其他 DRM |Linux、Opera、WebKit 及其他 |各种格式 |
| **Windows 10 设备（Windows 电脑、Windows 平板电脑、Windows Phone、Xbox）** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |DASH（对于 HLS，不支持 PlayReady）<br/><br/>DASH、平滑流式处理（对于 HLS，不支持 PlayReady） |
| **Android 设备（手机、平板电脑、电视）** |Widevine |Chrome/EME |DASH、HLS |
| **iOS（iPhone、iPad）、OS X 客户端和 Apple 电视** |FairPlay |Safari 8+/EME |HLS |


就目前每种 DRM 的部署状态而言，服务通常需要实现 2 到 3 个 DRM，以确保能以最佳方式处理所有类型的终结点。

必须在服务逻辑复杂性与客户端复杂性之间做出取舍，以便各种不同客户端上的用户体验能够达到特定的程度。

做出选择时，请记住以下几点：

* PlayReady 原生实现在每种 Windows 设备和部分 Android 设备上，且可通过软件 SDK 在几乎所有平台上实现
* Widevine 原生实现在每种 Android 设备、Chrome 和部分其他设备上
* FairPlay 只可在 iOS 和 Mac OS 客户端中使用，或通过 iTunes 使用。

因此，典型的多重 DRM 是：

* 选项 1：PlayReady 和 Widevine
* 选项 2：PlayReady、Widevine 和 FairPlay

## <a name="a-reference-design"></a>参考设计
本部分探讨一种参考设计，这种设计与用于实现它的技术无关。

DRM 子系统可能包含以下组件：

1. 密钥管理
2. DRM 打包
3. DRM 许可证传送
4. 权利检查
5. 身份验证/授权
6. 播放器
7. 来源/CDN

下图演示了 DRM 子系统中组件之间的高级交互。

![使用 CENC 的 DRM 子系统](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

设计中有三个基本“层”：

1. 后端办公系统层（黑色表示），不对外部公开；
2. 外围网络层（蓝色表示），包含所有面向公众的终结点；
3. 公共 Internet 层（浅蓝色表示），包含具有跨公共 Internet 流量的 CDN 和播放器。

无论是静态加密还是动态加密，都应该有一个用于控制 DRM 保护的内容管理工具。 DRM 加密的输入应该包括：

1. MBR 视频内容；
2. 内容密钥；
3. 许可证获取 URL。

在播放期间，高级流程是：

1. 对用户进行身份验证；
2. 为用户创建授权令牌；
3. 将 DRM 保护的内容（清单）下载到播放器；
4. 播放器将许可证获取请求和密钥 ID 与授权令牌提交到许可证服务器。

在进入下一主题前，简单介绍一下密钥管理的设计。

| **ContentKey–to-Asset** | **方案** |
| --- | --- |
| 一对一 |最简单的情况。 它提供最精细的控制。 但是，通常产生最高的许可证传送成本。 每个受保护的资产需要至少一个许可证请求。 |
| 一对多 |可以对多个资产使用相同的内容密钥。 例如，对于如流派或流派子集（或电影基因）的逻辑组中的所有资产，可以使用单个内容密钥。 |
| 多对一 |每个资产需要多个内容密钥。 <br/><br/>例如，如果需要针对 MPEG-DASH 应用具有多重 DRM 的动态 CENC 保护，且针对 HLS 应用动态 AES-128 加密，则需要两个单独的内容密钥，两者分别有自己的 ContentKeyType。 （对于用于动态 CENC 保护的内容密钥，应使用 ContentKeyType.CommonEncryption，而对于用于动态 AES-128 加密的内容密钥，应使用 ContentKeyType.EnvelopeEncryption。）<br/><br/>再举一个示例，在 DASH 内容的 CENC 保护中，从理论上讲，可以使用一个内容密钥来保护视频流，以及使用另一个内容密钥来保护音频流。 |
| 多对多 |结合上述两种方案：对于相同数据“组”中的每个多重资产，使用一组内容密钥。 |

另一个需要考虑的重要因素是持久性和非持续久许可证的使用。

为何这些考虑因素非常重要？

如果针对许可证传送使用公有云，它们对于许可证传送成本有直接影响。 假设有以下两个不同的设计方案：

1. 每月订阅：使用永久性许可证，以及 1 对多内容密钥到资产映射。 例如 对于所有儿童影片，使用单个内容密钥进行加密。 在这种情况下：

    所有儿童影片/设备的许可证总数 = 1
2. 每月订阅：在内容密钥与资产之间使用非永久性许可证以及 1 对 1 映射。 在这种情况下：

    所有儿童影片/设备的许可证总数 = [观看影片数] x [会话数]

很容易就会发现，如果许可证传送服务是由 Azure 媒体服务等公有云提供的，则两种不同的设计会导致大不相同的许可证请求模式，进而造成许可证传送成本的不同。

## <a name="mapping-design-to-technology-for-implementation"></a>将设计映射到技术以供实现
接下来，我们通过指定用于每个构建块的技术，将常规设计映射到 Microsoft Azure/Azure 媒体服务平台上的技术。

下表显示了映射：

| **构建块** | **技术** |
| --- | --- |
| **播放器** |[Azure 媒体播放器](https://azure.microsoft.com/services/media-services/media-player/) |
| **标识提供者 (IDP)** |Azure Active Directory |
| **安全令牌服务 (STS)** |Azure Active Directory |
| **DRM 保护工作流** |Azure 媒体服务动态保护 |
| **DRM 许可证传送** |1.Azure 媒体服务许可证传送（PlayReady、Widevine、FairPlay），或 <br/>2.Axinom 许可证服务器， <br/>3.自定义 PlayReady 许可证服务器 |
| **源** |Azure 媒体服务流式处理终结点 |
| **密钥管理** |不需要参考实现 |
| **内容管理** |一个 C# 控制台应用程序 |

换而言之，标识提供者 (IDP) 和安全令牌服务 (STS) 都将是 Azure AD。 对于播放器，我们将使用 [Azure 媒体播放器 API](http://amp.azure.net/libs/amp/latest/docs/)。 Azure 媒体服务和 Azure Media Player 都支持具有多重 DRM 的 DASH 和 CENC。

下图显示了上述技术映射的整体结构与流程。

![AMS 上的 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

为了设置动态 CENC 加密，内容管理工具将使用以下输入：

1. 公开的内容；
2. 来自密钥生成/管理的内容密钥；
3. 许可证获取 URL；
4. Azure AD 中的信息列表。

内容管理工具的输出是：

1. ContentKeyAuthorizationPolicy，包含有关许可证传送如何验证 JWT 令牌和 DRM 许可证规范的规范；
2. AssetDeliveryPolicy，包含有关流格式、DRM 保护和许可证获取 URL 的规范。

在运行时，流程如下所示：

1. 对用户进行身份验证后，生成 JWT 令牌；
2. JWT 令牌中包含的声明之一是“组”声明，包含“EntitledUserGroup”的组对象 ID。 此声明用于传递“权利检查”。
3. 播放器下载 CENC 保护内容的客户端清单，并可以“看到”以下信息：

   1. 密钥 ID，
   2. 内容受 CENC 保护，
   3. 许可证获取 URL。
4. 播放器根据支持的浏览器/DRM 发出许可证获取请求。 在许可证获取请求中，还会提交密钥 ID 和 JWT 令牌。 许可证传送服务会验证 JWT 令牌，在发布所需的许可证之前包含声明。

## <a name="implementation"></a>实现
### <a name="implementation-procedures"></a>实现过程
实现将包含以下步骤：

1. 准备测试资产：将测试视频编码/打包为 Azure 媒体服务中的多比特率分段 MP4。 此资产不受 DRM 保护。 DRM 保护稍后由动态保护完成。
2. 创建密钥 ID 和内容密钥（可以选择从密钥种子中获取）。 对于本教程，我们不需要密钥管理系统，因为只针对一些测试资产处理一组密钥 ID 和内容密钥；
3. 使用 AMS API 来对测试资产配置多重 DRM 许可证传送服务。 如果使用公司或公司供应商的自定义许可证服务器，而不是 Azure 媒体服务中的许可证服务，则可以跳过此步骤，而是在配置许可证传送的步骤中指定许可证获取 URL。 需要使用 AMS API 指定一些详细配置，例如不同 DRM 许可证服务的许可证策略限制、许可证响应模板，等等。目前，Azure 门户尚未提供此配置所需的 UI。 可以在以下文章中查找 API 级别信息和示例代码：[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-playready-widevine.md)。
4. 使用 AMS API 针对测试资产配置资产传送策略。 可以在以下文章中查找 API 级别信息和示例代码：[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-playready-widevine.md)。
5. 在 Azure 中创建和配置 Azure Active Directory 租户；
6. 在 Azure Active Directory 租户中创建一些用户帐户和组：应该至少创建“EntitledUser”组，并将用户添加到此组。 此组中的用户将可通过许可证获取的权利检查，而不在此组中的用户将无法通过身份验证检查，且无法获取任何许可证。 成为此“EntitledUser”组的成员，是由 Azure AD 发出的 JWT 令牌中所需的“组”声明。 此声明要求应该在配置多重 DRM 许可证传送服务步骤中指定。
7. 创建 ASP.NET MVC 应用，用于托管视频播放器。 此 ASP.NET 应用会根据 Azure Active Directory 租户受到用户身份验证的保护。 对用户进行身份验证后，适当的声明将包含在获取的访问令牌中。 建议在此步骤中使用 OpenID Connect API。 需要安装以下 NuGet 包：
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. 使用 [Azure 媒体播放器 API](http://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/)，可指定要在不同的 DRM 平台上使用哪种 DRM 技术。
9. 测试矩阵：

| **DRM** | **浏览器** | **已获授权用户的结果** | **未获授权用户的结果** |
| --- | --- | --- | --- |
| **PlayReady** |MS Edge 或 Windows 10 上的 IE11 |成功 |失败 |
| **Widevine** |Windows 10 上的 Chrome |成功 |失败 |
| **FairPlay** |TBD | | |

Azure 媒体服务团队的 George Trifonov 撰写的博客提供了针对 ASP.NET MVC 播放器应用配置 Azure Active Directory 的详细步骤：[将基于 Azure 媒体服务 OWIN MVC 的应用与 Azure Active Directory 相集成，并基于 JWT 声明限制内容密钥传送](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

George 还撰写了一篇有关 [Azure 媒体服务和动态加密中的 JWT 令牌身份验证](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)的博客文章。  

有关 Azure Active Directory 的详细信息：

* 可以在 [Azure Active Directory 开发人员指南](../active-directory/active-directory-developers-guide.md)中找到面向开发人员的信息。
* 可以在[管理 Azure AD 目录](../active-directory/active-directory-administer.md)中找到面向管理员的信息。

### <a name="some-gotchas-in-implementation"></a>实现中的一些难点
实现中存在一些“难点”。 希望以下“难点”列表可以帮助你在遇到问题时进行故障排除。

1. **颁发者** URL 应以**“/”**结尾。  

    **受众**应该是播放器应用程序客户端 ID，并且还应该在颁发者 URL 的末尾添加**“/”**。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    在 [JWT 解码器](http://jwt.calebb.net/)中，应该可以在 JWT 令牌中看到 **aud** 和 **iss**，如下所示：

    ![第一个难点](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. 将权限添加到 AAD 中的应用程序内（在应用程序的“配置”选项卡上）。 对于每个应用程序都必须这样做（本地版本和已部署的版本）。

    ![第二个难点](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. 使用正确的颁发者设置动态 CENC 保护：

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    以下操作不可行：

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID 是 AAD 租户 ID。 GUID 可以在 Azure 门户上的终结点弹出窗口中找到。
4. 授予组成员资格声明权限。 确保 AAD 应用程序清单文件中有以下内容

    "groupMembershipClaims": "All"（默认值为 null）
5. 创建限制要求时设置适当的 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    由于除了 SWT (AAD) 以外还添加了 JWT (ACS) 支持，因此默认 TokenType 是 TokenType.JWT。 如果使用 SWT/ACS，则必须设置为 TokenType.SWT。

## <a name="additional-topics-for-implementation"></a>有关实现的其他主题
接下来将讨论设计和实现中的一些其他主题。

### <a name="http-or-https"></a>使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

1. 通过 Azure AD 进行用户身份验证（需要使用 HTTPS）；
2. 客户端与 Azure AD 之间的 JWT 令牌交换（需要使用 HTTPS）；
3. 客户端的 DRM 许可证获取，如果许可证传送由 Azure 媒体服务提供（需要使用 HTTPS）。 当然，PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于 Azure 媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

因此，最佳实践是让 ASP.NET 播放器应用程序使用 HTTPS。 这意味着 Azure Media Player 会在使用 HTTPS 的页面上。 不过，我们更偏向于使用 HTTP 进行流式传输，因此需要考虑混合内容问题。

1. 浏览器不允许混合内容。 但是 Silverlight 等插件和适用于平滑流与 DASH 的 OSMF 插件允许混合内容。 混合内容存在安全隐患 - 因为能够插入恶意 JS 威胁，从而使客户数据处于风险之中。  默认情况下，浏览器将阻止此类内容，目前唯一的解决方法是在服务器（来源）端允许所有域（不管是 https 还 http）。 这可能也不是个好主意。
2. 应该避免混合内容：都使用 HTTP，或者都使用 HTTPS。 当播放混合内容时，silverlightSS 技术需要清除混合内容警告。 flashSS 技术在没有混合内容警告的情况下处理混合内容。
3. 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

在参考实现中，对于 DRM 保护内容，应用程序和流都使用 HTTTPS。 对于打开的内容，播放器不需要身份验证或许可证，因此可以任意使用 HTTP 或 HTTPS。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 签名密钥滚动更新
这是需要在实现中考虑的要点。 如果未在实现中考虑，整个系统最终会在最多 6 周之后完全停止运行。

Azure AD 使用行业标准，通过 Azure AD 在本身与应用程序之间建立信任。 具体而言，Azure AD 使用签名密钥，该密钥由公钥和私钥对组成。 当 Azure AD 创建包含用户相关信息的安全令牌时，Azure AD 将使用私钥对令牌进行签名，然后将令牌发回给应用程序。 若要验证该令牌是否有效且确实来自 Azure AD，应用程序必须使用由 Azure AD 公开，包含在租户的联合元数据文档中的公钥来验证令牌的签名。 此公钥 – 以及衍生它的签名密钥 – 是由 Azure AD 中所有租户使用的同一个密钥。

以下文档提供了有关 Azure AD 密钥滚动更新的详细信息：[有关 Azure AD 中签名密钥滚动更新的重要信息](../active-directory/active-directory-signing-key-rollover.md)。

在[公钥 - 私钥对](https://login.microsoftonline.com/common/discovery/keys/)之间，

* Azure Active Directory 使用私钥生成 JWT 令牌；
* 应用程序（例如 AMS 中的 DRM 许可证传送服务）使用公钥验证 JWT 令牌；

出于安全目的，Azure Active Directory 将定期轮换此证书（每隔 6 周）。 在发生安全违规时，密钥滚动更新可随时发生。 因此，AMS 中的许可证传送服务需要在 Azure AD 轮换密钥对时更新公钥，否则 AMS 中的令牌身份验证会失败，并且不颁发任何许可证。

这是在配置 DRM 许可证传送服务时通过设置 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument 来实现的。

JWT 令牌流程如下：

1. Azure AD 使用当前私钥为已经过身份验证的用户颁发 JWT 令牌；
2. 当播放器看到使用多重 DRM 的 CENC 保护的内容时，会先找到 Azure AD 颁发的 JWT 令牌。
3. 播放器将包含 JWT 令牌的许可证获取请求发送到 AMS 中的许可证传送服务；
4. AMS 中的许可证传送服务使用来自 Azure AD 的当前/有效公钥来验证 JWT 令牌，并颁发许可证。

DRM 许可证传送服务始终会检查来自 Azure AD 的当前/有效公钥。 Azure AD 提供的公钥是用于验证 Azure AD 所颁发 JWT 令牌的密钥。

如果 AAD 生成 JWT 令牌之后、播放器将 JWT 令牌发送到 AMS 中的 DRM 许可证传送服务以进行验证之前发生密钥滚动更新，会怎么样？

因为密钥可能在任何时间滚动更新，联合元数据文档中始终有多个有效公钥可用。 Azure 媒体服务许可证传送可以使用文档中指定的任何密钥，因为一个密钥可能很快就被轮换为另一个密钥。

### <a name="where-is-the-access-token"></a>访问令牌位于何处？
如果在[带有 OAuth 2.0 客户端凭据授权的应用程序标识](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api)下，Web 应用调用 API 应用，身份验证流程如下：

1. 用户在 Web 应用程序中登录到 Azure AD（请参阅 [Web 浏览器到 Web 应用程序](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)。
2. Azure AD 许可证终结点使用授权代码将用户代理重定向回到客户端应用程序。 用户代理将授权代码返回给客户端应用程序的重定向 URI。
3. Web 应用程序需要获取访问令牌，以便通过 Web API 进行身份验证并检索所需的资源。 它向 Azure AD 的令牌终结点发出一个请求，在其中提供凭据、客户端 ID 以及 Web API 的应用程序 ID URI。 它将提供授权代码来证明已获得用户许可。
4. Azure AD 对应用程序进行身份验证并返回用来调用 Web API 的 JWT 访问令牌。
5. 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。

在此“应用程序标识”流程中，Web API 相信 Web 应用程序已对用户进行了身份验证。 因此，此模式称为受信任的子系统。 [此页面上的示意图](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code)描绘了授权代码授予流程。

在具有令牌限制的许可证获取中，遵循相同的受信任子系统模式。 Azure 媒体服务中的许可证传送服务是 Web API 资源，即 Web 应用程序需要访问的“后端资源”。 那么，访问令牌位于何处？

确实，我们是从 Azure AD 获取访问令牌的。 成功完成用户身份验证后，将返回授权代码。 然后使用授权代码连同客户端 ID 和应用密钥来交换访问令牌。 访问令牌用于访问指向或代表 Azure 媒体服务许可证传送服务的“指针”应用程序。

我们需要遵照以下步骤，在 Azure AD 中注册并配置“指针”应用程序：

1. 在 Azure AD 租户中

   * 添加应用程序（资源）与登录 URL：

   https://[resource_name].azurewebsites.net/ 以及

   * 应用 ID URL：

   https://[aad_tenant_name].onmicrosoft.com/[resource_name]；
2. 为资源应用添加新的密钥；
3. 更新应用程序清单文件，使 groupMembershipClaims 属性具有以下值："groupMembershipClaims": "All"；  
4. 在指向播放器 Web 应用的 Azure AD 应用中，在“对其他应用程序的权限”部分添加上述步骤 1 中添加的资源应用。 在“委派权限”下面选中“访问 [资源名称]”复选框。 这样便可授予 Web 应用创建访问令牌的权限以访问资源应用。 如果使用 Visual Studio 和 Azure Web 应用进行开发，应该对本地和已部署版本的 Web 应用这样做。

因此，Azure AD 颁发的 JWT 令牌确实是用于访问此“指针”资源的访问令牌。

### <a name="what-about-live-streaming"></a>如何使用实时流？
前面的讨论内容将重点放在按需资产上。 如何使用实时流？

好消息是，可以使用完全相同的设计和实现来保护 Azure 媒体服务中的实时流，方法是将与节目关联的资产视为“VOD 资产”。

具体而言，众所周知，要在 Azure 媒体服务中执行实时流，需要创建通道，并在通道下面创建节目。 若要创建节目，需要创建资产并在其中包含节目的实时存档。 要使用带有多重 DRM 的 CENC 来保护实时内容，只需要在启动节目之前将相同设置/处理应用到资产，就如同它是“VOD 资产”一样。

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>如何使用 Azure 媒体服务外部的许可证服务器？
通常，客户可能已在他们自己的数据中心或由 DRM 服务提供商托管的位置投资了许可证服务器场。 幸运的是，Azure 媒体服务内容保护可让在混合模式下运行：内容在 Azure 媒体服务中托管并以动态方式保护，而 DRM 许可证由 Azure 媒体服务外部的服务器传送。 在此情况下，存在以下变更注意事项：

1. 安全令牌服务需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT 令牌，其中包含“权利消息”。 因此，需要有 STS 来颁发此类 JWT 令牌。 作者已完成此实现，并且可以在以下 [Azure 文档中心](https://azure.microsoft.com/documentation/)中的以下文档中查找详细信息：[使用 Axinom 将 Widevine 许可证传送到 Azure 媒体服务](media-services-axinom-integration.md)。
2. 不再需要在 Azure 媒体服务中配置许可证传送服务 (ContentKeyAuthorizationPolicy)。 需要做的就是，在设置使用多重 DRM 的 CENC 过程中配置 AssetDeliveryPolicy 时，提供许可证获取 URL（针对 PlayReady、Widevine 和 FairPlay）。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>如何使用自定义 STS？
可能有多种原因使得客户想要选择使用自定义 STS（安全令牌服务）来提供 JWT 令牌。 以下是其中一些原因：

1. 客户使用的标识提供者 (IDP) 不支持 STS。 在此情况下，可以选择自定义 STS。
2. 客户在集成 STS 与客户的订户计费系统时可能需要更多弹性或更紧密的控制。 例如，MVPD 运营商可能提供多个 OTT 订户套餐，如高级、基本、运动等。运营商可能想要让令牌中的声明与订户套餐匹配，这样，只有正确套餐中的内容可供使用。 在此情况下，自定义 STS 可提供所需的弹性和控制度。

使用自定义 STS 时需要进行两项更改：

1. 为资产配置许可证传送服务时，需要指定自定义 STS 用于验证的安全密钥（以下将详细说明），而不是来自 Azure Active Directory 的当前密钥。
2. 生成 JTW 令牌时，需要指定安全密钥，而不是 Azure Active Directory 中当前 X509 证书的私钥。

有两种类型的安全密钥：

1. 对称密钥：使用同一密钥来生成和验证 JWT 令牌；
2. 非对称密钥：结合使用 X509 证书中的私钥-公钥对，私钥用于加密/生成 JWT 令牌，公钥用于验证令牌。

#### <a name="tech-note"></a>技术说明
如果使用 .NET Framework/C# 作为开发平台，用于非对称安全密钥的 X509 证书的密钥长度必须至少为 2048。 这是 .NET Framework 中 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 类的要求。 否则，将引发以下异常：

IDX10630: 用于签名的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小于 '2048' 位。

## <a name="the-completed-system-and-test"></a>完成的系统和测试
我们会在完成的端到端系统中演练几种方案，让读者在获取登录帐户之前对该行为有个基本印象。

可以在[此处](https://openidconnectweb.azurewebsites.net/)找到播放器 Web 应用程序及其登录凭据。

如果需要“非集成式”方案：托管在 Azure 媒体服务中的视频资产未受保护或受 DRM 保护，但是没有令牌身份验证（对任何提出请求的对象颁发许可证），可以测试它而不用登录（方法是切换到 HTTP，如果视频是通过 HTTP 流式传输的）。

如果需要端到端集成方案：视频资产受 Azure 媒体服务的动态 DRM 保护，令牌身份验证与 JWT 令牌由 Azure AD 生成，则需要登录。

### <a name="user-login"></a>用户登录
为了测试端到端集成 DRM 系统，需要创建或添加一个“帐户”。

什么帐户？

尽管 Azure 最初只允许 Microsoft 帐户用户的访问，但现在它允许来自两个系统的用户的访问。 这种变化是通过以下方式实现的：让所有 Azure 属性信任 Azure AD 执行身份验证，让 Azure AD 对组织用户进行身份验证，并创建一种联合关系，在这种关系中，Azure AD 将信任 Microsoft 帐户使用者标识系统对使用者用户执行身份验证。 因此，Azure AD 能够对“来宾”Microsoft 帐户以及“本机”Azure AD 帐户进行身份验证。

由于 Azure AD 信任 Microsoft 帐户 (MSA) 域，因此可以将以下任何域的任何帐户添加到自定义 Azure AD 租户，并使用该帐户登录：

| **域名** | **域** |
| --- | --- |
| **自定义 Azure AD 租户域** |somename.onmicrosoft.com |
| **企业域** |microsoft.com |
| **Microsoft 帐户 (MSA) 域** |outlook.com、live.com 或 hotmail.com |

可以联系任何作者来创建或添加帐户。

以下是不同域帐户使用的不同登录页的屏幕截图。

**自定义 Azure AD 租户域帐户**：在此方案中，可看到自定义 Azure AD 租户域的自定义登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**采用智能卡的 Microsoft 域帐户**：在此方案中，可看到由 Microsoft 公司 IT 部门自定义的、采用双重身份验证的登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帐户 (MSA)**：在此方案中，可看到使用者的 Microsoft 帐户登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>使用 PlayReady 的加密媒体扩展
在支持 PlayReady 加密媒体扩展 (EME) 的新式浏览器（例如 Windows 8.1 和更高版本上的 IE 11，以及 Windows 10 上的 Microsoft Edge 浏览器）上，PlayReady 是 EME 的基本 DRM。

![使用 EME for PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

出现深色播放器区域是因为 PlayReady 保护阻止对受保护的视频进行屏幕截图。

以下屏幕显示了播放器插件和 MSE/EME 支持。

![使用 EME for PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 上的 Microsoft Edge 和 IE 11 中的 EME 允许支持其 Windows 10 设备上的 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 的调用。 PlayReady SL3000 解锁增强型高级内容（4K、HDR 等）以及新的内容传送模型（增强型内容的早期窗口）流程。

将重心放在 Windows 设备上：PlayReady 是 Windows 设备可用的 HW 中唯一的 DRM（PlayReady SL3000）。 流式处理服务可通过 EME 或 UWP 应用程序来使用 PlayReady，并利用 PlayReady SL3000 来提供比其他 DRM 所能提供更高的影片质量。 一般而言，2K 内容会流经 Chrome 或 Firefox，而 4K 内容流过 Microsoft Edge/IE11 或相同设备上的 UWP 应用程序（取决于服务的设置及实现）。

#### <a name="using-eme-for-widevine"></a>使用 EME for Widevine
在支持 EME/Widevine 的现代浏览器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背后的 DRM。

![使用 EME for Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

请注意，Widevine 不会阻止对受保护的视频进行屏幕截图。

![使用 EME for Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>未获授权的用户
如果用户不是“已获授权用户”组的成员，则通不过“权利检查”，且多重 DRM 许可证服务将拒绝颁发请求的许可证，如下所述。 详细的描述是“许可证获取失败”，这是设计使然。

![未获得授权的用户](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>运行自定义安全令牌服务
对于运行自定义安全令牌服务 (STS) 的方案，JWT 令牌由自定义 STS 使用对称或非对称密钥来颁发。

使用对称密钥的方案（使用 Chrome）：

![运行自定义 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

通过 X509 证书使用非对称密钥的方案（使用 Microsoft 现代浏览器）。

![运行自定义 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

在上述两个方案中，用户身份验证相同 – 通过 Azure AD。 唯一的差别在于，JWT 令牌是由自定义 STS 而不是 Azure AD 颁发。 配置动态 CENC 保护时，许可证传送服务的限制将指定 JWT 令牌的类型是对称还是非对称密钥。

## <a name="summary"></a>摘要
在本文档中，我们讨论了使用多重原生 DRM 的 CENC 以及通过令牌身份验证进行访问控制：它的设计和实现使用了 Azure、Azure 媒体服务和 Azure Media Player。

* 文中提供了一个参考设计，其中包含 DRM/CENC 子系统中的所有必要组件；
* Azure、Azure 媒体服务和 Azure Media Player 的参考实现。
* 同时还讨论到直接涉及设计和实现的某些主题。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
