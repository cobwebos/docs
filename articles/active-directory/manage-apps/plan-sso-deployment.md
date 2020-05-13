---
title: 规划 Azure Active Directory 单一登录部署
description: 帮助你在组织中规划、部署和管理 SSO 的指南。
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
ms.custom: has-adal-ref
ms.openlocfilehash: 4b5836ffe721aa3e329651c3709ce64344363728
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197875"
---
# <a name="plan-a-single-sign-on-deployment"></a>计划单一登录部署

单一登录（SSO）是指只需使用单个用户帐户登录一次即可访问用户所需的所有应用程序和资源。 使用 SSO，用户可以访问所有所需的应用程序，而无需再次进行身份验证。

## <a name="benefits-of-sso"></a>SSO 的优点

当用户登录到 Azure Active Directory （Azure AD）中的应用程序时，单一登录（SSO）可增加安全性和便利性。 

许多组织依赖软件即服务（SaaS）应用程序（如 Office 365、Box 和 Salesforce）来提高最终用户的工作效率。 从历史上看，IT 人员需要在每个 SaaS 应用程序中单独创建和更新用户帐户，并且用户需要记住每个应用程序的密码。

Azure Marketplace 具有超过3000个具有预先集成 SSO 连接的应用程序，因此可以轻松地将它们集成到租户中。

## <a name="licensing"></a>授权

- **Azure AD 的授权**-SSO 适用于预先集成的 SaaS 应用程序。 但是，目录中的对象数以及要部署的功能可能需要额外的许可证。 有关许可证要求的完整列表，请参阅[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。
- **应用程序授权**-你将需要适用于你的 SaaS 应用程序的许可证，以满足你的业务需求。 与应用程序所有者合作，确定分配到应用程序的用户是否具有其在应用程序中的角色的相应许可证。 如果 Azure AD 基于角色管理自动预配，则在 Azure AD 中分配的角色必须与应用程序中拥有的许可证数量一致。 应用程序中拥有的许可证数量不正确可能会导致在用户的预配/更新过程中出现错误。

## <a name="plan-your-sso-team"></a>规划 SSO 团队

- **吸引正确的利益干系人**-当技术项目发生故障时，这通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题，请[确保你参与了正确的利益干系人](https://aka.ms/deploymentplans)，并且利益干系人了解他们的角色。
- **规划通信**-通信对任何新服务的成功至关重要。 主动向用户传达其体验如何发生更改、何时发生更改，以及如何在遇到问题时获得支持。 查看[最终用户将如何访问启用 SSO 的应用程序](end-user-experiences.md)的选项，并创建你的通信以匹配你的选择。 

## <a name="plan-your-sso-protocol"></a>规划 SSO 协议

基于联合身份验证协议的 SSO 实现可以提高安全性、可靠性和最终用户体验，并且更容易实现。 许多应用程序都是在 Azure AD 中预先集成的，其中[提供了循序渐进指南](../saas-apps/tutorial-list.md)。 可以在我们的[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)中找到它们。 有关每种 SSO 方法的详细信息，请参阅文章[单一登录到应用程序中的 Azure Active Directory](what-is-single-sign-on.md)。

可通过两种主要方式使用户能够单一登录到应用：

- **具有联合单一登录**Azure AD 使用 Azure AD 帐户对应用程序进行身份验证。 支持 SAML 2.0、WS 联合身份验证或 OpenID Connect 等协议的应用程序支持此方法，它是单一登录的最丰富模式。 建议在应用程序支持时使用联合 SSO 和 Azure AD，而不是基于密码的 SSO 和 ADFS。

- **使用基于密码的单一登录**用户首次登录应用程序时，他们将访问该用户名和密码。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 基于密码的单一登录允许使用 Web 浏览器扩展插件或移动应用安全存储和重放应用程序的密码。 此选项利用应用程序提供的现有登录过程，使管理员能够管理密码，而不要求用户知道密码。

### <a name="considerations-for-federation-based-sso"></a>基于联合的 SSO 的注意事项

- **使用 OpenID connect 和 OAuth** -如果要连接的应用程序支持该应用程序，请使用 OIDC/OAuth 2.0 方法将 SSO 启用到该应用程序。 此方法需要较少的配置，并支持更丰富的用户体验。 有关详细信息，请参阅[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)、 [OpenID connect 1.0](../develop/v2-protocols-oidc.md)和[Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **基于 saml 的 SSO 的终结点配置**-如果使用 saml，则开发人员在配置应用程序之前将需要特定信息。 有关详细信息，请参阅[编辑基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md)。
- **基于 SAML 的 sso 的证书管理-为**应用程序启用联合 sso 时，Azure AD 会创建默认情况下有效期为三年的证书。 如果需要，可以自定义该证书的到期日期。 确保已准备好处理证书，以便在证书过期之前续订证书。 若要了解详细信息，请参阅[Azure AD 管理证书](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>基于密码的 SSO 的注意事项

使用基于密码的 SSO 的 Azure AD 需要部署一个浏览器扩展，该扩展将安全地检索凭据并填写登录窗体。 定义一种机制，用于使用[受支持的浏览器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大规模部署扩展。 选项包括：

- [Internet Explorer 组策略](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [用户驱动的 Chrome、Firefox、Microsoft Edge 或 IE 的下载和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

若要了解详细信息，请参阅[如何配置密码单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>捕获不在库中的应用程序的登录窗体元数据

Microsoft 支持在 web 应用程序上捕获用于密码保管的元数据（捕获 "用户名" 和 "密码" 字段）。 在配置应用程序的过程中导航到登录 URL，以捕获窗体元数据。 向应用程序所有者询问确切的登录 URL。 在登录过程中将使用此信息，在登录过程中将 Azure AD 凭据映射到应用程序。

若要了解详细信息，请参阅[什么是应用程序访问和 SSO 与 Azure AD？-基于密码的 sso](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>指示窗体中的元数据需要回收

当应用程序更改其 HTML 布局时，可能需要重新捕获要调整的元数据以进行更改。 指示 HTML 布局发生更改的常见症状包括：

- 报告单击该应用程序的用户会在登录页中 "停滞"
- 报告未填充用户名或密码的用户

#### <a name="shared-accounts"></a>共享的帐户

从登录的角度来看，使用共享帐户的应用程序不同于对单独用户使用密码 SSO 的库应用程序。 但是，在规划和配置应用程序使用共享帐户时，还需要执行一些其他步骤：

1. 与应用程序业务用户合作来记录以下内容：
   1. 组织中将使用应用程序的一组用户
   1. 应用程序中与一组用户关联的现有凭据集 
1. 对于用户集和凭据的每个组合，请根据你的要求，在云中或本地创建安全组。
1. 重置共享凭据。 将应用部署到 Azure AD 后，个人不需要共享帐户的密码。 由于 Azure AD 会存储密码，因此请考虑将其设置为非常长且复杂。 
1. 如果应用程序支持，则配置密码的自动滚动更新。 这样，即使是初始设置，管理员也不知道共享帐户的密码。 

## <a name="plan-your-authentication-method"></a>规划身份验证方法

选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 请实现通过使用 Azure AD Connect（还可在云中预配用户）配置的身份验证方法。

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 你应选择最符合你的特定方案的一个。 有关详细信息，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)。

## <a name="plan-your-security-and-governance"></a>规划您的安全和管理 

由于 BYOD 设备和云应用程序的急剧增长，网络外围网络变得越来越漏洞，而且效率较低，因此，标识是新的主数据中心。 

### <a name="plan-access-reviews"></a>规划访问评审

[访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)使组织能够有效地管理组成员身份、访问企业应用程序和角色分配。 你应计划定期查看用户访问权限，以确保只有正确的人员才能继续访问。

设置访问评审时要规划的一些关键主题包括：

1. 确定适合您的业务需求的访问评审的步调。 这可能是每周、每月、每年、每周或按需执行的频率。

1. 创建表示应用访问报表审阅者的组。 你需要确保最熟悉应用及其目标用户和用例的利益干系人是访问评审的参与者

1. 完成访问评审包括对不再需要访问权限的用户的应用程序访问权限。 计划处理来自拒绝用户的潜在支持请求。 已删除的用户将在 Azure AD 中保留30天，在此期间，管理员可以在必要时还原。 30 天后，该用户将被永久删除。 使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前显式删除最近删除的用户。

### <a name="plan-auditing"></a>规划审核

Azure AD 提供了[包含技术和业务见解的报表](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 

安全和活动报告都可用。 安全报表显示标记为有风险的用户和有风险的登录。活动报表通过详细说明登录活动，并提供所有登录名的审核线索，来帮助你了解组织中用户的行为。 你可以使用报表来管理风险、提高工作效率和监视符合性。

| 报告类型 | 访问评审 | 安全报表 | 登录报告 |
|-------------|---------------|------------------|----------------|
| 使用查看 | 应用程序权限和使用情况。 | 可能已泄露的帐户 | 谁在访问应用程序 |
| 可能的操作 | 审核访问;废除权限 | 撤消访问权限;强制重置安全 | “撤销访问权限” |

Azure AD 保留30天的大多数审核数据，并通过 Azure 管理门户或通过 API 获取数据，以便下载到分析系统。

### <a name="consider-using-microsoft-cloud-application-security"></a>考虑使用 Microsoft 云应用程序安全性

Microsoft Cloud App Security （MCAS）是一种云访问安全代理（CASB）解决方案。 它可让你查看云应用和服务，提供复杂分析来识别和对抗以来自2200，并使你能够控制数据的传播方式。

部署 MCAS 可让你：

- 使用 Cloud Discovery 来映射和确定你的组织正在使用的云环境和云应用。
- 云中的批准和批准应用
- 使用易于部署的应用连接器来利用提供程序 Api，以便查看和管理连接到的应用
- 使用条件访问应用控制保护，以实时查看和控制云应用中的访问和活动
- 通过设置，然后不断地调整策略，帮助你进行连续控制。

Microsoft 云应用程序安全性（MCAS）会话控制可用于任何操作系统上任何主要平台上的任何浏览器。 也可阻止或允许移动应用和桌面应用。 通过本机方式与 Azure AD 进行集成，可以支持在 Azure AD 中配置了 SAML 或 Open ID Connect 应用程序的任何应用，包括[多个特色应用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)。

有关 MCAS 的信息，请参阅[Microsoft Cloud App Security 概述](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是基于用户的订阅服务。 可以在[MCAS 授权数据表](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)中查看许可详细信息。

### <a name="use-conditional-access"></a>使用条件访问

使用条件性访问，你可以针对云应用自动执行基于条件的访问控制决策。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件性访问不应作为拒绝服务（DoS）攻击等方案的第一线防御，但可以使用来自这些事件的信号来确定访问权限。 例如，可以使用登录风险级别、请求的位置等等。 有关条件性访问的详细信息，请参阅[概述](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)和[部署计划](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure SSO 技术要求

以下部分详细介绍了配置特定应用程序的要求，其中包括所需的环境、终结点、声明映射、所需的属性、证书以及所使用的协议。 在[Azure AD 门户](https://portal.azure.com/)中配置 SSO 需要此信息。

### <a name="authentication-mechanism-details"></a>身份验证机制详细信息

对于所有预先集成的 SaaS 应用程序，Microsoft 提供教程，不需要此信息。 如果应用程序不在我们的应用程序 marketplace/库中，你可能需要收集以下数据片段：

- **应用程序使用的当前标识提供程序（如果适用）** -例如： AD FS、PingFederate、Okta
- **目标应用程序支持的协议**-例如，SAML 2.0、OpenID Connect、OAuth、基于窗体的身份验证、ws-federation、ws 信任
- **用 Azure AD 配置的协议**-例如，SAML 2.0 或1.1、OpenID Connect、OAuth、基于窗体的 WS 进纸

### <a name="attribute-requirements"></a>特性要求

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 某些应用管理其他类型的对象，如组。 计划将用户属性从 Azure AD 映射到应用程序，并根据业务需求[自定义默认属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。

### <a name="certificate-requirements"></a>证书要求

应用程序的证书必须是最新的，或者是用户无法访问该应用程序的风险。 大多数 SaaS 应用程序证书都适用于36个月。 您可以在应用程序边栏选项卡中更改该证书的持续时间。 请确保记录过期情况并了解如何管理证书续订。 

可以通过两种方法来管理证书。 

- **自动证书滚动更新**-Microsoft 支持[Azure AD 中的签名密钥滚动更新](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 虽然这是用于管理证书的首选方法，但并非所有 ISV 都支持此方案。

- **手动更新**-每个应用程序都有自己的基于定义方式过期的证书。 在应用程序的证书过期之前，请创建新的证书并将其发送到 ISV。 此信息可从联合元数据提取。 [在此处阅读有关联合元数据的详细信息。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>实现 SSO

使用以下阶段来规划和部署你的组织中的解决方案：

### <a name="user-configuration-for-sso"></a>SSO 的用户配置

- **确定测试用户**

   与应用所有者联系，要求他们在应用程序内创建至少三个测试用户。 确保正确填充你将用作主标识符的信息并匹配 Azure AD 中可用的属性。 在大多数情况下，这将映射到基于 SAML 的应用程序的 "NameID"。 对于 JWT 令牌，它是 "preferred_username"。
   
   在 Azure AD 中创建用户，将其作为基于云的用户手动创建，或使用 Azure AD Connect 同步引擎从本地同步用户。 确保信息与要发送到应用程序的声明匹配。

- **配置 SSO**

   在[应用程序列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)中，找到并打开应用程序的 SSO 教程，然后按照本教程中的步骤操作，成功配置 SaaS 应用程序。

   如果找不到应用程序，请参阅[自定义应用程序文档](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 这将引导你完成如何添加不位于 Azure AD 库中的应用程序。

   或者，你可以使用[Microsoft 指南文档](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)，为企业应用程序使用在 SAML 令牌中颁发的声明。 确保这会映射到你预期在应用程序的 SAML 响应中收到的内容。 如果在配置过程中遇到问题，请使用有关[如何调试 SSO 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)的指南。

自定义应用程序载入是 Azure AD Premium P1 或 P2 许可证功能。

### <a name="provide-sso-change-communications-to-end-users"></a>向最终用户提供 SSO 更改通信

实现您的通信计划。 请确保您的最终用户知道发生了更改、到达时、现在要做什么以及如何寻求帮助。

### <a name="verify-end-user-scenarios-for-sso"></a>验证 SSO 的最终用户方案

你可以使用以下测试用例在公司拥有的设备和个人设备上执行测试，以确保你的 SSO 配置按预期方式工作。 以下方案假设用户要导航到应用程序 URL，并通过服务提供商（SP 发起的身份验证流）启动的身份验证流。

| 方案 | 由用户启动的 SP 启动的身份验证流的预期结果 |
|----------|---------------------------------------------------|
| 在公司网络上登录到具有 IE 的应用程序。 | 集成的 Windows 身份验证（IWA）发生，无其他提示。 |
| 在关闭具有新登录尝试的公司网络的情况下，通过 IE 登录到应用程序。 | AD FS Server 上的基于窗体的提示。 用户成功登录并浏览器提示进行 MFA。 |
| 使用 IE 登录到应用程序，而不使用当前会话的公司网络，且从未执行过 MFA。 | 用户不会收到第一个因素的提示。 用户接收 MFA 提示。 |
| 使用 IE 登录到应用程序时，通过当前会话与公司网络断开，并已在此会话中执行 MFA。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSOs 到应用程序中。 |
| 使用 Chrome/Firefox/Safari 登录到应用程序时，通过当前会话与公司网络断开，并已在此会话中执行 MFA。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSO 到应用程序。 |
| 通过 Chrome/Firefox/Safari 登录到应用程序，同时利用新的登录尝试。 | AD FS Server 上的基于窗体的提示。 用户成功登录并浏览器提示进行 MFA。 |
| 使用当前会话在公司网络上使用 Chrome/Firefox 登录到应用程序。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSO 到应用程序。 |
| 使用新的登录尝试通过应用程序移动应用登录到应用程序。 | AD FS Server 上的基于窗体的提示。 用户成功登录，并且 ADAL 客户端提示进行 MFA。 |
| 未经授权的用户尝试通过登录 URL 登录到应用程序。 | AD FS Server 上的基于窗体的提示。 用户无法以首个因素登录。 |
| 授权的用户尝试登录，但输入的密码不正确。 | 用户导航到 "应用程序 URL" 并收到错误的用户名/密码错误。 |
| 授权的用户单击电子邮件中的链接，已经过身份验证。 | 用户单击 URL 并登录到应用程序，无其他提示。 |
| 授权的用户单击电子邮件中的链接，但尚未通过身份验证。 | 用户单击 "URL"，系统会提示第一个因素进行身份验证。 |
| 经授权的用户通过新的登录尝试，使用应用程序移动应用程序（SP 启动）登录应用程序。 | AD FS Server 上的基于窗体的提示。 用户成功登录，并且 ADAL 客户端提示进行 MFA。 |
| 未经授权的用户尝试通过登录 URL （SP 发起的）登录到应用程序。 | AD FS Server 上的基于窗体的提示。 用户无法以首个因素登录。 |
| 授权的用户尝试登录，但输入的密码不正确。| 用户导航到 "应用程序 URL" 并收到错误的用户名/密码错误。 |
| 授权的用户注销，然后再次登录。 | 如果配置了注销 URL，用户将从所有服务中注销，并提示进行身份验证。 |
| 授权的用户注销，然后再次登录。 | 如果未配置注销 URL，用户将使用现有的 Azure AD 浏览器会话中的现有令牌自动登录。 |
| 授权的用户单击电子邮件中的链接，已经过身份验证。 | 用户单击 URL 并登录到应用程序，无其他提示。 |
| 授权的用户单击电子邮件中的链接，但尚未通过身份验证。 | 用户单击 "URL"，系统会提示第一个因素进行身份验证。 |

## <a name="manage-sso"></a>管理 SSO

本部分概述了成功管理 SSO 的要求和建议。

### <a name="required-administrative-roles"></a>必需的管理角色

始终使用具有最少权限的角色来完成 Azure Active Directory 中所需的任务。 Microsoft 建议[查看可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)，并选择正确的角色以解决此应用程序的每个角色的需求。 在部署完成后，可能需要暂时应用某些角色并将其删除。

| 增添| 角色 | Azure AD 角色（如果需要） |
|--------|-------|-----------------------------|
| 咨询台管理员 | 第1层支持 | 无 |
| 标识管理员 | 在问题影响时进行配置和调试 Azure AD | 全局管理员 |
| 应用程序管理员 | 应用程序中的用户证明，具有权限的用户配置 | 无 |
| 基础结构管理员 | 证书滚动更新所有者 | 全局管理员 |
| 业务所有者/利益干系人 | 应用程序中的用户证明，具有权限的用户配置 | 无 |

建议使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) （PIM）来管理角色，以便为具有目录权限的用户提供其他审核、控制和访问权限。

### <a name="sso-certificate-lifecycle-management"></a>SSO 证书生命周期管理

重要的是，确定正确的角色和电子邮件分发列表，以管理签名证书生命周期的工作，以及通过单一登录配置的应用程序 Azure AD。 下面是我们建议采用的一些关键角色：

- 在应用程序中更新用户属性的所有者
- 所有者待命-针对应用程序中断/修复的支持
- 与证书相关的更改通知的密切监视的电子邮件通讯组列表

证书的最长生存期为三年。 建议确定如何处理 Azure AD 与应用程序之间的证书更改。 这可以帮助防止或最大程度地减少因证书过期或强制执行证书滚动更新导致的中断。

### <a name="rollback-process"></a>回滚进程

完成基于测试用例的测试后，可以通过应用程序迁移到生产环境。 转移到生产意味着你将在生产租户中实现计划的已测试配置，并将其部署到你的用户。 但是，如果你的部署不按计划进行，则必须规划要执行的操作。 如果在部署过程中 SSO 配置失败，你必须了解如何缓解任何中断并降低对用户的影响。

应用程序中的身份验证方法的可用性将决定您的最佳策略。 请始终确保应用所有者详细地了解如何在部署遇到问题时返回到原始登录配置状态。

- **如果你的应用程序支持多个标识提供程序**（例如 LDAP 和 AD FS 和 Ping），请不要在部署期间删除现有的 SSO 配置。 相反，请在迁移过程中禁用它，以防以后需要切换回。 

- **如果应用不支持多个 idp** ，但允许用户使用基于窗体的身份验证（用户名/密码）登录，请确保在新的 SSO 配置部署失败的情况下，用户可以回退到此方法。

### <a name="access-management"></a>访问管理

建议在管理对资源的访问权限时选择缩放的方法。 常见的方法包括通过使用 Azure AD Connect 进行同步、[基于用户属性在 Azure AD 中创建动态组](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或在资源所有者管理 Azure AD 中创建[自助服务组](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，从而利用本地组。

### <a name="monitor-security"></a>监视安全性

建议设置定期节奏，以便查看 SaaS 应用安全性的不同方面，并执行所需的任何补救措施。

### <a name="troubleshooting"></a>疑难解答

以下链接显示故障排除方案。 你可能想要为你的支持人员创建特定指南，其中包含这些方案和修复这些方案的步骤。

#### <a name="consent-issues"></a>同意问题

- [意外同意错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [“用户同意”错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登录问题

- [从自定义门户登录时遇到的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [从访问面板登录时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [应用程序登录页上的错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登录 Microsoft 应用程序时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 应用程序库中列出的应用程序的 SSO 问题

- [Azure 应用程序库中列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 应用程序库中列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 应用程序库中未列出的应用程序的 SSO 问题

- [Azure 应用程序库中未列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 应用程序库中未列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>后续步骤

[调试基于 SAML 的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[通过 PowerShell 为应用声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[单一注销 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) （对于外部用户，如合作伙伴和供应商）

[Azure AD 条件性访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 访问](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[应用程序 SSO 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
