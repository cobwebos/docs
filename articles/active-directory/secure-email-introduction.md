---
title: "建议的安全策略和配置 | Microsoft docs"
description: "介绍 Microsoft 就如何部署安全电子邮件、文档以及应用策略和配置提供的建议和核心概念。"
author: jeffgilb
manager: femila
editor: jsnow
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 3c8e636921ab269a5314f74789c8144602685412
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="recommended-security-policies-and-configurations"></a>建议的安全策略和配置
 
## <a name="introduction"></a>介绍

提供的建议介绍了在企业中应用安全策略和配置时通用的 Microsoft 准则。 这些建议可确保员工在工作时既安全又高效。 

### <a name="intended-audience"></a>目标受众

这些建议的目标受众是熟悉 [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx) (Office 365) 和 [Microsoft 企业移动性 + 安全性](http://microsoft.com/ems)产品的企业基础结构架构师和 IT 专业人员。 这些产品包括但不限于：Azure Active Directory（标识）、Microsoft Intune（设备管理）、Azure 信息保护（数据保护）。 

### <a name="customer-environment"></a>客户环境

建议的策略适用于完全在 Microsoft 云中运营的企业组织，以及其基础结构既在本地部署又在 Microsoft 云中部署的客户。 

### <a name="assumptions"></a>假设

提供的建议中，许多依赖于仅提供给企业移动性 + 安全性 (EMS) E5 订阅的服务。 提供的建议假定 EMS E5 订阅功能是完整的。 

### <a name="caveats"></a>注意事项

你的组织可能必须遵守法规或其他符合性要求，其中包括的特定建议可能会要求你应用与这些建议的配置不符的策略。  这些配置建议的使用情况控件是以前从未提供过的。  我们建议这些控件，是因为我们认为它们在安全性和工作效率之间实现了很好的平衡。  

我们严格遵循各种组织保护要求。 不过，我们无法遵循你组织的所有可能的要求或所有独特的要求。 请使用本文档作为指南，了解 Microsoft 和那些安全、高效的企业团队的想法，确保在组织中正确应用策略。 

>[!NOTE]
>如需这些必需核心概念的概述，以便了解这些建议中描述的保护功能，请参阅 [EMS 和 Office 365 服务概述](secure-email-ems-office365-service-descriptions.md)。
>

## <a name="core-concepts"></a>核心概念

如果用户在尝试完成工作的过程中遇到不必要的障碍，因而只能规避组织安全策略，则不管你采取什么样的安全措施都无济于事。 Azure AD 单一登录 (SSO) 的目的是尽量减少用户的负担。 这样就可以让用户在遵守组织访问控制策略的情况下，保持较高的工作效率。 

### <a name="single-sign-on-authentication"></a>单一登录身份验证

下图描绘的是典型的 SSO 身份验证流：

![最终用户单一登录体验](./media/secure-email/typical-authentication-flow.png)

客户端将凭据（例如用户名和密码）和/或在过去获得的任何 SSO 项目提交到 Azure AD 即可开始身份验证。 SSO 项目可以是用于浏览器的会话令牌，也可以是用于富应用程序的刷新令牌。 

Azure AD 验证凭据和/或 SSO 项目，并评估所有适用的策略， 然后发出资源提供程序（图中的 Exchange Online）的访问令牌。 Azure AD 还在响应过程中发出 SSO 项目，让客户端在未来的请求中实现 SSO。 

客户端存储 SSO 项目，并将访问令牌作为身份证明提交给资源提供程序。 在验证访问令牌并执行必需的检查后，Exchange Online 会授予客户端访问电子邮件的权限。

#### <a name="single-sign-on-sso-refresh-tokens"></a>单一登录 (SSO) 刷新令牌

SSO 可以通过多种方式来实现。 例如，可以将标识提供者提供的 Cookie 用作 SSO 项目，以便在浏览器中存储用户的登录状态， 然后即可在以后尝试通过同一标识提供者以无提示方式（系统不提示你输入凭据）登录到应用程序。 

当用户通过 Azure AD 进行身份验证时，系统将在用户的浏览器与 Azure AD 之间建立 SSO 会话。 SSO 令牌采用 Cookie 形式，代表此会话。 Azure AD 使用两种 SSO 会话令牌：持久性和非持久性会话令牌。 如果在登录时选中“使我保持登录状态”复选框，浏览器会将持久性会话令牌存储为持久性 Cookie。 非持久性会话令牌存储为会话 Cookie，在浏览器关闭时销毁。 

对于能够使用新式身份验证协议（例如 [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) 和 [OAuth 2.0](https://tools.ietf.org/html/rfc6749)）的功能强大的应用程序，可以将刷新令牌用作 SSO 项目（此前介绍的 SSO Cookie 除外）来启用 SSO。 当应用程序请求新的访问令牌时，系统会将刷新令牌提供给授权服务器。 

刷新令牌包含有关身份验证方法类型的声明和属性，这些方法是在对用户进行身份验证时使用的。 例如，如果用户成功使用多种方法（用户名和密码以及电话身份验证）进行了身份验证，则刷新令牌中会存在多重身份验证 (MFA) 声明。 此外还可能有其他声明，其中包含 MFA 有效期等数据。 

客户端可以通过刷新令牌来获取新的访问令牌，不需进行全新的交互式身份验证。 刷新令牌的生存期比访问令牌要长得多，可以通过兑换方式来获得新的访问和刷新令牌对。 然后，新获得的刷新令牌又可继续用于提取另一组访问和刷新令牌。 

客户端会继续此 SSO 过程，直至刷新令牌最长非活动时间设置过期、刷新令牌最长存在时间过期，或者身份验证和授权策略要求更改。 这种更改在颁发原始刷新令牌时发生。 重大的用户属性更改（例如密码重置）也需生成新的身份验证令牌。 客户端必须进行全新的交互式身份验证才能继续操作。 其实质是 SSO 过程出现中断，这是客户端此前从未经历过的。 

#### <a name="conditional-access"></a>条件性访问

Azure AD 充当应用程序的授权服务器，由其来决定是否颁发访问令牌，而决定依据则是对适用于你要尝试访问的资源的条件性访问策略的评估。 如果符合策略要求，则会颁发访问令牌和更新的刷新令牌。 如果不符合策略，用户会收到如何才能符合策略的说明，并且/或者会被要求完成包括多重身份验证 (MFA) 在内的其他步骤。  完成 MFA 后，系统会将 MFA 声明添加到生成的刷新令牌中。  

刷新令牌中的声明会随着时间的推移而累积。 某些声明有过期时间，过期后就不再在授权检查时考虑这些声明。 有时候，这可能会导致意外结果。 例如，如果将条件性访问策略配置为从 Extranet 位置进行身份验证尝试时必须进行 MFA， 则当用户从 Extranet 访问资源时，有时可能不会收到预期的 MFA 提示。 之所以出现这种情况，可能是因为用户刚好在离开 Intranet 之前执行过 MFA。 因此，用户的访问令牌中存在有效的 MFA 声明。 该 MFA 声明符合策略要求，因此 Azure AD 不通过额外的 MFA 请求来提示用户。

#### <a name="token-lifetime-policy"></a>令牌生存期策略

除了令牌中的各个声明会过期，令牌本身也会过期。 如前所述，令牌过期是 SSO 体验中断的一个原因。 可以通过[令牌生存期策略](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)设置 Azure AD 颁发的令牌的生存期。 从上述内容可以推断，有时候较难确定 SSO 会话的“轮廓”。 例如，在使用富应用程序时，会存在各种因素，这些因素看起来并无关联，但却会影响 SSO 会话的生存期。

>[!NOTE]
>Azure AD 全局管理员可以控制刷新令牌的有效性和非活动期间。 若要了解如何通过相关设置来配置访问令牌和声明，请参阅 [Azure Active Directory 中可配置的令牌生存期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)一文。
>

#### <a name="primary-refresh-tokens"></a>主刷新令牌

到目前为止，本文讨论的都是如何在单客户端上下文中使用 SSO。 但这对于在单个应用中体验 SSO 是不够的。 目前，用户体验到的交互式工作流跨越同一应用程序套件（例如 Microsoft Office）中的多个应用程序。 用户在访问时需要跨越不相关的应用程序，包括内部开发的 LOB 应用程序。 

传统上，已加入域的 Windows 设备使用 Windows 集成身份验证 (Kerberos) 跨多个应用程序和资源来实现高程度 SSO 体验。 这些应用包括支持的浏览器，例如 Internet Explorer 或 Edge。 对于 Azure AD 领域，存在主刷新令牌 (PRT) 形式的模拟。 

此特权 PRT 令牌仅适用于特选的一组客户端实体（例如平台系统组件）。 然后即可通过这些实体对其他客户端应用程序进行中转身份验证访问，因此也能够获得无缝的 SSO 体验。 

对于 [iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) 和 [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android) 设备上的 Office 365 用户，我们还做了其他工作，通过应用身份验证代理功能来减少必需身份验证的次数。 该功能已内置到 Microsoft Authenticator 和 Intune 公司门户应用中。

>[!NOTE]
>本文档所述建议假定其中一个应用已部署到用户的 iOS 或 Android 设备， 例如身份验证代理（Microsoft Authenticator 或 Intune 公司门户）应用。
>

### <a name="multi-factor-authentication"></a>多重身份验证

多重身份验证 (MFA) 提供有关身份验证主体的高级别信任，因为该主体提供有关其标识的多个证明或多项证据。 证明可以是预先确定的仅主体和授权机构了解的机密，也可以是仅主体应该拥有的物理实体。 MFA 通常分阶段执行。 首先，它使用密码来确定身份，然后要求使用另一（不容易受到恶意攻击的）身份验证方法进行双重身份验证，反之亦然。

不同授权机构对 MFA（也称强身份验证）的解释可能略有不同。 例如，某些授权机构（或者更具体地说，为这些授权机构配置策略的管理员）可能会选择将基于物理智能卡的身份验证视为 MFA。 这种情况是可能发生的，虽然严格说来智能卡身份验证是单级身份验证。 

如果在使用智能卡时既需要物理智能卡，又需要输入 PIN（机密），则可将这种验证方式视为 MFA。 不过，也有人在需要多久执行一次其方式更繁琐的身份验证这个问题上持宽松态度。 因此可以认为，对于通常需要强身份验证的资源，在强身份验证之间间隔进行正常身份验证是有效的。 例如，某些组织只要求用户每隔数小时或数天进行一次 MFA。 间隔时间取决于受保护资源的敏感程度，但这样做的前提是尝试访问资源的用户所在物理位置不变。

Azure AD 和 AD FS 使用 MFA 声明来指示身份验证是否是通过 MFA 执行的。 默认情况下，如果身份验证是通过 [Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) 或 [Windows Hello 企业版](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)执行的，则 Azure AD 会颁发带 MFA 声明的令牌。 在联合身份验证方案中，Azure AD 认可进行联合身份验证的标识提供者（例如 AD FS）提供的 MFA 声明，因此会在令牌中传递 MFA 声明。 



## <a name="recommended-client-configuration-for-sso-and-conditional-access"></a>建议用于 SSO 和条件性访问的客户端配置
此部分介绍我们建议的默认平台客户端配置，目的是为用户提供最佳的 SSO 体验，并介绍进行条件性访问的技术先决条件。

### <a name="windows-devices"></a>Windows 设备
建议使用 Windows 10（1703 或更高版），因为根据设计，Azure 可以提供最顺畅的 SSO 体验，既适用于本地，又适用于 Azure AD。 应该将工作单位或学校配发的设备配置为直接加入 Azure AD；如果组织使用本地 AD 域加入，则应将这些设备[配置为自动且无提示地注册到 Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)。 

对于 BYOD Windows 设备，用户可以使用“添加工作或学校帐户”。 对于 BYOD Windows 设备，用户可以使用“添加工作或学校帐户”。 请注意，Windows 10 上的 Chrome 浏览器用户需[安装一个扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?utm_source=chrome-app-launcher-info-dialog)才能获得与 Edge/IE 相同的顺畅登录体验。 另外，如果组织有已加入域的 Windows 7 设备，则可安装适用于非 Windows 10 计算机的 Microsoft Workplace Join [注册包](https://www.microsoft.com/download/details.aspx?id=53554)，将设备注册到 Azure AD。

### <a name="ios-devices"></a>iOS 设备

建议在部署条件性访问或 MFA 策略之前，先在用户设备上安装 [Microsoft Authenticator 应用](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)。 至少应该在系统[要求用户将其设备注册](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time)到 Azure AD（通过添加工作或学校帐户的方式）时，或者在用户安装 Intune 公司门户应用以将其设备通过注册方式加入管理时，安装该应用。 这取决于配置的条件性访问策略。

### <a name="android-devices"></a>Android 设备

建议用户在部署条件性访问策略之前安装 [Intune 公司门户应用](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal&hl=en
)和 [Microsoft Authenticator 应用](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)，或者在进行某些身份验证尝试时根据需要这样做。 安装完应用之后，系统可能会要求用户注册到 Azure AD 或将其设备注册到 Intune。 这取决于配置的条件性访问策略。 

另一建议是，公司拥有的设备 (COD) 应由 OEM 标准化，而支持 Android for Work 或 Samsung Knox 的版本则应允许通过 Intune MDM 策略来管理和保护邮件帐户。


### <a name="recommended-email-clients"></a>建议的电子邮件客户端
以下电子邮件客户端支持新式身份验证和条件性访问。 Azure 信息保护尚不适用于所有客户端。

|平台|客户端|版本/说明|Azure 信息保护|
|:-------|:-----|:------------|:--------------------|
|**Windows**|Outlook|2016、2013（[启用新式身份验证]((https://support.office.com/article/Enable-Modern-Authentication-for-Office-2013-on-Windows-devices-7dc1c01a-090f-4971-9677-f1b192d6c910))）|是|
|**iOS**|Outlook|[最新](https://itunes.apple.com/us/app/microsoft-outlook-email-and-calendar/id951937596?mt=8)|否|
|**Android**|Outlook|[最新](https://play.google.com/store/apps/details?id=com.microsoft.office.outlook&hl=en)|否|
|macOS|即将提供支持||否|
|**Linux**|不支持||否|

#### <a name="additional-client-software"></a>其他客户端软件
若要访问 Azure 信息保护的受保护文档，可能需要其他软件。 请确保使用[支持的软件和文档格式](https://docs.microsoft.com/information-protection/get-started/requirements-applications)，以便通过 Azure 信息保护来创建和查看受保护的文档。


### <a name="security-guidelines"></a>安全指导原则

此部分包含通用的安全指导原则。在实施后续部分提供的任何建议时，应该遵循这些原则。

#### <a name="security-and-productivity-trade-offs"></a>安全性和工作效率权衡

需要在安全性和工作效率之间进行权衡。 为了更好地理解这些权衡，请参看广泛使用的安全性-功能-可用性/易用性 (SFU) 安全三因素图：

![安全性和工作效率权衡](./media/secure-email/security-triad.png)

本文档中的建议以 SFU 三因素模型为基础，并遵循以下原则： 

* 了解受众 - 按工作职能/安全标准灵活处理
* 及时应用安全策略，确保其有意义

#### <a name="administrators-versus-users"></a>管理员与用户

建议创建安全组，其中包含的所有用户都有管理帐户，或者有资格临时接收管理帐户特权。 这些安全组用于定义特定于 Azure AD 和 Office 365 管理员的条件性访问策略。  

策略建议考虑的是与帐户关联的特权。 [Office 365 管理员](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)角色相对于 Exchange Online 和 Office 365 用户角色拥有更多实质上的特权。 因此，对于这些帐户的策略建议要比常规用户帐户更严格。 所有与管理员关联的策略都是指建议用于 Office 365 管理帐户的策略。

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>减少可以持久进行管理访问的帐户数

使用 Azure AD Privileged Identity Management 减少持久性管理帐户数。 另外，建议 Office 365 管理员创建一个单独的用户帐户，在执行常规的非管理性操作时使用；只有在需要完成与其工作职能相关的任务时，才使用管理帐户。


## <a name="tiers-of-security-and-protection"></a>安全和保护层
大多数组织对于安全和数据保护都有特定的要求。 这些要求因行业部门和组织内的工作职能而异。 例如，法律部门和 Office 365 管理员可能会要求对其电子邮件通信实施更多的安全和信息保护控制，而这些对于其他业务单位用户来说则不是必需的。 

每个行业也都有自己的一套专门的规章制度。 在提供建议时，我们不是提供一个包含所有可能的安全选项的列表，也不是按行业部门或工作职能来提供，而是将其分成三个不同的安全和保护层级，根据具体的需求来应用，这些层级包括：[基线、敏感和高度管控](https://go.microsoft.com/fwlink/p/?linkid=841656)。  

基线。 建议设置一个最低标准，用于保护数据和那些访问数据的标识和设备。 遵循基线建议可获得强大的默认保护，足以满足许多组织的需求。 

敏感。 某些客户的所有或部分数据必须在较高级别进行保护。 可以对 Office 365 环境中的所有数据集或具体数据集应用强化保护。 建议通过相应的安全级别对访问敏感数据的标识和设备进行保护。 

高度管控。 某些组织可能有很小一部分数据属于高级别数据、商业机密数据或高度管控的数据。 Microsoft 会根据组织的相关要求提供相应功能，包括增强对标识和设备的保护。 

### <a name="default-protection-mechanism-recommendations"></a>默认保护机制建议
下表包含默认的保护机制建议，适用于每个此前定义的安全和保护层级：

|保护机制|基线|敏感|高度管控|
|:-------------------|:-------|:--------|:---------------|
|强制执行 MFA|针对中等登录风险或以上|针对低登录风险或以上|针对所有新的会话|
|强制更改密码|针对高风险用户|针对高风险用户|针对高风险用户|
|强制执行 Intune 应用程序保护|是|是|是|
|强制执行 Intune 注册 (COD)|需要符合的或已加入域的设备|需要符合的或已加入域的设备|需要符合的或已加入域的设备|

### <a name="device-ownership"></a>设备所有权
上表反映了一个趋势，即许多组织支持混合使用公司拥有的设备 (COD)、个人设备或自带设备 (BYOD)，以便提高全体工作人员的移动工作效率。 Intune 应用保护策略可确保电子邮件不会从 COD 和 BYOD 上的 Outlook 移动应用和其他 Office 移动应用泄漏。  

公司拥有的设备需要通过 Intune 进行管理或者需要加入域，然后才能应用其他保护和控制。  组织可能会选择不允许将 BYOD 用于特定的用户填充操作或特定的应用，具体取决于数据敏感程度。


## <a name="next-steps"></a>后续步骤
[保护电子邮件策略和配置](secure-email-recommended-policies.md)

