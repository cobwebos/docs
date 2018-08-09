---
title: 使用 Azure 媒体服务设计带访问控制的内容保护系统 | Microsoft Docs
description: 了解如何为 Microsoft 平滑流式处理客户端移植工具包授权。
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2018
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: c125d5a741331d5c9476da23766057ac0c42cdbf
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493721"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>使用 Azure 媒体服务设计带访问控制的内容保护系统

## <a name="overview"></a>概述

针对 Over-The-Top (OTT) 或在线流解决方案设计和构建数字版权管理 (DRM) 子系统是相当复杂的任务。 运营商/在线视频提供商通常会将此任务外包给专门的 DRM 服务提供商。 本文档旨在陈述 OTT 或在线流解决方案中端到端 DRM 子系统的参考设计和实现。

本文档的目标读者是使用 OTT 或在线流/多屏解决方案的 DRM 子系统的工程师，或对于 DRM 子系统有兴趣的读者。 假设读者熟悉市场上的至少一种 DRM 技术，例如 PlayReady、Widevine、FairPlay 或 Adobe Access。

在 DRM 方面，本文还会介绍使用多重 DRM 的 CENC（通用加密）。 在线流和 OTT 行业中的主要趋势是在各种客户端平台上使用 CENC 与多重原生 DRM。 这是从以前对于各种客户端平台使用单个 DRM 及其客户端 SDK 的趋势变化而来的。 使用 CENC 与多重原生 DRM 时，PlayReady 和 Widevine 都按照[通用加密 (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 规范加密。

使用 CENC 与多重 DRM 的好处如下：

* 降低加密成本，因为对于不同平台使用其原生 DRM 进行单一加密处理。
* 降低管理加密资产的成本，因为只需要一份加密资产。
* 消除 DRM 客户端许可成本，因为原生 DRM 客户端在其原生平台上通常是免费提供的。

Microsoft 已成为 DASH 和 CENC 与其他一些主要行业播放器的积极推动者。 Azure 媒体服务为 DASH 和 CENC 提供支持。 有关最新通告，请参阅以下博客：

*  [特此宣布已在 Azure 媒体服务中推出 Google Widevine 许可证传送服务](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure 媒体服务已添加 Google Widevine 打包功能用于传送多重 DRM 流](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>本文的目标

本文的目标是：

* 提供使用 CENC 与多重 DRM 的 DRM 子系统的参考设计。
* 提供 Azure/媒体服务平台上的参考实现。
* 讨论一些设计和实现主题。

在本文中，“多重 DRM”一词涵盖以下产品：

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

下表汇总了每个 DRM 支持的原生平台/原生应用和浏览器。

| **客户端平台** | **原生 DRM 支持** | **浏览器/应用** | **流格式** |
| --- | --- | --- | --- |
| **智能电视、操作员 STB、OTT STB** |主要为 PlayReady，和/或 Widevine，和/或其他 DRM |Linux、Opera、WebKit 及其他 |各种格式 |
| **Windows 10 设备（Windows 电脑、Windows 平板电脑、Windows Phone、Xbox）** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>通用 Windows 平台 |DASH（对于 HLS，不支持 PlayReady）<br/><br/>DASH、平滑流式处理（对于 HLS，不支持 PlayReady） |
| **Android 设备（手机、平板电脑、电视）** |Widevine |Chrome/EME |DASH、HLS |
| **iOS（iPhone、iPad）、OS X 客户端和 Apple 电视** |FairPlay |Safari 8+/EME |HLS |

就目前每种 DRM 的部署状态而言，服务通常需要实现两到三个 DRM，以确保能以最佳方式处理所有类型的终结点。

必须在服务逻辑复杂性与客户端复杂性之间做出取舍，以便各种不同客户端上的用户体验能够达到特定的程度。

做出选择时，请记住以下几点：

* PlayReady 原生实现在每种 Windows 设备和部分 Android 设备上，且可通过软件 SDK 在几乎所有平台上实现。
* Widevine 原生实现在每种 Android 设备、Chrome 和部分其他设备上。
* FairPlay 只可在 iOS 和 Mac OS 客户端中使用，或通过 iTunes 使用。

典型多重 DRM 的两个选项是：

* PlayReady 和 Widevine
* PlayReady、Widevine 和 FairPlay

## <a name="a-reference-design"></a>参考设计
本部分探讨一种参考设计，这种设计与用于实现它的技术无关。

DRM 子系统可能包含以下组件：

* 密钥管理
* DRM 打包
* DRM 许可证传送
* 权利检查
* 身份验证/授权
* 播放器
* 来源/内容分发网络 (CDN)

下图演示了 DRM 子系统中组件之间的高级交互：

![使用 CENC 的 DRM 子系统](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

设计中包含三个基本层：

* 后端办公系统层（黑色表示），不对外部公开。
* 外围网络层（蓝色表示），包含所有面向公众的终结点。
* 公共 Internet 层（浅蓝色表示），包含具有跨公共 Internet 流量的 CDN 和播放器。

无论是静态加密还是动态加密，也都应该有一个用于控制 DRM 保护的内容管理工具。 DRM 加密的输入包括：

* MBR 视频内容
* 内容密钥
* 许可证获取 URL

在播放期间，高级流程是：

* 对用户进行身份验证。
* 为用户创建授权令牌。
* 将 DRM 保护的内容（清单）下载到播放器。
* 播放器将许可证获取请求和密钥 ID 与授权令牌提交到许可证服务器。

以下部分介绍密钥管理的设计。

| **ContentKey-to-asset** | **方案** |
| --- | --- |
| 一对一 |最简单的情况。 它提供最精细的控制。 但是，此排列方式通常产生最高的许可证传送成本。 每个受保护的资产需要至少一个许可证请求。 |
| 一对多 |可以对多个资产使用相同的内容密钥。 例如，对于如流派或流派子集（或电影基因）的逻辑组中的所有资产，可以使用单个内容密钥。 |
| 多对一 |每个资产需要多个内容密钥。 <br/><br/>例如，如果需要针对 MPEG-DASH 应用具有多重 DRM 的动态 CENC 保护，且针对 HLS 应用动态 AES-128 加密，则需要两个单独的内容密钥。 每个内容密钥有自身的 ContentKeyType。 （对于用于动态 CENC 保护的内容密钥，应使用 ContentKeyType.CommonEncryption。 对于用于动态 AES-128 加密的内容密钥，应使用 ContentKeyType.EnvelopeEncryption。）<br/><br/>再举一个示例，在 DASH 内容的 CENC 保护中，从理论上讲，可以使用一个内容密钥来保护视频流，以及使用另一个内容密钥来保护音频流。 |
| 多对多 |结合上述两种方案。 对于相同数据“组”中的每个多重资产，使用一组内容密钥。 |

另一个需要考虑的重要因素是持久性和非持续久许可证的使用。

为何这些考虑因素非常重要？

如果针对许可证传送使用公有云，持久性和非持久性许可证对于许可证传送成本有直接影响。 下面演示了两种不同的设计方案：

* 每月订阅：使用永久性许可证，以及一对多内容密钥到资产映射。 例如，对于所有儿童影片，使用单个内容密钥进行加密。 在这种情况下：

    所有儿童影片/设备的许可证总数 = 1

* 每月订阅：在内容密钥与资产之间使用非永久性许可证以及一对一映射。 在这种情况下：

    所有儿童影片/设备的许可证总数 = [观看影片数] x [会话数]

两种不同的设计会导致大不相同的许可证请求模式。 如果许可证传送服务由 Azure 媒体服务等公有云提供，则不同的模式会造成许可证传送成本不同。

## <a name="map-design-to-technology-for-implementation"></a>将设计映射到技术以供实现
接下来，通过指定用于每个构建基块的技术，将常规设计映射到 Azure/媒体服务平台上的技术。

下表显示了映射。

| **构建基块** | **技术** |
| --- | --- |
| **播放器** |[Azure 媒体播放器](https://azure.microsoft.com/services/media-services/media-player/) |
| **标识提供者 (IDP)** |Azure Active Directory (Azure AD) |
| **安全令牌服务 (STS)** |Azure AD |
| **DRM 保护工作流** |媒体服务动态保护 |
| **DRM 许可证传送** |* 媒体服务许可证传送（PlayReady、Widevine、FairPlay） <br/>* Axinom 许可证服务器 <br/>* 自定义 PlayReady 许可证服务器 |
| **源** |媒体服务流式处理终结点 |
| **密钥管理** |不需要参考实现 |
| **内容管理** |一个 C# 控制台应用程序 |

换而言之，IDP 和 STS 都将与 Azure AD 配合使用。 [Azure 媒体播放器 API](http://amp.azure.net/libs/amp/latest/docs/) 用于播放器。 媒体服务和媒体播放器都支持具有多重 DRM 的 DASH 和 CENC。

下图显示了上述技术映射的整体结构与流程。

![媒体服务中的 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

为了设置动态 CENC 加密，内容管理工具将使用以下输入：

* 公开的内容
* 来自密钥生成/管理的内容密钥
* 许可证获取 URL
* Azure AD 中的信息列表

内容管理工具的输出如下：

* ContentKeyAuthorizationPolicy，包含有关许可证传送如何验证 JSON Web 令牌 (JWT) 令牌和 DRM 许可证规范的规范。
* AssetDeliveryPolicy，包含有关流格式、DRM 保护和许可证获取 URL 的规范。

在运行时，流如下：

* 对用户进行身份验证后，生成 JWT。
* JWT 中包含的声明之一是 groups 声明，其中包含 EntitledUserGroup 的组对象 ID。 此声明用于传递权利检查。
* 播放器下载 CENC 保护内容的客户端清单，并可以识别以下信息：
   * 密钥 ID。
   * 内容受 CENC 保护。
   * 许可证获取 URL。
* 播放器根据支持的浏览器/DRM 发出许可证获取请求。 在许可证获取请求中，还会提交密钥 ID 和 JWT。 许可证传送服务会验证 JWT，在颁发所需的许可证之前包含声明。

## <a name="implementation"></a>实现
### <a name="implementation-procedures"></a>实现过程
实现包括下列步骤：

1. 准备测试资产。 将测试视频编码/打包为媒体服务中的多比特率分段 MP4。 此资产不受 DRM 保护。 DRM 保护稍后由动态保护完成。

2. 创建密钥 ID 和内容密钥（可以选择从密钥种子中获取）。 在此情况下，不需要密钥管理系统，因为只需要对一些测试资产使用单个密钥 ID 和内容密钥。

3. 使用媒体服务 API 来对测试资产配置多重 DRM 许可证传送服务。 如果使用公司或公司供应商的自定义许可证服务器，而不是媒体服务中的许可证服务，则可以跳过此步骤。 在配置许可证传送的步骤中，可以指定许可证获取 URL。 需要使用媒体服务 API 指定一些详细配置，例如不同 DRM 许可证服务的许可证策略限制和许可证响应模板。 目前，Azure 门户尚未提供此配置所需的 UI。 有关 API 级别信息和示例代码，请参阅[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-playready-widevine.md)。

4. 使用媒体服务 API 针对测试资产配置资产传送策略。 有关 API 级别信息和示例代码，请参阅[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-playready-widevine.md)。

5. 在 Azure 中创建和配置 Azure AD 租户。

6. 在 Azure AD 租户中创建一些用户帐户和组。 至少创建一个“已获授权用户”组，并将一个用户添加到此组。 在许可证获取过程中，此组中的用户可以通过权利检查。 此组中的用户无法通过身份验证检查，且无法获取许可证。 在 Azure AD 颁发的 JWT 中的 groups 声明中必须使用此“已获授权用户”组的成员身份。 配置多重 DRM 许可证传送服务时，可以在步骤中指定此声明要求。

7. 创建 ASP.NET MVC 应用，用于托管视频播放器。 此 ASP.NET 应用根据 Azure AD 租户受到用户身份验证的保护。 对用户进行身份验证后，适当的声明将包含在获取的访问令牌中。 我们建议在此步骤中使用 OpenID Connect API。 安装以下 NuGet 包：

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. 使用 [Azure 媒体播放器 API](http://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) 指定要在不同的 DRM 平台上使用哪种 DRM 技术。

9. 下表显示了测试矩阵。

    | **DRM** | **浏览器** | **已获授权用户的结果** | **未获授权用户的结果** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10 上的 Microsoft Edge 或 Internet Explorer 11 |成功 |失败 |
    | **Widevine** |Chrome、Firefox、Opera |成功 |失败 |
    | **FairPlay** |macOS 上的 Safari      |成功 |失败 |
    | **AES-128** |大多数新式浏览器  |成功 |失败 |

有关针对 ASP.NET MVC 播放器应用配置 Azure AD 的信息，请参阅[将基于 Azure 媒体服务 OWIN MVC 的应用与 Azure Active Directory 相集成，并基于 JWT 声明限制内容密钥传送](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

有关详细信息，请参阅 [Azure 媒体服务和动态加密中的 JWT 令牌身份验证](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。  

有关 Azure AD 的信息：

* 可以在 [Azure Active Directory 开发人员指南](../../active-directory/develop/azure-ad-developers-guide.md)中找到面向开发人员的信息。
* 可以在[管理 Azure AD 租户目录](../../active-directory/fundamentals/active-directory-administer.md)中找到面向管理员的信息。

### <a name="some-issues-in-implementation"></a>实现中的一些问题
请使用以下故障排除信息来帮助解决实现问题。

* 颁发者 URL 必须以“/”结尾。 受众必须是播放器应用程序客户端 ID。 此外，必须在颁发者 URL 的末尾添加“/”。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    在 [JWT 解码器](http://jwt.calebb.net/)中，可以看到 JWT 中所示的 **aud** 和 **iss**：

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* 在应用程序的“配置”选项卡上，将权限添加到 Azure AD 中的应用程序。 对于本地版本和已部署的版本，需要提供每个应用程序的权限。

    ![权限](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* 设置动态 CENC 保护时，请使用正确的颁发者。

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    以下代码无法运行：

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID 是 Azure AD 租户 ID。 可以在 Azure 门户上的“终结点”弹出菜单中找到该 GUID。

* 授予组成员资格声明权限。 确保 Azure AD 应用程序清单文件中包含以下内容： 

    "groupMembershipClaims": "All"（默认值为 null）

* 创建限制要求时，请设置适当的 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    由于除了 JWT (ACS) 以外，还添加了对 SWT (Azure AD) 的支持，因此默认 TokenType 是 TokenType.JWT。 如果使用 SWT/ACS，则必须将令牌设置为 TokenType.SWT。

## <a name="additional-topics-for-implementation"></a>有关实现的其他主题
本部分讨论设计和实现中的一些其他主题。

### <a name="http-or-https"></a>使用 HTTP 还是 HTTPS？
构建的 ASP.NET MVC 播放器应用程序必须支持以下功能：

* 通过 Azure AD 进行用户身份验证（使用 HTTPS）。
* 客户端与 Azure AD 之间的 JWT 交换（使用 HTTPS）。
* 客户端的 DRM 许可证获取，如果许可证传送由媒体服务提供（必须使用 HTTPS）。 PlayReady 产品套件不会针对许可证传送强制要求使用 HTTPS。 如果 PlayReady 许可证服务器位于媒体服务以外的地方，则可以使用 HTTP 或 HTTPS。

最佳做法是让 ASP.NET 播放器应用程序使用 HTTPS，使媒体播放器位于使用 HTTPS 的页面上。 不过，最好是使用 HTTP 进行流式传输，因此需要考虑混合内容问题。

* 浏览器不允许混合内容。 但是 Silverlight 等插件和适用于平滑流与 DASH 的 OSMF 插件允许混合内容。 混合内容存在安全隐患 - 因为能够插入恶意 JavaScript 威胁，从而使客户数据处于风险之中。 默认情况下，浏览器会阻止此类内容。 唯一的解决方法是在服务器（来源）端允许所有域（不管是 HTTPS 还是 HTTP）。 这可能也不是个好主意。
* 避免混合内容。 播放器应用程序和媒体播放器应使用 HTTP 或 HTTPS。 播放混合内容时，silverlightSS 技术需要清除混合内容警告。 flashSS 技术在没有混合内容警告的情况下处理混合内容。
* 如果流式处理终结点是在 2014 年 8 月之前创建的，则它不支持 HTTPS。 在此情况下，请针对 HTTPS 创建并使用新的流式处理终结点。

在参考实现中，对于 DRM 保护内容，应用程序和流都使用 HTTPS。 对于打开的内容，播放器不需要身份验证或许可证，因此可以使用 HTTP 或 HTTPS。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 签名密钥滚动更新
密钥滚动更新是需要在实现中考虑的要点。 如果忽略它，已完成的系统最终会在最多六周之后完全停止运行。

Azure AD 使用行业标准，通过 Azure AD 在本身与应用程序之间建立信任。 具体而言，Azure AD 使用签名密钥，该密钥由公钥和私钥对组成。 当 Azure AD 创建包含用户相关信息的安全令牌时，Azure AD 将使用私钥对此令牌进行签名，然后将令牌发回给应用程序。 若要验证该令牌是否有效且来自 Azure AD，应用程序验证令牌的签名。 应用程序可以使用由 Azure AD 公开且包含在租户联合元数据文档中的公钥。 此公钥以及衍生它的签名密钥是由 Azure AD 中所有租户使用的同一个密钥。

有关 Azure AD 密钥滚动更新的详细信息，请参阅[有关 Azure AD 中签名密钥滚动更新的重要信息](../../active-directory/active-directory-signing-key-rollover.md)。

在[公钥-私钥对](https://login.microsoftonline.com/common/discovery/keys/)之间：

* Azure AD 使用私钥生成 JWT。
* 应用程序（例如媒体服务中的 DRM 许可证传送服务）使用公钥验证 JWT。

出于安全目的，Azure AD 将定期轮换证书（每隔六周）。 在发生安全违规时，密钥滚动更新可随时发生。 因此，媒体服务中的许可证传送服务需要在 Azure AD 轮换密钥对时更新公钥。 否则媒体服务中的令牌身份验证会失败，并且不颁发任何许可证。

若要设置此服务，可以在配置 DRM 许可证传送服务时设置 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument。

下面是 JWT 流：

* Azure AD 使用当前私钥为已经过身份验证的用户颁发 JWT。
* 当播放器看到使用多重 DRM 的 CENC 保护的内容时，会先找到 Azure AD 颁发的 JWT。
* 播放器将包含 JWT 的许可证获取请求发送到媒体服务中的许可证传送服务。
* 媒体服务中的许可证传送服务使用来自 Azure AD 的当前/有效公钥来验证 JWT，并颁发许可证。

DRM 许可证传送服务始终会检查来自 Azure AD 的当前/有效公钥。 Azure AD 提供的公钥是用于验证 Azure AD 所颁发 JWT 的密钥。

如果 Azure AD 生成 JWT 之后、播放器将 JWT 发送到媒体服务中的 DRM 许可证传送服务以进行验证之前发生密钥滚动更新，会怎么样？

由于密钥可能在任何时间滚动更新，联合元数据文档中始终有多个有效公钥可用。 媒体服务许可证传送可以使用文档中指定的任何密钥。 因为一个密钥可能很快就被轮换为另一个密钥。

### <a name="where-is-the-access-token"></a>访问令牌位于何处？
如果在[带有 OAuth 2.0 客户端凭据授予的应用程序标识](../../active-directory/develop/authentication-scenarios.md#web-application-to-web-api)下，Web 应用调用 API 应用，身份验证流如下：

* 用户在 Web 应用程序中登录到 Azure AD。 有关详细信息，请参阅 [Web 浏览器到 Web 应用程序](../../active-directory/develop/authentication-scenarios.md#web-browser-to-web-application)。
* Azure AD 许可证终结点使用授权代码将用户代理重定向回到客户端应用程序。 用户代理将授权代码返回给客户端应用程序的重定向 URI。
* Web 应用程序需要获取访问令牌，以便通过 Web API 进行身份验证并检索所需的资源。 它向 Azure AD 的令牌终结点发出一个请求，在其中提供凭据、客户端 ID 以及 Web API 的应用程序 ID URI。 它将提供授权代码来证明已获得用户许可。
* Azure AD 对应用程序进行身份验证并返回用来调用 Web API 的 JWT 访问令牌。
* 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的“Authorization”标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 进行验证。 如果验证成功，则返回所需的资源。

在此应用程序标识流中，Web API 相信 Web 应用程序已对用户进行了身份验证。 因此，此模式称为受信任的子系统。 [授权流示意图](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code)描绘了授权代码授予流的工作原理。

在具有令牌限制的许可证获取中，遵循相同的受信任子系统模式。 媒体服务中的许可证传送服务是 Web API 资源，即 Web 应用程序需要访问的“后端资源”。 那么，访问令牌位于何处？

访问令牌是是从 Azure AD 获取的。 成功完成用户身份验证后，将返回授权代码。 然后使用授权代码连同客户端 ID 和应用密钥来交换访问令牌。 访问令牌用于访问指向或代表媒体服务许可证传送服务的“指针”应用程序。

若要在 Azure AD 中注册并配置指针应用，请执行以下步骤：

1. 在 Azure AD 租户中：

   * 添加应用程序（资源）与登录 URL https://[资源名称].azurewebsites.net/。 
   * 添加应用 ID 与 URL https://[AAD 租户名称].onmicrosoft.com/[资源名称]。

2. 为资源应用添加新的密钥。

3. 更新应用程序清单文件，使 groupMembershipClaims 属性具有值 "groupMembershipClaims": "All"。

4. 在指向播放器 Web 应用的 Azure AD 应用中，在“对其他应用程序的权限”部分添加步骤 1 中添加的资源应用。 在“委派权限”下面选择“访问 [资源名称]”。 此选项可授予 Web 应用创建访问令牌的权限以访问资源应用。 如果使用 Visual Studio 和 Azure Web 应用进行开发，请对本地版本和已部署版本的 Web 应用执行此操作。

Azure AD 颁发的 JWT 是用于访问此指针资源的访问令牌。

### <a name="what-about-live-streaming"></a>如何使用实时流？
前面的讨论内容将重点放在按需资产上。 如何使用实时流？

可以使用完全相同的设计和实现来保护媒体服务中的实时传送视频流，方法是将与节目关联的资产视为 VOD 资产。

具体而言，要在媒体服务中执行实时传送视频流，需要创建频道，并在频道下面创建节目。 若要创建节目，需要创建资产并在其中包含节目的实时存档。 要使用带有多重 DRM 的 CENC 来保护实时内容，可在启动节目之前将相同设置/处理应用到资产，就如同它是 VOD 资产一样。

### <a name="what-about-license-servers-outside-media-services"></a>如何使用媒体服务外部的许可证服务器？
通常，客户可能已在他们自己的数据中心或由 DRM 服务提供商托管的位置投资了许可证服务器场。 使用媒体服务内容保护，可以在混合模式下操作。 可以在媒体服务中托管和动态保护内容，而 DRM 许可证由 Azure 媒体服务外部的服务器传送。 在此情况下，请注意以下变更：

* STS 需要颁发被许可证服务器场认可和验证的令牌。 例如，Axinom 提供的 Widevine 许可证服务器需要特定的 JWT，其中包含权利消息。 因此，需要通过一个 STS 颁发此类 JWT。 有关此类实现的信息，请转到 [Azure 文档中心](https://azure.microsoft.com/documentation/)并参阅[使用 Axinom 将 Widevine 许可证传送到 Azure 媒体服务](media-services-axinom-integration.md)。
* 不再需要在媒体服务中配置许可证传送服务 (ContentKeyAuthorizationPolicy)。 需要在设置使用多重 DRM 的 CENC 过程中配置 AssetDeliveryPolicy 时，提供许可证获取 URL（针对 PlayReady、Widevine 和 FairPlay）。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>如何使用自定义 STS？
客户可能想要使用自定义 STS 来提供 JWT。 原因包括：

* 客户使用的 IDP 不支持 STS。 在此情况下，可以选择自定义 STS。
* 客户在集成 STS 与客户的订户计费系统时可能需要更多弹性或更紧密的控制。 例如，MVPD 运营商可能提供多个 OTT 订户套餐，如高级、基本和运动。 运营商可能想要让令牌中的声明与订户套餐匹配，这样，只有特定套餐中的内容可供使用。 在此情况下，自定义 STS 可提供所需的弹性和控制度。

使用自定义 STS 时，必须进行两项更改：

* 为资产配置许可证传送服务时，需要指定自定义 STS 用于验证的安全密钥，而不是来自 Azure AD 的当前密钥。 （下面提供了详细信息。） 
* 生成 JTW 令牌时，需要指定安全密钥，而不是 Azure AD 中当前 X509 证书的私钥。

有两种类型的安全密钥：

* 对称密钥：使用同一密钥来生成和验证 JWT。
* 非对称密钥：使用 X509 证书中的私钥-公钥对，私钥用于加密/生成 JWT，公钥用于验证令牌。

> [!NOTE]
> 如果使用 .NET Framework/C# 作为开发平台，用于非对称安全密钥的 X509 证书的密钥长度必须至少为 2048。 这是 .NET Framework 中 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 类的要求。 否则，将引发以下异常：

> IDX10630: 用于签名的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小于 '2048' 位。

## <a name="the-completed-system-and-test"></a>完成的系统和测试
本部分逐步讲解如何在完成的端到端系统中完成以下方案，让读者在获取登录帐户之前对该行为有个基本印象。

* 如需非集成式方案：

    * 托管在媒体服务中的视频资产未受保护或受 DRM 保护，但是没有令牌身份验证（对任何提出请求的对象颁发许可证），可以测试它而不用登录。 如果视频是通过 HTTP 流式传输的，请切换到 HTTP。

* 如果需要端到端集成方案：

    * 视频资产在媒体服务中受动态 DRM 的保护，并且令牌身份验证与 JWT 由 Azure AD 生成，则需要登录。

对于播放器 Web 应用程序及其登录，请参阅[此网站](https://openidconnectweb.azurewebsites.net/)。

### <a name="user-sign-in"></a>用户登录
为了测试端到端集成 DRM 系统，需要创建或添加一个帐户。

什么帐户？

尽管 Azure 最初只允许 Microsoft 帐户用户的访问，但现在它允许来自两个系统的用户的访问。 所有 Azure 属性现在都信任 Azure AD 执行身份验证，Azure AD 将对组织用户进行身份验证。 已创建一种联合关系，在这种关系中，Azure AD 信任 Microsoft 帐户使用者标识系统对使用者用户执行身份验证。 因此，Azure AD 能够对来宾 Microsoft 帐户以及本机 Azure AD 帐户进行身份验证。

由于 Azure AD 信任 Microsoft 帐户域，因此可以将以下任何域的任何帐户添加到自定义 Azure AD 租户，并使用该帐户登录：

| **域名** | **域** |
| --- | --- |
| **自定义 Azure AD 租户域** |somename.onmicrosoft.com |
| **企业域** |microsoft.com |
| **Microsoft 帐户域** |outlook.com、live.com 或 hotmail.com |

可以联系任何作者来创建或添加帐户。

以下屏幕截图显示了不同域帐户使用的不同登录页：

**自定义 Azure AD 租户域帐户**：自定义 Azure AD 租户域的自定义登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**采用智能卡的 Microsoft 域帐户**：由 Microsoft 公司 IT 部门自定义的、采用双重身份验证的登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帐户**：使用者的 Microsoft 帐户登录页。

![自定义 Azure AD 租户域帐户](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>使用 PlayReady 的加密媒体扩展
在支持 PlayReady 加密媒体扩展 (EME) 的新式浏览器（例如 Windows 8.1 和更高版本上的 Internet Explorer 11，以及 Windows 10 上的 Microsoft Edge 浏览器）上，PlayReady 是 EME 的基本 DRM。

![使用 EME for PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

出现深色播放器区域是因为 PlayReady 保护阻止对受保护的视频进行屏幕截图。

以下屏幕截图显示了播放器插件和 Microsoft Security Essentials (MSE)/EME 支持。

![PlayReady 的播放器插件](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 上的 Microsoft Edge 和 Internet Explorer 11 中的 EME 允许在支持 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 的 Windows 10 设备上调用该技术。 PlayReady SL3000 解锁增强型高级内容（4K、HDR）以及新的内容传送模型（增强型内容）流程。

将重心放在 Windows 设备上：PlayReady 是 Windows 设备可用的硬件中唯一的 DRM（PlayReady SL3000）。 流式处理服务可通过 EME 或通用 Windows 平台应用程序来使用 PlayReady，并利用 PlayReady SL3000 来提供比其他 DRM 所能提供更高的视频质量。 一般而言，2K 及更低分辨内容会流经 Chrome 或 Firefox，而 4K 及更低分辨内容流过 Microsoft Edge/Internet Explorer 11 或相同设备上的通用 Windows 平台应用程序。 流量取决于服务设置和实现。

#### <a name="use-eme-for-widevine"></a>使用 EME for Widevine
在支持 EME/Widevine 的现代浏览器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背后的 DRM。

![使用 EME for Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine 不会阻止对受保护的视频进行屏幕截图。

![Widevine 的播放器插件](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>未获授权的用户
如果用户不是“已获授权用户”组的成员，则通不过权利检查。 多重 DRM 许可证服务将拒绝颁发请求的许可证，如下所述。 详细的说明是“许可证获取失败”，这是设计使然。

![未获授权的用户](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>运行自定义安全令牌服务
如果运行自定义 STS，JWT 由自定义 STS 使用对称或非对称密钥来颁发。

以下屏幕截图显示了使用对称密钥的方案（使用 Chrome）：

![自定义 STS 和对称密钥](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

以下屏幕截图显示了通过 X509 证书使用非对称密钥的方案（使用 Microsoft 新式浏览器）：

![自定义 STS 和非对称密钥](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

在上述两个方案中，用户身份验证相同。 身份验证是通过 Azure AD 发生的。 唯一的差别在于，JWT 由自定义 STS 而不是 Azure AD 颁发。 配置动态 CENC 保护时，许可证传送服务的限制将指定 JWT 的类型是对称还是非对称密钥。

## <a name="summary"></a>摘要
本文档讨论了使用多重原生 DRM 的 CENC 以及通过令牌身份验证进行访问控制：它的设计和实现使用了 Azure、媒体服务和媒体播放器。

* 文中提供了一个参考设计，其中包含 DRM/CENC 子系统中的所有必要组件。
* 已提供 Azure、媒体服务和媒体播放器的参考实现。
* 同时还讨论了直接涉及到设计和实现的某些主题。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
