---
title: 规划 Azure 活动目录单一登录部署
description: 帮助您在组织中规划、部署和管理 SSO 的指南。
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
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512821"
---
# <a name="plan-a-single-sign-on-deployment"></a>计划单一登录部署

单一登录 （SSO） 意味着仅使用单个用户帐户登录一次即可访问用户需要的所有应用程序和资源。 使用 SSO，用户可以访问所有必需的应用程序，而无需进行第二次身份验证。

## <a name="benefits-of-sso"></a>SSO 的优势

当用户登录到 Azure 活动目录 （Azure AD） 中的应用程序时，单一登录 （SSO） 增加了安全性和便利性。 

许多组织依赖软件即服务 （SaaS） 应用程序（如 Office 365、Box 和 Salesforce）来提高最终用户的工作效率。 从历史上看，IT 人员需要在每个 SaaS 应用程序中单独创建和更新用户帐户，用户需要记住每个应用程序的密码。

Azure 应用商店具有 3000 多个具有预集成 SSO 连接的应用程序，因此可以轻松地将它们集成到租户中。

## <a name="licensing"></a>授权

- **Azure AD 许可**- 用于预集成 SaaS 应用程序的 SSO 是免费的。 但是，目录中的对象数和要部署的功能可能需要其他许可证。 有关许可证要求的完整列表，请参阅[Azure 活动目录定价](https://azure.microsoft.com/pricing/details/active-directory/)。
- **应用程序许可**- 您需要为 SaaS 应用程序提供适当的许可证，以满足您的业务需求。 与应用程序所有者合作，确定分配给应用程序的用户是否具有适合其在应用程序中角色的许可证。 如果 Azure AD 根据角色管理自动预配，则 Azure AD 中分配的角色必须与应用程序中拥有的许可证数一致。 应用程序中拥有的许可证数量不正确可能会导致用户预配/更新期间出错。

## <a name="plan-your-sso-team"></a>规划您的 SSO 团队

- **吸引合适的利益相关者**- 当技术项目失败时，通常是由于对影响、结果和责任的期望不匹配。 为了避免这些陷阱，[请确保您与合适的利益相关者接洽](https://aka.ms/deploymentplans)，并确保利益相关者了解他们的角色。
- **计划通信**- 通信对于任何新服务的成功都至关重要。 主动向用户传达他们的体验将如何变化、何时更改以及遇到问题时如何获得支持。 查看[最终用户如何访问其启用 SSO 的应用程序](end-user-experiences.md)的选项，并制作您的通信以匹配您的选择。 

## <a name="plan-your-sso-protocol"></a>规划您的 SSO 协议

基于联合协议的 SSO 实现提高了安全性、可靠性和最终用户体验，并且更易于实现。 许多应用程序都预先集成在 Azure AD 中，并提供[分步指南](../saas-apps/tutorial-list.md)。 您可以在我们的[Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/)中找到它们。 有关每个 SSO 方法的详细信息，请参阅 Azure[活动目录中的应用程序单一登录](what-is-single-sign-on.md)一文。

可通过两种主要方式使用户能够单一登录应用：

- **具有联合单登录**Azure AD 使用其 Azure AD 帐户对用户进行身份验证。 此方法支持支持 SAML 2.0、WS-联合或 OpenID 连接等协议的应用程序，并且是单一登录最丰富的模式。 我们建议在应用程序支持 Azure AD 时将联合 SSO 与 Azure AD 一起使用，而不是使用基于密码的 SSO 和 ADFS。

- **使用基于密码的单一登录**用户首次使用用户名和密码登录到应用程序时，他们可以访问该应用程序。 首次登录后，Azure AD 会为应用程序提供用户名和密码。 基于密码的单一登录允许使用 Web 浏览器扩展插件或移动应用安全存储和重放应用程序的密码。 此选项利用应用程序提供的现有登录过程，使管理员能够管理密码，并且不需要用户知道密码。

### <a name="considerations-for-federation-based-sso"></a>基于联合的 SSO 的注意事项

- **使用 OpenID 连接和 OAuth** - 如果您要连接到的应用程序支持它，请使用 OIDC/OAuth 2.0 方法将您的 SSO 启用该应用程序。 此方法需要较少的配置，并可实现更丰富的用户体验。 有关详细信息，请参阅[OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md) [、OpenID 连接 1.0](../develop/v2-protocols-oidc.md)和[Azure 活动目录开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **基于 SAML 的端点配置**- 如果您使用 SAML，则开发人员在配置应用程序之前将需要特定信息。 有关详细信息，请参阅[编辑基本 SAML 配置](configure-single-sign-on-non-gallery-applications.md)。
- **基于 SAML 的 SSO 证书管理**- 当您为应用程序启用联合 SSO 时，Azure AD 会创建默认有效期为三年的证书。 如果需要，您可以自定义该证书的到期日期。 确保您有在证书过期之前续订证书的流程。 要了解更多信息，请参阅[Azure AD 管理证书](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>基于密码的 SSO 的注意事项

使用 Azure AD 进行基于密码的 SSO 需要部署浏览器扩展，该扩展将安全地检索凭据并填写登录表单。 定义一种机制，以便使用[受支持的浏览器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大规模部署扩展。 选项包括：

- [Internet 资源管理器的组策略](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [互联网资源管理器的配置管理器](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome、火狐、微软边缘或 IE 的用户驱动下载和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

要了解更多信息，请参阅[如何配置密码单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>捕获不在库中的应用程序的登录表单元数据

Microsoft 支持在 Web 应用程序上捕获元数据以进行密码保管（捕获用户名和密码字段）。 在配置应用程序以捕获表单元数据的过程中导航到登录 URL。 向应用程序所有者询问确切的登录 URL。 此信息在登录过程中使用，在登录期间将 Azure AD 凭据映射到应用程序。

要了解更多信息，请参阅[什么是使用 Azure AD 的应用程序访问和 SSO？](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>指示需要重新捕获表单中的元数据

当应用程序更改其 HTML 布局时，您可能需要重新捕获元数据以调整更改。 指示 HTML 布局已更改的常见症状包括：

- 用户报告单击应用程序会"卡住"登录页
- 报告用户名或密码未填充的用户

#### <a name="shared-accounts"></a>共享的帐户

从登录的角度来看，具有共享帐户的应用程序与为单个用户使用密码 SSO 的库应用程序没有区别。 但是，在规划和配置旨在使用共享帐户的应用程序时，还需要执行一些其他步骤：

1. 与应用程序业务用户合作记录以下内容：
   1. 组织中将使用应用程序的用户集
   1. 与用户集关联的应用程序中的现有凭据集 
1. 对于用户集和凭据的每个组合，根据您的要求在云或本地创建安全组。
1. 重置共享凭据。 在 Azure AD 中部署应用后，个人不需要共享帐户的密码。 由于 Azure AD 将存储密码，因此请考虑将其设置为非常长且复杂。 
1. 如果应用程序支持密码，则配置密码的自动翻转。 这样，即使是进行初始设置的管理员也不会知道共享帐户的密码。 

## <a name="plan-your-authentication-method"></a>规划身份验证方法

选择正确的身份验证方法是设置 Azure AD 混合标识解决方案至关重要的第一个决定。 请实现通过使用 Azure AD Connect（还可在云中预配用户）配置的身份验证方法。

若要选择身份验证方法，需要考虑时间、现有基础结构、复杂性和实现所选内容的成本。 这些因素对每个组织都不同，并可能随时间变化。 您应该选择最符合特定方案的方案。 有关详细信息，请参阅为[Azure 活动目录混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)。

## <a name="plan-your-security-and-governance"></a>规划安全性和治理 

身份是安全关注和投资的新的主要支点，因为随着 BYOD 设备和云应用程序的爆炸式发展，网络外围变得越来越多孔，效率也越来越低。 

### <a name="plan-access-reviews"></a>规划访问审核

[访问审核](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)使组织能够高效地管理组成员身份、访问企业应用程序和角色分配。 您应该计划定期查看用户访问权限，以确保只有合适的人员才能继续访问。

设置访问审核时要规划的一些关键主题包括：

1. 确定适合您业务需求的访问审核的节奏。 这可能像每周、每月、每年或按需练习一样频繁。

1. 创建表示应用访问报表审阅者的组。 您需要确保最熟悉应用及其目标用户和用例的利益相关者是访问审核的参与者

1. 完成访问审核包括取消不再需要访问权限的用户的应用访问权限。 计划处理来自被拒绝用户的潜在支持请求。 已删除的用户将在 Azure AD 中保留 30 天，在此期间，如有必要，管理员可以还原这些用户。 30 天后，该用户将被永久删除。 使用 Azure 活动目录门户，全局管理员可以在达到该时间段之前显式永久删除最近删除的用户。

### <a name="plan-auditing"></a>计划审核

Azure AD 提供[包含技术和业务见解的报告](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 

安全和活动报告都可用。 安全报告显示标记为风险的用户，以及风险登录。 活动报告通过详细说明登录活动并提供所有登录的审核跟踪，帮助您了解组织中用户的行为。 您可以使用报表来管理风险、提高工作效率和监控合规性。

| 报告类型 | 访问审核 | 安全报表 | 登录报告 |
|-------------|---------------|------------------|----------------|
| 用于查看 | 应用程序权限和使用情况。 | 可能受损的帐户 | 谁正在访问应用程序 |
| 潜在行动 | 审计访问;撤消权限 | 撤销访问权限;强制安全重置 | “撤销访问权限” |

Azure AD 会保留大多数审核数据 30 天，并通过 Azure 管理门户或 API 提供数据，以便下载到分析系统中。

### <a name="consider-using-microsoft-cloud-application-security"></a>考虑使用微软云应用程序安全性

微软云应用安全 （MCAS） 是云访问安全代理 （CASB） 解决方案。 它使您能够了解云应用和服务，提供复杂的分析以识别和打击网络威胁，并使您能够控制数据传输方式。

部署 MCAS 使您能够：

- 使用"云发现"映射和标识云环境以及您的组织正在使用的云应用。
- 在云中制裁和取消制裁应用
- 使用易于部署的应用连接器，利用提供商 API，查看和治理您连接到的应用
- 使用条件访问应用控制保护，获得实时可见性并控制云应用中的访问和活动
- 通过设置策略，然后持续微调策略，帮助您获得持续控制。

Microsoft 云应用程序安全 （MCAS） 会话控件可用于任何操作系统上任何主要平台上的任何浏览器。 也可阻止或允许移动应用和桌面应用。 通过本机与 Azure AD 集成，可以支持任何配置 SAML 的应用，或支持在 Azure AD 中单一登录的"打开 ID 连接"应用，包括[多个特色应用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)。

有关 MCAS 的信息，请参阅[Microsoft 云应用安全概述](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是一种基于用户的订阅服务。 您可以在[MCAS 许可数据表中](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)查看许可详细信息。

### <a name="use-conditional-access"></a>使用条件访问

借助条件访问，您可以自动为云应用做出基于条件的访问控制决策。

完成第一因素身份验证后将强制执行条件访问策略。 因此，条件访问不是针对拒绝服务 （DoS） 攻击等方案的第一线防御，而是可以使用这些事件发出的信号来确定访问。 例如，可以使用登录风险级别、请求的位置等。 有关条件访问的详细信息[，请参阅概述](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)和[部署计划](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure SSO 技术要求

以下部分详细介绍了配置特定应用程序的要求，包括必要的环境、终结点、声明映射、所需属性、证书和所使用的协议。 您需要此信息才能在[Azure AD 门户](https://portal.azure.com/)中配置 SSO。

### <a name="authentication-mechanism-details"></a>身份验证机制详细信息

对于所有预集成的 SaaS 应用，Microsoft 提供了教程，您不需要此信息。 如果应用程序不在我们的应用程序市场/库中，您可能需要收集以下数据片段：

- **应用程序为 SSO 使用的当前标识提供程序（如果适用）** - 例如：AD FS、Ping Federate、Okta
- **目标应用程序支持的协议**- 例如，SAML 2.0、OpenID 连接、OAuth、基于表单的 Auth、WS-Fed、WS-Trust
- **协议配置 Azure AD** - 例如，SAML 2.0 或 1.1、OpenID 连接、OAuth、基于窗体的 WS-Fed

### <a name="attribute-requirements"></a>属性要求

Azure AD 用户对象和每个 SaaS 应用的用户对象之间有一组预先配置的属性和属性映射。 某些应用管理其他类型的对象，如组。 规划从 Azure AD 到应用程序的用户属性映射，并根据业务需求[自定义默认属性映射](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)。

### <a name="certificate-requirements"></a>证书要求

应用程序的证书必须是最新的，否则用户可能会无法访问该应用程序。 大多数 SaaS 应用程序证书的 36 个月都适用。 在应用程序边栏选项卡中更改该证书持续时间。 请务必记录过期情况，并了解如何管理证书续订。 

有两种方法可以管理证书。 

- **自动证书滚动**- 微软支持[在 Azure AD 中签名密钥滚动。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) 虽然这是我们管理证书的首选方法，但并非所有 ISV 都支持此方案。

- **手动更新**- 每个应用程序都有自己的证书，该证书会根据定义方式过期。 在应用程序的证书过期之前，请创建新证书并将其发送到 ISV。 可以从联合元数据提取此信息。 [在此处阅读有关联合元数据的更多内容。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>实现 SSO

使用以下阶段规划和部署组织中的解决方案：

### <a name="user-configuration-for-sso"></a>SSO 的用户配置

- **识别测试用户**

   联系应用所有者，要求他们在应用程序中至少创建三个测试用户。 确保正确填充主标识符时使用的信息，并匹配 Azure AD 中可用的属性。 在大多数情况下，这将映射到基于 SAML 的应用程序的"NameID"。 对于 JWT 令牌，它是"preferred_username"。
   
   在 Azure AD 中手动创建用户作为基于云的用户，或使用 Azure AD 连接同步引擎从本地同步用户。 确保信息与发送到应用程序的声明匹配。

- **配置 SSO**

   从[应用程序列表中](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)，查找并打开应用程序的 SSO 教程，然后按照本教程的步骤成功配置 SaaS 应用程序。

   如果找不到应用程序，请参阅[自定义应用程序文档](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 这将引导您了解如何添加不在 Azure AD 库中的应用程序。

   或者，您可以使用[Microsoft 的指导文档](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)为企业应用程序使用 SAML 令牌中发出的声明。 确保此映射到您希望在应用程序的 SAML 响应中收到的内容。 如果您在配置过程中遇到问题，请使用有关如何[调试 SSO 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)的指导。

自定义应用程序载入是 Azure AD 高级 P1 或 P2 许可证功能。

### <a name="provide-sso-change-communications-to-end-users"></a>向最终用户提供 SSO 更改通信

实施沟通计划。 确保您让最终用户知道更改即将出现、更改何时到达、现在该怎么办以及如何寻求帮助。

### <a name="verify-end-user-scenarios-for-sso"></a>验证 SSO 的最终用户方案

您可以使用以下测试用例对公司拥有的设备和个人设备进行测试，以确保 SSO 配置按预期工作。 以下方案假定用户正在导航到应用程序 URL 并遍达由服务提供商启动的身份验证流（SP 启动身份验证流）。

| 方案 | 按用户对 SP 启动的 auth 流的预期结果 |
|----------|---------------------------------------------------|
| 在公司网络上登录 IE 应用程序。 | 集成的 Windows 身份验证 （IWA） 在没有其他提示时发生。 |
| 使用 IE 登录到应用程序，同时使用新的登录尝试关闭公司网络。 | AD FS 服务器上的基于表单的提示。 用户成功登录并提示 MFA。 |
| 使用 IE 登录到应用程序，同时使用当前会话关闭公司网络，并且从未执行 MFA。 | 用户不会收到第一个因素的提示。 用户收到 MFA 的提示。 |
| 使用 IE 登录到应用程序，同时使用当前会话关闭公司网络，并且已在此会话中执行 MFA。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSO 进入应用程序。 |
| 使用 Chrome/Firefox/Safari 登录应用程序，同时使用当前会话关闭公司网络，并且已在此会话中执行 MFA。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSO 进入应用程序。 |
| 登录进入应用程序与Chrome/火狐/Safari，而关闭公司网络与新的登录尝试。 | AD FS 服务器上的基于表单的提示。 用户成功登录并提示 MFA。 |
| 在企业网络上使用 Chrome/Firefox 在当前会话登录应用程序。 | 用户不会收到第一个因素的提示。 用户不会收到 MFA。 用户 SSO 进入应用程序。 |
| 使用新的登录尝试使用应用程序移动应用程序登录到应用程序。 | AD FS 服务器上的基于表单的提示。 用户成功登录，ADAL 客户端提示 MFA。 |
| 未经授权的用户尝试使用登录 URL 登录应用程序。 | AD FS 服务器上的基于表单的提示。 用户无法使用第一个因素登录。 |
| 授权用户尝试登录但输入不正确的密码。 | 用户导航到应用程序 URL 并收到错误的用户名/密码错误。 |
| 授权用户单击电子邮件中的链接，并已通过身份验证。 | 用户单击 URL 并登录到应用程序中，没有其他提示。 |
| 授权用户单击电子邮件中的链接，但尚未经过身份验证。 | 用户单击 URL 并提示使用第一个因素进行身份验证。 |
| 授权用户使用应用程序移动应用程序 （SP 启动） 登录应用程序，并尝试新的登录。 | AD FS 服务器上的基于表单的提示。 用户成功登录，ADAL 客户端提示 MFA。 |
| 未经授权的用户尝试使用登录 URL （SP 启动） 登录应用程序。 | AD FS 服务器上的基于表单的提示。 用户无法使用第一个因素登录。 |
| 授权用户尝试登录但输入不正确的密码。| 用户导航到应用程序 URL 并收到错误的用户名/密码错误。 |
| 授权用户注销，然后再次登录。 | 如果配置了注销 URL，则用户将注销所有服务并提示进行身份验证。 |
| 授权用户注销，然后再次登录。 | 如果未配置注销 URL，用户将自动使用现有 Azure AD 浏览器会话中的现有令牌重新登录。 |
| 授权用户单击电子邮件中的链接，并已通过身份验证。 | 用户单击 URL 并登录到应用程序中，没有其他提示。 |
| 授权用户单击电子邮件中的链接，但尚未经过身份验证。 | 用户单击 URL 并提示使用第一个因素进行身份验证。 |

## <a name="manage-sso"></a>管理 SSO

本节概述了成功管理 SSO 的要求和建议。

### <a name="required-administrative-roles"></a>所需的管理角色

始终使用具有最少可用权限的角色，以完成 Azure 活动目录中所需的任务。 Microsoft 建议[查看可用的不同角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)并选择正确的角色来解决此应用程序每个角色的需求。 某些角色可能需要临时应用并在部署完成后删除。

| 角色| 角色 | Azure AD 角色（如果需要） |
|--------|-------|-----------------------------|
| 帮助台管理员 | 第 1 层支持 | 无 |
| 身份管理员 | 当问题影响 Azure AD 时配置和调试 | 全局管理员 |
| 应用程序管理员 | 用户在应用程序中的证明，对具有权限的用户的配置 | 无 |
| 基础架构管理员 | 证书滚存所有者 | 全局管理员 |
| 企业主/利益相关者 | 用户在应用程序中的证明，对具有权限的用户的配置 | 无 |

我们建议使用[特权标识管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)（PIM） 来管理您的角色，为具有目录权限的用户提供其他审核、控制和访问审查。

### <a name="sso-certificate-lifecycle-management"></a>SSO 证书生命周期管理

确定负责管理 Azure AD 和正在配置单一登录的应用程序之间的签名证书生命周期的正确角色和电子邮件通讯组列表非常重要。 以下是我们建议的一些关键角色：

- 用于更新应用程序中的用户属性的所有者
- 用户随叫随到，以寻求应用程序中断/修复支持
- 密切监视与证书相关的更改通知的电子邮件通讯组列表

证书的最大生存期为三年。 我们建议建立一个过程，介绍如何处理 Azure AD 和应用程序之间的证书更改。 这有助于防止或最小化由于证书过期或强制证书滚动而中断。

### <a name="rollback-process"></a>回滚过程

完成基于测试用例的测试后，是时候随应用程序进入生产。 迁移到生产意味着您将在生产租户中实现计划配置和测试配置，并将其推广到用户。 但是，请务必规划在部署未按计划进行的情况下该怎么做。 如果 SSO 配置在部署期间失败，您必须了解如何缓解任何中断并减少对用户的影响。

应用程序中身份验证方法的可用性将决定最佳策略。 始终确保为应用所有者提供详细的文档，说明在部署遇到问题时如何回到原始登录配置状态。

- **如果应用支持多个标识提供程序**（例如 LDAP 和 AD FS 和 Ping），则在推出期间不要删除现有的 SSO 配置。 而是在迁移期间禁用它，以防以后需要将其切换回来。 

- **如果应用不支持多个 IDP，** 但允许用户使用基于表单的身份验证（用户名/密码）登录，请确保用户可以在新的 SSO 配置推出失败时回退此方法。

### <a name="access-management"></a>访问管理

我们建议在管理对资源的访问时选择缩放的方法。 常见方法包括通过 Azure AD 连接同步来利用本地组，[根据用户属性在 Azure AD 中创建动态组](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或在资源所有者管理的 Azure AD 中创建[自助服务组](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)。

### <a name="monitor-security"></a>监控安全性

我们建议设置常规节奏，其中查看 SaaS 应用安全性的不同方面，并执行所需的任何补救措施。

### <a name="troubleshooting"></a>疑难解答

以下链接存在故障排除方案。 您可能需要为支持人员创建一个特定的指南，其中包含这些方案和修复它们的步骤。

#### <a name="consent-issues"></a>同意问题

- [意外同意错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [“用户同意”错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登录问题

- [从自定义门户登录时遇到问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [从访问面板登录时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [应用程序登录页上的错误](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登录到 Microsoft 应用程序时出现问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 应用程序库中列出的应用程序的 SSO 问题

- [Azure 应用程序库中列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 应用程序库中列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 应用程序库中未列出的应用程序的 SSO 问题

- [Azure 应用程序库中未列出的应用程序的密码 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 应用程序库中未列出的应用程序的联合 SSO 问题](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>后续步骤

[调试基于 SAML 的 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[通过 PowerShell 应用的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自定义在 SAML 令牌中发出的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[单登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[单签出 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B（](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)适用于外部用户（如合作伙伴和供应商）

[Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure 标识保护](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 访问](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[应用程序 SSO 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
