---
title: 在 Azure Active Directory 中保护标识基础结构的五个步骤
description: 本文档概述了管理员应该实现的重要操作的列表，以帮助他们使用 Azure AD 功能保护其组织
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: 870bb9720500b6eda5e7b9eb258b6764a94f01b6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903586"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>保护标识基础结构的五个步骤

如果您阅读本文档，您就会注意到安全性的重要性。 你可能已经承担保护组织的责任。 如果需要说服其他人的安全重要性，请将其发送到阅读最新的[Microsoft 安全智能报告](https://go.microsoft.com/fwlink/p/?linkid=2073747)。

本文档将帮助你通过使用5个步骤的清单帮助你的组织免受网络攻击，帮助你获得更 Azure Active Directory 安全的状态。

此清单将帮助你快速部署关键建议的操作，以立即通过说明如何执行以下操作来保护组织：

* 增强凭据。
* 减小攻击面。
* 自动进行威胁响应。
* 利用云智能。
* 启用最终用户自助服务。

在阅读此清单时，请确保跟踪已完成的功能和步骤。

> [!NOTE]
> 本文档中的许多建议仅适用于配置为使用 Azure Active Directory 作为标识提供者的应用程序。 为单一登录配置应用可确保凭据策略、威胁检测、审核、日志记录以及其他功能的优点添加到这些应用程序中。 [通过 Azure Active Directory 的单一登录](../../active-directory/manage-apps/configure-single-sign-on-portal.md)是所有这些建议所基于的基础。

本文档中的建议与[标识安全分数](../../active-directory/fundamentals/identity-secure-score.md)（Azure AD 租户的标识安全配置的自动评估）一致。 组织可以使用 Azure AD 门户中的 "标识" 安全分数页来找出当前安全配置的缺口，以确保它们遵循当前的 Microsoft[最佳安全方案](identity-management-best-practices.md)。 在 "安全分数" 页中实施每个建议将增加你的得分，并使你能够跟踪进度，并可帮助你将实现与其他类似规模的组织或行业进行比较。

![标识安全评分](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> 此处所述的许多功能都需要 Azure AD Premium 订阅，而有些则是免费的。 有关详细信息，请查看我们的[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)和[Azure AD 部署清单](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)。

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>开始之前：保护具有 MFA 的特权帐户

在您开始此核对清单之前，请确保阅读此清单时不会受到侵害。 首先需要保护特权帐户。

控制特权帐户的攻击者可能会造成巨大的损害，因此首先保护这些帐户至关重要。 使用[Azure AD 安全默认值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)或[条件性访问](../../active-directory/conditional-access/plan-conditional-access.md)，为组织中的所有管理员启用和要求[Azure 多重身份验证](../../active-directory/authentication/multi-factor-authentication.md)（MFA）。 如果尚未实现 MFA，请立即执行此操作！ 这一点很重要。

全部设置？ 让我们开始查看清单。

## <a name="step-1---strengthen-your-credentials"></a>步骤 1-增强凭据

大多数企业安全漏洞的来源是使用少量方法（例如密码喷涂、破坏性重播或网络钓鱼）之一泄露的帐户。 在此视频中了解有关这些攻击的详细信息（45分钟）：
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>确保你的组织使用强身份验证

考虑到密码被猜中、钓鱼、被恶意软件盗用或重复使用的频率，使用某种形式的强凭据备份密码非常重要-详细了解[Azure 多重身份验证](../../active-directory/authentication/multi-factor-authentication.md)。

若要轻松启用基本的标识安全级别，可以使用一键式启用和[Azure AD 安全默认设置](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。 默认情况下，安全默认为租户中的所有用户强制实施 Azure MFA，并阻止来自租户范围内的传统协议的登录。

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>开始禁止经常攻击的密码，并关闭传统的复杂性和过期规则。

许多组织都使用传统的复杂性（需要特殊的字符、数字、大写和小写）和密码过期规则。 [Microsoft 的研究](https://aka.ms/passwordguidance)表明，这些策略使用户能够选择更容易猜到的密码。

Azure AD 的[动态禁止密码](../../active-directory/authentication/concept-password-ban-bad.md)功能使用当前攻击者行为，以防止用户设置容易被猜到的密码。 当在云中创建用户时，此功能始终为 on，但现在也可用于混合组织（在部署[Azure AD 密码保护 Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)。 Azure AD 密码保护会阻止用户选择这些常见密码，并可以将其扩展到阻止包含指定自定义关键字的密码。 例如，可以防止用户选择包含公司产品名称或本地运动团队的密码。

Microsoft 建议采用以下基于[NIST 指南](https://pages.nist.gov/800-63-3/sp800-63b.html)的新式密码策略：

1. 要求密码至少包含8个字符。 不一定更好，因为它们会使用户选择可预测密码、在文件中保存密码或将密码记下来。
2. 禁用过期规则，使用户能够轻松猜出密码，如**Spring2019！**
3. 禁用字符组合要求，并防止用户选择经常攻击的密码，因为这会导致用户在密码中选择可预测的字符替换。

如果直接在 Azure AD 中创建标识，则可以使用[PowerShell 来阻止用户过期密码](../../active-directory/authentication/concept-sspr-policy.md)。 混合组织应使用[域组策略设置](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10))或[Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)来实施这些策略。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>防范泄露的凭据并为中断增加复原能力

如果你的组织将混合标识解决方案用于直通身份验证或联合身份验证，则应出于以下两个原因启用密码哈希同步：

* "Azure AD 管理" 中包含 "已[泄漏凭据](../../active-directory/reports-monitoring/concept-risk-events.md)" 报表的用户会警告你用户名和密码对，这些密码对已在 "暗 web" 公开。 令人难以置信的密码通过网络钓鱼、恶意软件和密码重用在以后被泄露的第三方站点上泄露。 Microsoft 会发现其中许多泄露的凭据，并在此报表中通知你，如果这些凭据与你组织中的凭据匹配-但仅当你[启用了密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)时才是如此！
* 发生本地中断（例如，在勒索软件攻击中）时，可以[使用密码哈希同步切换到使用云身份验证](choose-ad-authn.md)。利用此备份身份验证方法，你可以继续访问配置为 Azure Active Directory 身份验证的应用，包括 Office 365。 在这种情况下，IT 员工无需采取个人电子邮件帐户来共享数据，直到本地中断得到解决。

了解有关[密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)工作原理的详细信息。

> [!NOTE]
> 如果启用了密码哈希同步，并使用 Azure AD 域服务，则 Kerberos （AES 256）哈希和可选 NTLM （RC4，无 salt）哈希也将加密并同步到 Azure AD。

### <a name="implement-ad-fs-extranet-smart-lockout"></a>实现 AD FS extranet 智能锁定

组织，它将应用程序配置为直接进行身份验证，以便 Azure AD 受益于[Azure AD 智能锁定](../../active-directory/authentication/concept-sspr-howitworks.md)。 如果使用 Windows Server 2012R2 中的 AD FS，请实现 AD FS [extranet 锁定保护](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果在 Windows Server 2016 上使用 AD FS，则实现[extranet 智能锁定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 AD FS 智能 Extranet 锁定可防止强力攻击，这种攻击的目标 AD FS，同时阻止用户在 Active Directory 中被锁定。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>充分利用内部安全、更易于使用的凭据

使用[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)，你可以将密码替换为电脑和移动设备上的强双重身份验证。 此身份验证包含一种新类型的用户凭据，这些凭据将安全地绑定到设备并使用生物识别或 PIN。

## <a name="step-2---reduce-your-attack-surface"></a>步骤 2-减少攻击面

考虑到密码泄露的普适性相，最大程度地减少组织中的攻击面是至关重要的。 消除使用较旧的、不太安全的协议、限制访问入口点，并对资源的管理访问权限进行更重要的控制有助于减少攻击面。

### <a name="block-legacy-authentication"></a>阻止旧身份验证

使用其自己的旧式方法对 Azure AD 进行身份验证并访问公司数据的应用，为组织带来了另一种风险。 使用旧身份验证的应用示例为 POP3、IMAP4 或 SMTP 客户端。 旧身份验证应用代表用户进行身份验证，并阻止 Azure AD 进行高级安全评估。 替代的新式身份验证可降低安全风险，因为它支持多重身份验证和条件性访问。 建议执行以下三项操作：

1. [如果使用 AD FS，则阻止旧身份验证](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)。
2. 设置[SharePoint online 和 Exchange online 以使用新式身份验证](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)。
3. 如果有 Azure AD Premium，请使用[条件性访问策略](../../active-directory/conditional-access/conditions.md)来阻止旧身份验证，否则请使用[Azure AD 安全默认值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)。

### <a name="block-invalid-authentication-entry-points"></a>阻止无效的身份验证入口点

使用 "假定违反思维"，应在发生泄露的用户凭据时降低其影响。 对于环境中的每个应用，请考虑使用以下有效案例：哪些组、哪些网络、哪些设备和其他元素获得授权–然后阻止其余部分。 使用[Azure AD 条件性访问](../../active-directory/conditional-access/overview.md)，你可以根据你定义的特定条件控制授权用户访问其应用和资源的方式。

### <a name="restrict-user-consent-operations"></a>限制用户同意操作

务必了解各种[Azure AD 的应用程序许可体验](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)、[权限类型和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)，以及他们对组织安全状况的影响。 默认情况下，Azure AD 中的所有用户都可以授予利用 Microsoft 标识平台的应用程序访问你组织的数据。 尽管允许用户自行同意，但允许用户轻松获取与 Microsoft 365、Azure 和其他服务集成的有用应用程序，如果不小心使用和监视，则可能会造成风险。

Microsoft 建议[禁用未来的用户同意操作](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application)，以帮助减少您的 surface 区域并降低这种风险。 如果禁用了最终用户许可，则仍将接受以前的许可授权，但所有未来的同意操作都必须由管理员执行。 用户可以通过集成的[管理员同意请求工作流](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)或通过你自己的支持过程来请求管理员许可。 在禁用最终用户同意之前，请使用我们的[建议](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests)在组织中规划此更改。 对于你希望允许所有用户访问的应用程序，请考虑[代表所有用户授予同意](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)，以确保尚未单独同意的用户能够访问该应用程序。 如果你不希望所有方案中的所有用户都可以使用这些应用程序，请使用[应用程序分配](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)和[条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)限制用户对应用的访问权限。

请确保用户可以为新的应用程序请求管理员批准，以减少用户的不受限制，最小化支持量，并防止用户使用非 Azure AD 凭据注册应用程序。 在您对同意操作进行控制后，管理员应定期审核应用和许可权限。


### <a name="implement-azure-ad-privileged-identity-management"></a>实现 Azure AD Privileged Identity Management

"假定违反" 的另一个影响是，需要将受损帐户可以使用特权角色操作的可能性降至最低。

[Azure AD Privileged Identity Management （PIM）](../../active-directory/privileged-identity-management/pim-configure.md)可帮助你最大程度地减少帐户权限：

* 确定和管理分配到管理角色的用户。
* 了解你应删除的未使用或过量权限角色。
* 建立规则以确保特权角色受到多重身份验证的保护。
* 建立规则以确保授权的角色仅足够长的时间来完成特权任务。

启用 Azure AD PIM，然后查看分配有管理角色的用户，并在这些角色中删除不必要的帐户。 对于剩余的特权用户，将其从永久用户移到符合条件。 最后，建立适当的策略，以确保他们需要获得对这些特权角色的访问权限时，使用必要的更改控制可以安全地执行此操作。

在部署特权帐户过程中，请遵循[最佳做法至少创建两个紧急帐户](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)，以确保在锁定自己时仍有权访问 Azure AD。

## <a name="step-3---automate-threat-response"></a>步骤 3-自动执行威胁响应

Azure Active Directory 有许多自动拦截攻击的功能，消除了检测和响应之间的延迟。 减少犯罪分子在环境中嵌入的时间时，可降低成本和风险。 下面是你可以执行的具体步骤。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 实现用户风险安全策略

用户风险表示用户的标识已遭到破坏，并且是根据与用户标识关联的[用户风险检测](../../active-directory/identity-protection/overview.md)来计算的。 用户风险策略是一种条件性访问策略，可评估特定用户或组的风险级别。 根据低、中、高风险级别，可以将策略配置为阻止访问或要求使用多重身份验证进行安全的密码更改。 Microsoft 的建议是要求对高风险用户进行安全密码更改。

![标记为有风险的用户](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 实现登录风险策略

登录风险是指除帐户所有者尝试使用标识登录以外的其他人。 [登录风险策略](../../active-directory/identity-protection/overview.md)是一种条件性访问策略，可评估特定用户或组的风险级别。 根据风险级别（高/中/低），可以将策略配置为阻止访问或强制执行多重身份验证。 请确保在中等或更高的风险登录上强制执行多重身份验证。

![从匿名 Ip 登录](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>步骤 4-使用云智能

审核和记录与安全相关的事件和相关警报是高效保护策略的重要组成部分。 安全日志和报表为你提供可疑活动的电子记录，并帮助你检测可能指示网络的已尝试或成功外部渗透的模式以及内部攻击。 您可以使用审核来监视用户活动、记录法规遵从性、进行鉴证分析等。 警报提供安全事件的通知。

### <a name="monitor-azure-ad"></a>监视 Azure AD

Microsoft Azure 服务和功能可为你提供可配置的安全审核和日志记录选项，以帮助你识别安全策略和机制中的缺口，并解决这些缺口以帮助防止泄露。 可以使用[Azure 日志记录和审核](log-audit.md)，并[在 Azure Active Directory 门户中使用审核活动报告](../../active-directory/reports-monitoring/concept-audit-logs.md)。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>监视混合环境中的 Azure AD Connect Health

[通过监视 AD FS 与 Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) ，你可以更深入地了解对 AD FS 基础结构的攻击的潜在问题和可见性。 Azure AD Connect Health 通过详细信息、解决步骤和相关文档的链接提供警报;与身份验证通信相关的几个指标的使用情况分析;性能监视和报告。

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>监视 Azure AD Identity Protection 事件

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md)是一种通知、监视和报告工具，可以用来检测影响组织标识的潜在漏洞。 它检测到风险检测，如泄漏的凭据、无法进行的传播，以及来自受感染设备的登录、匿名 IP 地址、与可疑活动关联的 IP 地址以及未知位置。 启用通知警报，接收具有风险的用户电子邮件和/或每周摘要电子邮件。

Azure AD Identity Protection 提供了应每天监视的两个重要报表：
1. 有风险的登录报告会显示你应该调查的用户登录活动，合法所有者可能未执行登录。
2. 风险用户报表将显示可能已泄露的用户帐户，如检测到的凭据或从不同位置登录的用户导致不可能的旅行事件。

![标记为有风险的用户](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>审核应用和许可权限

用户可以被欺骗地定位到被入侵的网站或应用程序，这些应用程序将获得对其个人资料信息和用户数据（如电子邮件）的访问权限。 恶意执行组件可使用收到的许可权限加密其邮箱内容，并要求 ransom 重新获取邮箱数据。 [管理员应查看并审核](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)用户提供的权限，或者禁止用户在默认情况下授予许可。

除了审核用户提供的权限之外，还可以在高级环境中[找到有风险或不需要的 OAuth 应用程序](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth)。

## <a name="step-5---enable-end-user-self-service"></a>步骤 5-启用最终用户自助服务

尽可能多地平衡安全性与工作效率。 随着在长期运行时为安全性设置基础的思维方式，与你的旅程相同，你可以通过在保持警惕的同时为用户提供保护，消除组织中的摩擦。

### <a name="implement-self-service-password-reset"></a>实现自助密码重置

Azure AD 的[自助服务密码重置（SSPR）](../../active-directory/authentication/quickstart-sspr.md)为 IT 管理员提供了一种简单的方法，使用户能够在没有技术支持或管理员干预的情况下重置或解锁其密码或帐户。 系统包括详细的报告，用于跟踪用户重置其密码的时间，以及通知您误用或滥用的通知。

### <a name="implement-self-service-group-and-application-access"></a>实现自助服务组和应用程序访问

Azure AD 使非管理员能够使用安全组、Office 365 组、应用程序角色和访问包目录管理对资源的访问。  [自助服务组管理](../../active-directory/users-groups-roles/groups-self-service-management.md)使组所有者可以管理自己的组，而无需分配管理角色。 用户还可以创建和管理 Office 365 组，而无需依靠管理员来处理其请求，且未使用的组会自动过期。  [Azure AD 的权利管理](../../active-directory/governance/entitlement-management-overview.md)，通过全面的访问请求工作流和自动过期，进一步启用委托和可见性。  你可以委派给非管理员，以便能够为其拥有的组、团队、应用程序和 SharePoint Online 站点配置自己的访问包，并为需要审批访问权限的用户提供自定义策略，包括配置员工的作为审批者的经理和企业合作伙伴主办方。

### <a name="implement-azure-ad-access-reviews"></a>实现 Azure AD 访问评审

通过[Azure AD 访问评审](../../active-directory/governance/access-reviews-overview.md)，你可以管理访问包和组成员身份、访问企业应用程序和特权角色分配，以确保维护安全标准。  用户自身、资源所有者和其他审阅者进行定期监督，确保用户在不再需要访问权限时，不会长时间保留访问权限。

## <a name="summary"></a>요약

安全标识基础结构有很多方面，但这五个步骤的清单将帮助你快速完成更安全的身份基础结构：

* 增强凭据。
* 减小攻击面。
* 自动进行威胁响应。
* 利用云智能。
* 使用自助，实现更可预测且更全面的最终用户安全性。

我们非常感谢您对标识安全性的重视程度，希望此文档是您的组织更安全的方式的有用路线图。

## <a name="next-steps"></a>后续步骤

如果你需要帮助来规划和部署建议，请参阅[Azure AD 项目部署计划](https://aka.ms/deploymentplans)以获得帮助。

如果你完全相信这些步骤已完成，请使用 Microsoft 的[标识安全分数](../../active-directory/fundamentals/identity-secure-score.md)，这将使你保持最[新的最佳做法](identity-management-best-practices.md)和安全威胁。
