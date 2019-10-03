---
title: 设计带访问控制的多 DRM 内容保护系统 - Azure 媒体服务 | Microsoft 文档
description: 了解如何为 Microsoft 平滑流式处理客户端移植工具包授权。
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: ffbf53c0bb0aaf2832afecc2d0df935f04eeff19
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310328"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>设计带访问控制的多 DRM 内容保护系统 

针对 over-the-top (OTT) 或在线流解决方案设计和构建数字版权管理 (DRM) 子系统是相当复杂的任务。 运营商/在线视频提供商通常会将此任务外包给专门的 DRM 服务提供商。 本文档旨在陈述 OTT 或在线流解决方案中端到端 DRM 子系统的参考设计和参考实现。

本文档的目标读者是使用 OTT 或在线流/多屏解决方案的 DRM 子系统的工程师，或对于 DRM 子系统有兴趣的读者。 假设读者熟悉市场上的至少一种 DRM 技术，例如 PlayReady、Widevine、FairPlay 或 Adobe Access。

在本次讨论中，借助多 DRM，我们介绍 Azure 媒体服务支持的 3 个 DRM：PlayReady 和 Widevine 的通用加密 (CENC)、FairPlay 以及 AES-128 明文密钥加密。 在线流和 OTT 行业中的主要趋势是在各种客户端平台上使用原生 DRM。 这是从以前对于各种客户端平台使用单个 DRM 及其客户端 SDK 的趋势变化而来的。 使用 CENC 与多重原生 DRM 时，PlayReady 和 Widevine 都按照[通用加密 (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 规范加密。

使用原生多重 DRM 保护内容的好处包括：

* 降低加密成本，因为对于不同平台使用其原生 DRM 进行单一加密处理。
* 降低管理资产的成本，因为只需要一份资产。
* 消除 DRM 客户端许可成本，因为原生 DRM 客户端在其原生平台上通常是免费提供的。

### <a name="goals-of-the-article"></a>本文的目标

本文的目标是：

* 提供使用全部三个 DRM 的 DRM 子系统的参考设计（CENC 用于 DASH，FairPlay 用于 HLS，PlayReady 用于实现平滑流式处理）。
* 提供 Azure 和 Azure 媒体服务平台上的参考实现。
* 讨论一些设计和实现主题。

下表总结了不同平台上的原生 DRM 支持和不同浏览器中的 EME 支持。

| **客户端平台** | **原生 DRM** | **EME** |
| --- | --- | --- |
| **智能电视、STB** | PlayReady、Widevine 和/或其他 | 适用于 PlayReady 和/或 Widevine 的嵌入式浏览器/EME|
| **Windows 10** | PlayReady | 适用于 PlayReady 的 Microsoft Edge/IE11|
| **Android 设备（手机、平板电脑、电视）** |Widevine |适用于 Widevine 的 Chrome |
| **iOS** | FairPlay | 适用于 FairPlay 的 Safari（自 iOS 11.2 起） |
| **macOS** | FairPlay | 适用于 FairPlay 的 Safari（自 Mac OS X 10.11 + El Capitan 版 Safari 9+ 起）|
| **tvOS** | FairPlay | |

就目前每种 DRM 的部署状态而言，服务通常需要实现两到三个 DRM，以确保能以最佳方式处理所有类型的终结点。

必须在服务逻辑复杂性与客户端复杂性之间做出取舍，以便各种不同客户端上的用户体验能够达到特定的程度。

做出选择时，请记住以下几点：

* PlayReady 原生实现在每种 Windows 设备和部分 Android 设备上，且可通过软件 SDK 在几乎所有平台上实现。
* Widevine 原生实现在每种 Android 设备、Chrome 和部分其他设备上。 此外，通过 DASH，Widevine 也在 Firefox 和 Opera 浏览器中受支持。
* FairPlay 适用于 iOS、macOS 和 tvOS。


## <a name="a-reference-design"></a>参考设计
本部分探讨一种参考设计，这种设计与用于实现它的技术无关。

DRM 子系统可能包含以下组件：

* 密钥管理
* DRM 加密打包
* DRM 许可证传送
* 权利检查/访问控制
* 用户身份验证/授权
* 播放器应用
* 来源/内容分发网络 (CDN)

下图演示了 DRM 子系统中组件之间的高级交互：

![使用 CENC 的 DRM 子系统](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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

* 月度订阅：使用永久性许可证，以及一对多内容密钥到资产映射。 例如，对于所有儿童影片，使用单个内容密钥进行加密。 在这种情况下：

    所有儿童影片/设备的许可证总数 = 1

* 月度订阅：在内容密钥与资产之间使用非永久性许可证以及一对一映射。 在这种情况下：

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
| **DRM 保护工作流** |Azure 媒体服务动态保护 |
| **DRM 许可证传送** |* 媒体服务许可证传送（PlayReady、Widevine、FairPlay） <br/>* Axinom 许可证服务器 <br/>* 自定义 PlayReady 许可证服务器 |
| **源** |Azure 媒体服务流式处理终结点 |
| **密钥管理** |不需要参考实现 |
| **内容管理** |一个 C# 控制台应用程序 |

换言之，IDP 和 STS 都由 Azure AD 提供。 [Azure 媒体播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 用于播放器。 Azure 媒体服务和 Azure Media Player 通过 DASH 支持 CENC，通过 HLS 支持 FairPlay、通过平滑流式处理支持 PlayReady，并且可对 DASH、HLS 和平滑流式处理进行 AES-128 加密。

下图显示了上述技术映射的整体结构与流程。

![媒体服务中的 CENC](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

为了设置 DRM 内容加密，内容管理工具使用以下输入：

* 公开的内容
* 来自密钥管理的内容密钥
* 许可证获取 URL
* Azure AD 中的信息列表，例如受众、证书颁发者和令牌声明

内容管理工具的输出如下：

* ContentKeyPolicy 描述用于所用的每种 DRM 的 DRM 许可证模板；
* ContentKeyPolicyRestriction 描述颁发 DRM 许可证前的访问控制
* Streamingpolicy 描述用于流式处理的各种 DRM（加密模式、流式处理协议、容器格式）的组合
* StreamingLocator 描述用于加密和流式处理 URL 的内容密钥/IV 

在运行时，流如下：

* 对用户进行身份验证后，生成 JWT。
* JWT 中包含的声明之一是 groups 声明，其中包含 EntitledUserGroup 的组对象 ID。 此声明用于传递权利检查。
* 播放器下载 CENC 保护内容的客户端清单，并可以识别以下信息：
   * 密钥 ID。
   * 内容受 DRM 保护。
   * 许可证获取 URL。
* 播放器根据支持的浏览器/DRM 发出许可证获取请求。 在许可证获取请求中，还会提交密钥 ID 和 JWT。 许可证传送服务会验证 JWT，在颁发所需的许可证之前包含声明。

## <a name="implementation"></a>实现
### <a name="implementation-procedures"></a>实现过程
实现包括下列步骤：

1. 准备测试资产。 将测试视频编码/打包为媒体服务中的多比特率分段 MP4。 此资产不受 DRM 保护。  DRM 保护稍后由动态保护完成。

2. 创建密钥 ID 和内容密钥（可以选择从密钥种子中获取）。 在此情况下，不需要密钥管理系统，因为只需要对一些测试资产使用单个密钥 ID 和内容密钥。

3. 使用媒体服务 API 来对测试资产配置多重 DRM 许可证传送服务。 如果使用公司或公司供应商的自定义许可证服务器，而不是媒体服务中的许可证服务，则可以跳过此步骤。 在配置许可证传送的步骤中，可以指定许可证获取 URL。 需要使用媒体服务 API 指定一些详细配置，例如不同 DRM 许可证服务的许可证策略限制和许可证响应模板。 目前，Azure 门户尚未提供此配置所需的 UI。 有关 API 级别信息和示例代码，请参阅[使用 PlayReady 和/或 Widevine 动态通用加密](protect-with-drm.md)。

4. 使用媒体服务 API 针对测试资产配置资产传送策略。 有关 API 级别信息和示例代码，请参阅[使用 PlayReady 和/或 Widevine 动态通用加密](protect-with-drm.md)。

5. 在 Azure 中创建和配置 Azure AD 租户。

6. 在 Azure AD 租户中创建一些用户帐户和组。 至少创建一个“已获授权用户”组，并将一个用户添加到此组。 在许可证获取过程中，此组中的用户可以通过权利检查。 此组中的用户无法通过身份验证检查，且无法获取许可证。 在 Azure AD 颁发的 JWT 中的 groups 声明中必须使用此“已获授权用户”组的成员身份。 配置多重 DRM 许可证传送服务时，可以在步骤中指定此声明要求。

7. 创建 ASP.NET MVC 应用，用于托管视频播放器。 此 ASP.NET 应用根据 Azure AD 租户受到用户身份验证的保护。 对用户进行身份验证后，适当的声明将包含在获取的访问令牌中。 我们建议在此步骤中使用 OpenID Connect API。 安装以下 NuGet 包：

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. 使用 [Azure 媒体播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 指定要在不同的 DRM 平台上使用哪种 DRM 技术。

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

* 可以在 [Azure Active Directory 开发人员指南](../../active-directory/develop/v1-overview.md)中找到面向开发人员的信息。
* 可以在[管理 Azure AD 租户目录](../../active-directory/fundamentals/active-directory-administer.md)中找到面向管理员的信息。

### <a name="some-issues-in-implementation"></a>实现中的一些问题

请使用以下故障排除信息来帮助解决实现问题。

* 颁发者 URL 必须以“/”结尾。 受众必须是播放器应用程序客户端 ID。 此外，必须在颁发者 URL 的末尾添加“/”。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    在 [JWT 解码器](http://jwt.calebb.net/)中，可以看到 JWT 中所示的 **aud** 和 **iss**：

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* 在应用程序的“配置”选项卡上，将权限添加到 Azure AD 中的应用程序。  对于本地版本和已部署的版本，需要提供每个应用程序的权限。

    ![权限](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* 设置动态 CENC 保护时，请使用正确的颁发者。

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    以下代码无法运行：

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID 是 Azure AD 租户 ID。 可以在 Azure 门户上的“终结点”弹出菜单中找到该 GUID。 

* 授予组成员资格声明权限。 确保 Azure AD 应用程序清单文件中包含以下内容： 

    "groupMembershipClaims":"All"   （默认值为 null）

* 创建限制要求时，请设置适当的 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    由于除了 JWT (ACS) 以外，还添加了对 SWT (Azure AD) 的支持，因此默认 TokenType 是 TokenType.JWT。 如果使用 SWT/ACS，则必须将令牌设置为 TokenType.SWT。

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

![自定义 Azure AD 租户域帐户 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**采用智能卡的 Microsoft 域帐户**：由 Microsoft 公司 IT 部门自定义的、采用双重身份验证的登录页。

![自定义 Azure AD 租户域帐户 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帐户**：使用者的 Microsoft 帐户登录页。

![自定义 Azure AD 租户域帐户 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>使用 PlayReady 的加密媒体扩展

在支持 PlayReady 加密媒体扩展 (EME) 的新式浏览器（例如 Windows 8.1 和更高版本上的 Internet Explorer 11，以及 Windows 10 上的 Microsoft Edge 浏览器）上，PlayReady 是 EME 的基本 DRM。

![使用 EME for PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

出现深色播放器区域是因为 PlayReady 保护阻止对受保护的视频进行屏幕截图。

以下屏幕截图显示了播放器插件和 Microsoft Security Essentials (MSE)/EME 支持。

![PlayReady 的播放器插件](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Windows 10 上的 Microsoft Edge 和 Internet Explorer 11 中的 EME 允许在支持 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 的 Windows 10 设备上调用该技术。 PlayReady SL3000 解锁增强型高级内容（4K、HDR）以及新的内容传送模型（增强型内容）流程。

将重心放在 Windows 设备上：PlayReady 是 Windows 设备可用的硬件中唯一的 DRM（PlayReady SL3000）。 流式处理服务可通过 EME 或通用 Windows 平台应用程序来使用 PlayReady，并利用 PlayReady SL3000 来提供比其他 DRM 所能提供更高的视频质量。 一般而言，2K 及更低分辨内容会流经 Chrome 或 Firefox，而 4K 及更低分辨内容流过 Microsoft Edge/Internet Explorer 11 或相同设备上的通用 Windows 平台应用程序。 流量取决于服务设置和实现。

#### <a name="use-eme-for-widevine"></a>使用 EME for Widevine

在支持 EME/Widevine 的现代浏览器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背后的 DRM。

![使用 EME for Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine 不会阻止对受保护的视频进行屏幕截图。

![Widevine 的播放器插件](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>使用适用于 FairPlay 的 EME

同样，可以在 macOS 或 iOS 11.2 和更高版本 Safari 中，使用此测试播放机测试受 FairPlay 保护的内容。

请确保使用“FairPlay”作为 protectionInfo.type，并在 FPS AC 路径（FairPlay 流式处理应用程序证书路径）中包含应用程序证书的正确 URL。

### <a name="unentitled-users"></a>未获授权的用户

如果用户不是“已获授权用户”组的成员，则通不过权利检查。 多重 DRM 许可证服务将拒绝颁发请求的许可证，如下所述。 详细的说明是“许可证获取失败”，这是设计使然。

![未获授权的用户](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>运行自定义安全令牌服务

如果运行自定义 STS，JWT 由自定义 STS 使用对称或非对称密钥来颁发。

以下屏幕截图显示了使用对称密钥的方案（使用 Chrome）：

![自定义 STS 和对称密钥](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

以下屏幕截图显示了通过 X509 证书使用非对称密钥的方案（使用 Microsoft 新式浏览器）：

![自定义 STS 和非对称密钥](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

在上述两个方案中，用户身份验证相同。 身份验证是通过 Azure AD 发生的。 唯一的差别在于，JWT 由自定义 STS 而不是 Azure AD 颁发。 配置动态 CENC 保护时，许可证传送服务的限制将指定 JWT 的类型是对称还是非对称密钥。

## <a name="next-steps"></a>后续步骤

* [常见问题](frequently-asked-questions.md)
* [内容保护概述](content-protection-overview.md)
* [使用 DRM 保护内容](protect-with-drm.md)
