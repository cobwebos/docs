---
title: 规划 Azure Active Directory 单一登录部署
description: 指南可帮助你规划、 部署和管理组织中的 SSO。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499244"
---
# <a name="plan-a-single-sign-on-deployment"></a>规划单一登录部署

单一登录 (SSO) 意味着访问用户需要登录，仅在使用单个用户帐户后，所有应用程序和资源。 使用 SSO，用户可以访问全部所需的应用程序，而无需进行第二次身份验证。

## <a name="benefits-of-sso"></a>SSO 的优点

单一登录 (SSO) 时，添加的安全和方便用户登录到 Azure Active Directory (Azure AD) 中的应用程序。 

许多组织依赖于软件最终用户工作效率的服务 (SaaS) 应用程序，如 Office 365、 Box 和 Salesforce。 从历史上看，IT 人员需要单独创建和更新中的每个 SaaS 应用程序和用户需要记住的密码，为每个用户帐户。

Azure Marketplace 有 3000 多应用程序使用 SSO 连接预先集成，轻松地将其集成在租户中。

## <a name="licensing"></a>许可

- **Azure AD 许可**-SSO 预集成 SaaS 应用程序是免费的。 但是，在你的目录和你想要部署的功能中的对象的数目可能需要额外的许可证。 有关许可证要求的完整列表，请参阅[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。
- **应用程序授权**-你将需要你的 SaaS 应用程序，以满足你的业务需求的适当许可证。 使用应用程序所有者能够确定分配到应用程序的用户是否具有为其角色应用程序中的相应许可证。 如果 Azure AD 管理基于角色的自动设置，在 Azure AD 中分配的角色必须与应用程序中拥有的许可证数量保持一致。 在应用程序中拥有的许可证不正确数量可能会导致错误在预配/更新的用户的过程。

## <a name="plan-your-sso-team"></a>规划您的 SSO 团队

- **接触右利益干系人**-时技术项目失败，它通常是因为不匹配期望上影响、 结果和职责。 若要避免这些缺陷[确保您正在具有吸引力右利益干系人](https://aka.ms/deploymentplans)和利益干系人了解其角色。
- **计划通信**-通信对任何新服务的成功至关重要。 主动传达给你的用户有关他们的体验有何更改、 何时会更改，以及如何获取支持，在他们遇到的问题。 查看的选项[最终用户将如何访问其 SSO 已启用应用程序](end-user-experiences.md)，写出通信以匹配你的选择。 

## <a name="plan-your-sso-protocol"></a>规划您的 SSO 协议

SSO 实现基于协议的联合身份验证提高了安全性、 可靠性和和最终用户体验，并且更轻松地实现。 许多应用程序是与 Azure AD 中预先集成[分步指南将指导可用](../saas-apps/tutorial-list.md)。 您可以在找到我们[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)。 可以一文中找到的每个 SSO 方法的详细的信息[单一登录方式登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)。

有两种主要方式，在其中您可以让你的用户实现单一登录到您的应用程序：

- **使用联合单一登录**Azure AD 用户进行身份验证对应用程序通过使用其 Azure AD 帐户。 此方法支持的应用程序支持 saml 2.0 中，WS 联合身份验证或 OpenID Connect 协议，并且是最丰富的单一登录模式。 我们建议使用与 Azure AD 时应用程序支持此功能，而不是基于密码的 SSO 和 ADFS 联合 SSO。

- **使用基于密码的单一登录**用户登录到应用程序使用用户名和密码首次访问它。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 基于密码的单一登录允许使用 Web 浏览器扩展插件或移动应用安全存储和重放应用程序的密码。 此选项利用应用程序提供的现有登录过程，使管理员能够管理密码，并且不需要用户知道的密码。

### <a name="considerations-for-federation-based-sso"></a>基于联合的 SSO 的注意事项

- **使用 OpenID Connect 和 OAuth** -如果你要连接到支持它，该应用程序使用 OIDC/OAuth 2.0 方法启用到该应用程序在 SSO。 此方法需要较少的配置，并能够提供更丰富的用户体验。 有关详细信息，请参阅[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)， [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)，并[Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **终结点配置基于 SAML 的 sso** -如果你使用 SAML，您的开发人员将需要之前配置应用程序的特定信息。 有关详细信息，请参阅[配置基本 SAML 选项](configure-single-sign-on-portal.md)。
- **证书管理的基于 SAML 的 SSO** -时为你的应用程序，Azure AD 将创建默认情况下有效三年的证书启用联合 SSO。 您可以自定义如果需要该证书的到期日期。 请确保在续订证书过期之前的位置具有的流程。 若要了解详细信息，请参阅[Azure AD 管理证书](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>基于密码的 SSO 的注意事项

使用 Azure AD 进行基于密码的 SSO 需要部署浏览器扩展插件将安全地检索凭据，并填写登录窗体。 定义一种机制来部署具有大规模扩展[支持的浏览器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。 选项包括：

- [Internet Explorer 的组策略 ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet explorer 的 system Center Configuration Manager (SCCM) ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [用户驱动的下载和 Chrome、 Firefox、 Microsoft Edge 或 IE 的配置 ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

若要了解详细信息，请参阅[如何配置密码单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>捕获登录窗体的应用程序不在库中的元数据

Microsoft 通过密码保管库 （捕获用户名和密码字段） 的 web 应用程序支持捕获元数据。 配置要捕获的窗体元数据的应用程序的过程中导航到登录 URL。 要求提供确切的登录 URL 的应用程序所有者。 在登录过程中，将 Azure AD 凭据映射到登录期间的应用程序使用此信息。

若要了解详细信息，请参阅[应用程序访问与 SSO 与 Azure AD 是什么？ – 基于密码的 SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>指示在窗体中的元数据需要被重新捕获

当应用程序更改其 HTML 布局时，可能需要重新捕获要调整所做的更改的元数据。 表示 HTML 布局已更改的常见症状包括：

- 用户 reporting 应用程序上，单击"停止"在登录页
- 报告未填充的用户名或密码的用户

#### <a name="shared-accounts"></a>共享的帐户

从登录的角度来看，使用共享帐户的应用程序不是不同于使用适用于单个用户的密码 SSO 的库应用程序。 但是，有一些规划和配置应用程序应使用共享的帐户时所需的其他步骤：

1. 使用应用程序业务用户可以记录以下：
   1. 组的组织将使用该应用程序中的用户
   1. 现有组的一组用户关联的应用程序中的凭据 
1. 对于每个组合的一组用户和凭据，在云中或本地基于你的需求创建安全组。
1. 重置共享的凭据。 一旦在 Azure AD 中部署应用，个人无需共享帐户的密码。 由于 Azure AD 将存储的密码，请考虑将其设置为非常长而复杂。 
1. 如果应用程序支持此，配置的密码自动滚动更新。 这样一来，即使是管理员执行了初始设置的人员将了解共享帐户的密码。 

## <a name="plan-your-authentication-method"></a>计划你的身份验证方法

选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 请实现通过使用 Azure AD Connect（还可在云中预配用户）配置的身份验证方法。

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 您应选择最符合您的特定方案。 有关详细信息，请参阅[选择正确的身份验证方法为 Azure Active Directory 混合标识解决方案](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。

## <a name="plan-your-security-and-governance"></a>规划安全和管理 

标识是新的安全关注与投资主轴，因为网络外围已变得越来越多地越大，漏洞和更少有效使用的 BYOD 设备爆炸式增长和云应用程序。 

### <a name="plan-access-reviews"></a>计划访问评审

[访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)使组织能够有效地管理组成员身份、 对企业应用程序和角色分配访问权限。 您应计划评审定期进行以确保正确的人拥有持续访问权限的用户访问权限。

若要设置访问评审时规划的关键主题包括：

1. 确定适合你的业务需求的访问评审节奏。 这可以是为频繁至一周一次，每月、 每年一次，或按需练习。

1. 创建表示应用程序访问报表的审阅者的组。 您需要确保利益干系人最熟悉的应用程序及其目标用户和用例是参与访问评审

1. 完成访问评审包括拿走应用访问权限的用户不再需要访问权限。 计划用于处理来自已拒绝的用户的潜在支持请求。 已删除的用户将保持在此期间可还原由管理员有必要的 30 天的 Azure AD 中已删除。 30 天后，该用户将被永久删除。 使用 Azure Active Directory 门户，全局管理员可以显式永久删除最近删除的用户之前达到该时间段。

### <a name="plan-auditing"></a>计划审核

Azure AD 提供[包含技术和业务见解的报表](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 

提供了安全和活动报告。 安全报告显示已标记为风险和风险登录的用户。活动报告帮助你了解组织中用户的行为的详细说明登录活动，并提供所有登录名的审核线索。 您可以使用报表来管理风险、 提高工作效率，并监视符合性。

| 报表类型 | 访问评审 | 安全报表 | 登录报告 |
|-------------|---------------|------------------|----------------|
| 用于查看 | 应用程序权限和使用情况。 | 可能已泄露的帐户 | 谁在访问应用程序 |
| 潜在操作 | 审核的访问;撤消权限 | 撤消访问权限;强制安全重置 | 撤消访问权限 |

Azure AD 将保留 30 天最多的审核数据，并使数据可通过 Azure 管理门户或通过 API 供您下载到您的分析系统。

### <a name="consider-using-microsoft-cloud-application-security"></a>请考虑使用 Microsoft 云应用程序安全性

Microsoft 云应用安全 (MCAS) 是一种云访问安全代理 (CASB) 解决方案。 它让你了解你的云应用和服务、 提供复杂的分析来识别和防范 cyberthreats，并使您能够控制如何将数据传输。

部署 MCAS，可以：

- 使用 Cloud Discovery 映射并确定云环境和你的组织使用的云应用。
- 批准和取消批准云中的应用
- 使用易于部署的应用连接器利用提供程序 Api，可见性和管理连接到的应用
- 使用条件访问应用控制保护来实时查看和控制访问和你的云应用中的活动
- 可帮助你通过设置，并不断地微调、 策略实现持续控制。

Microsoft 云应用程序安全性 (MCAS) 会话控制还可用于在任何操作系统上任何主流平台上的任何浏览器。 移动应用和桌面应用程序还可阻止或允许。 通过与 Azure AD 本机集成，任何使用 SAML，进行配置的应用或 Open ID Connect 与 Azure AD 中的单一登录的应用程序可以支持，包括[几个精选应用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)。

MCAS 有关的信息，请参阅[Microsoft Cloud App Security 概述](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是基于用户的订阅服务。 你可以查看中的许可详细信息[MCAS 许可数据表](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)。

### <a name="use-conditional-access"></a>使用条件性访问

使用条件性访问，可以为你的云应用自动基于标准的访问控制决策。

完成第一因素身份验证后将强制执行条件访问策略。 因此，不应条件性访问，如方案第一个行防御拒绝服务 (DoS) 攻击，但可以使用从这些事件的信号来确定访问权限。 有关示例的登录风险级别，可以使用位置为请求中，依次类推。 有关条件性访问的详细信息，请参阅[概述](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)并[部署计划](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure 的 SSO 技术要求

以下部分详细介绍的要求，包括必要的环境、 终结点、 声明映射、 必需的属性、 证书和使用的协议为特定应用程序配置。 你将需要此信息来配置 SSO [Azure AD 门户](https://portal.azure.com/)。

### <a name="authentication-mechanism-details"></a>身份验证机制详细信息

对于所有预先集成的 SaaS 应用程序，Microsoft 提供的教程并不需要此信息。 如果应用程序不在我们的应用程序应用商店 / 库，您可能需要收集以下数据块：

- **当前应用程序将使用为 SSO 适用的标识提供者**-例如：AD FS，PingFederate，Okta
- **在目标应用程序支持的协议**-例如，SAML 2.0、 OpenID Connect、 OAuth、 基于窗体的身份验证，WS 联合身份验证、 WS 信任
- **正在配置 Azure AD 的协议**-例如，SAML 2.0 或 1.1，OpenID Connect、 OAuth、 基于窗体的 WS 联合身份验证

### <a name="attribute-requirements"></a>特性需求

没有一组预配置的属性和 Azure AD 用户对象和每个 SaaS 应用的用户对象之间的属性映射。 某些应用程序管理其他类型的组等对象。 规划 Azure AD 中为你的应用程序的用户属性的映射并[自定义默认属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)根据你的业务需要。

### <a name="certificate-requirements"></a>证书要求

应用程序的证书必须是最新的或者无法访问该应用程序的用户的风险。 大多数 SaaS 应用程序证书非常适用于 36 个月。 更改应用程序边栏选项卡中的证书持续时间。 请确保文档过期并且知道如何将管理证书续订。 

有两种方法来管理你的证书。 

- **自动证书滚动更新**-Microsoft 支持[在 Azure AD 中签名密钥滚动更新](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 这是我们的管理证书的首选的方法，而不是所有 ISV 都支持此方案。

- **手动更新**-每个应用程序具有其自己的证书过期基于定义的方式。 应用程序的证书过期之前，创建新证书并将其发送到 ISV。 此信息可以从联合身份验证元数据请求。 [详细了解联合身份验证元数据。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>实现 SSO

使用以下几个阶段来规划和部署你组织中的解决方案：

### <a name="user-configuration-for-sso"></a>用户配置 sso

- **标识你的测试用户**

   到应用程序所有者联系并请求他们创建的应用程序中的三个测试用户的最小值。 确保你将使用作为主标识符的信息正确填充，并且与 Azure AD 中可用的属性相匹配。 在大多数情况下这会映射到"NameID"基于 SAML 的应用程序。 JWT 令牌，它是"preferred_username"。
   
   手动创建用户在 Azure AD 中或者作为基于云的用户或同步从使用 Azure AD Connect 同步引擎的本地用户。 请确保信息与发送到应用程序的声明相匹配。

- **配置 SSO**

   从[的应用程序列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)，找到并打开 SSO 本教程为您的应用程序，然后按本教程的步骤已成功配置你的 SaaS 应用程序。

   如果找不到你的应用程序，请参阅[自定义应用程序文档](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 这将引导你完成如何添加不在 Azure AD 库应用程序。

   或者，可以使用的企业应用程序使用的 SAML 令牌中颁发的声明[Microsoft 的指导文档](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)。 请确保此值映射到您想要为应用程序在 SAML 响应中收到的内容。 如果在配置期间遇到问题，在使用我们的指南[如何调试 SSO 的集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)。

自定义应用程序载入是 Azure AD Premium P1 或 P2 许可证功能。

### <a name="provide-sso-change-communications-to-end-users"></a>向最终用户提供 SSO 更改通信

实现你的沟通计划。 请确保会让您知道，更改即将推出的它已到达时要执行的操作现在，以及如何寻求帮助的最终用户。

### <a name="verify-end-user-scenarios-for-sso"></a>验证 sso 的最终用户方案

可以使用以下测试用例执行测试上公司和个人设备，确保你的 SSO 配置按预期工作。 下面的方案假定用户导航到应用程序的 URL，并通过启动的服务提供商 （SP 发起的身份验证流） 的身份验证流。

| 场景 | 在用户通过 SP 发起的身份验证流的预期的结果 |
|----------|---------------------------------------------------|
| 登录到应用程序与公司网络上的 IE。 | 集成 Windows 身份验证 (IWA) 不出现在任何其他的提示。 |
| 登录到与 IE 随新的登录尝试的企业网络外部的应用程序。 | 在 AD FS 服务器上的基于窗体的提示。 用户成功登录和浏览器会提示进行 MFA。 |
| 登录到与 IE 与当前会话的企业网络外部的应用程序永远不会执行 MFA。 | 用户不会接收第一个因素的提示。 用户会收到有关 MFA 提示。 |
| 登录到应用程序使用与当前会话的企业网络外部的 IE 和已在此会话中执行 MFA。 | 用户不会接收第一个因素的提示。 用户不会接收 MFA。 用户 SSOs 到应用程序。 |
| 登录到应用程序使用与当前会话的企业网络外部的 Chrome/Firefox/Safari 和已在此会话中执行 MFA。 | 用户不会接收第一个因素的提示。 用户不会接收 MFA。 SSO 的用户访问应用程序。 |
| 登录到使用 Chrome/Firefox/Safari 随新的登录尝试的企业网络外部的应用程序。 | 在 AD FS 服务器上的基于窗体的提示。 用户成功登录和浏览器会提示进行 MFA。 |
| 为应用程序具有与当前会话的企业网络上的 Chrome/Firefox 的登录名。 | 用户不会接收第一个因素的提示。 用户不会接收 MFA。 SSO 的用户访问应用程序。 |
| 登录到应用程序与应用程序移动应用中的新的登录尝试。 | 在 AD FS 服务器上的基于窗体的提示。 用户成功登录，并针对 MFA 提示 ADAL 客户端。 |
| 未经授权的用户尝试登录到应用程序使用登录名的 URL。 | 在 AD FS 服务器上的基于窗体的提示。 用户无法使用第一个身份登录。 |
| 授权的用户尝试登录，但输入错误的密码。 | 用户导航到应用程序的 URL，并接收不当的用户名/密码错误。 |
| 授权的用户单击一封电子邮件中的链接，并已通过身份验证。 | 用户单击 url 并登录到应用程序无需额外提示。 |
| 授权的用户单击电子邮件中的链接和未尚未经过身份验证。 | 用户单击 URL，为提示使用第一个身份进行身份验证。 |
| 授权用户登录到应用程序移动应用的应用程序 （SP 发起） 随新的登录尝试。 | 在 AD FS 服务器上的基于窗体的提示。 用户成功登录，并针对 MFA 提示 ADAL 客户端。 |
| 未经授权的用户尝试登录到应用程序使用登录 URL （SP 发起的）。 | 在 AD FS 服务器上的基于窗体的提示。 用户无法使用第一个身份登录。 |
| 授权的用户尝试登录，但输入错误的密码。| 用户导航到应用程序的 URL，并接收不当的用户名/密码错误。 |
| 授权的用户注销并再次登录。 | 如果配置注销 URL，则用户记录从所有服务和提示进行身份验证。 |
| 授权的用户注销并再次登录。 | 如果未配置注销 URL，则用户将自动记录在使用现有的 Azure AD 浏览器会话中的现有令牌中返回。 |
| 授权的用户单击一封电子邮件中的链接，并已通过身份验证。 | 用户单击 url 并登录到应用程序无需额外提示。 |
| 授权的用户单击电子邮件中的链接和未尚未经过身份验证。 | 用户单击 URL，为提示使用第一个身份进行身份验证。 |

## <a name="manage-sso"></a>管理 SSO

本部分概述的要求和建议，若要成功管理 SSO。

### <a name="required-administrative-roles"></a>所需的管理角色

始终使用与可用于完成在 Azure Active Directory 所需的任务的最少权限的角色。 Microsoft 建议[查看可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)和选择适合解决你的需求为每个角色为此应用程序。 某些角色可能需要暂时应用并在部署完成后删除。

| 角色| 角色 | Azure AD 角色 （如果需要） |
|--------|-------|-----------------------------|
| 帮助支持人员管理员 | 第 1 层支持 | 无 |
| 标识管理员 | 配置和调试时的问题会影响 Azure AD | 全局管理员 |
| 应用程序管理员 | 在应用程序上具有权限的用户的配置中的用户证明 | 无 |
| 基础结构管理员 | 证书滚动更新所有者 | 全局管理员 |
| 业务所有者/利益干系人 | 在应用程序上具有权限的用户的配置中的用户证明 | 无 |

我们建议使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) 来管理你的角色，以提供其他审核、 控制和访问检查对于具有目录权限的用户。

### <a name="sso-certificate-lifecycle-management"></a>SSO 证书生命周期管理

务必要确定适当的角色和电子邮件通讯组列表执行任务的 Azure AD 之间的签名证书的生命周期管理和应用程序正在配置使用单一登录。 下面是一些我们建议使用就地的重要作用：

- 更新应用程序中的用户属性的所有者
- 所有者电话技术支持的应用程序的故障维修服务支持
- 与证书相关的更改通知的严密监控的电子邮件通讯组列表

证书的最大生存期是三年。 我们建议建立上如何将处理证书更改 Azure AD 之间的进程和应用程序。 这可以帮助防止或最大程度减少由于证书过期而发生服务中断或强制执行证书滚动更新。

### <a name="rollback-process"></a>回滚进程

完成测试基于测试用例后，就可以移动到生产环境且应用程序。 移动到生产环境意味着将在生产租户中实现你的计划和测试配置，并将其扩展到你的用户。 但是，务必要计划要在你的部署不会按计划的情况下执行的操作。 如果在部署过程失败的 SSO 配置，必须了解如何减少任何服务中断和减小对用户影响。

在应用程序中的身份验证方法的可用性将确定最佳策略。 请始终确保您具有详细如何在你的部署遇到问题的情况下会与原始登录名配置状态的应用程序所有者的文档。

- **如果您的应用程序支持多个标识提供者**、 示例 LDAP 和 AD FS 和 Ping 操作，不会删除在部署阶段的现有 SSO 配置。 相反，将其禁用在迁移期间以防您需要将其切换回更高版本。 

- **如果您的应用程序不支持多个 Idp**但允许用户使用基于窗体的身份验证 （用户名/密码） 登录，请确保这种方法的新的 SSO 配置推出失败的情况下，用户可以可回退。

### <a name="access-management"></a>访问管理

我们建议选择一种缩放的方法，管理对资源的访问时。 常用的方法包括通过 Azure AD Connect 同步使用的本地组[基于用户属性在 Azure AD 中创建动态组](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或创建[自助服务组](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)Azure AD 中由资源所有者管理。

### <a name="monitor-security"></a>监视安全性

我们建议设置在其中查看 SaaS 应用程序安全的不同方面以一般的步调和执行任何所需的更正操作。

### <a name="troubleshooting"></a>故障排除

以下链接提供的故障排除方案。 您可能想要创建您的支持人员，其中包含这些方案和步骤来解决这些问题的特定指南。

#### <a name="consent-issues"></a>许可问题

- [出现的意外许可错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [用户许可错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登录问题

- [登录自定义门户中出现的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [从访问面板登录时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [应用程序登录页上的错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登录到 Microsoft 应用程序的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>在 Azure 应用程序库中列出的应用程序的 SSO 问题

- [在 Azure 应用程序库中列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [有关在 Azure 应用程序库中列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>未在 Azure 应用程序库中列出的应用程序的 SSO 问题

- [替换为 Azure 应用程序库中未列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [对于未在 Azure 应用程序库中列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>后续步骤

[调试基于 SAML 的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[通过 PowerShell 的应用的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[单一注销 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) （适用于如合作伙伴和供应商的外部用户）

[Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 访问](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[SSO 的应用程序教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
