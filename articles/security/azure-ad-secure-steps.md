---
title: 保护 Azure Active Directory 中的标识基础结构的五个步骤
description: 本文档概述管理员为了使用 Azure AD 功能保护其组织而要执行的重要操作列表
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939762"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>保护标识基础结构的五个步骤

阅读本文档意味着你认识到了安全的重要性。 你可能已在履行保护组织的责任。 如果需要说服其他人注重安全性，请建议他们阅读最新的 [Microsoft 安全情报](https://www.microsoft.com/security/intelligence-report)。

本文档将会帮助你使用 Azure Active Directory 的功能来提高安全性，其中说明了如何使用包含五个步骤的查检表来防范组织受到网络攻击。

此查检表有助于快速部署关键的建议措施，立即为组织提供保护。具体措施：

* 增强凭据。
* 缩小受攻击面。
* 自动化威胁响应。
* 提高审核和监视的意识。
* 通过自助措施实现具有更高可预测性的全面最终用户安全性。

> [!NOTE]
> 本文档中的许多建议仅适用于配置为使用 Azure Active Directory 作为标识提供者的应用程序。 在应用中配置单一登录可确保将凭据策略、威胁检测、审核和日志记录的优势及其他功能添加到这些应用程序。 [通过 Azure Active Directory 实现的单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso)是基础 - 本文中的所有建议都立足于此。

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>开始之前：使用 MFA 保护特权帐户

在开始实施此查检表之前，请确保在阅读此查检表时不会受到攻击。 首先需要保护自己的特权帐户。

获得特权帐户控制权的攻击者可以执行巨大的破坏，因此先行保护这些帐户至关重要。 使用[基线保护](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection)启用 [Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA)，并要求组织中的所有管理员执行 MFA。 如果尚未实施 MFA，请立即实施！ 因为 MFA 非常重要。

准备好了吗？ 让我们开始阅读查检表。

## <a name="step-1---strengthen-your-credentials"></a>步骤 1 - 增强凭据 

大多数企业安全漏洞的起源在于某个帐户受到某种手段的攻击。这些手段多种多样，例如密码喷洒 (password spray)、破解重放或网络钓鱼。 请观看以下视频来详细了解这些攻击：
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

如果标识系统中的用户使用弱密码，而未使用多重身份验证来增强密码，则这些用户面临的问题不是是否或者何时会受到攻击，而是受到攻击的“频率”如何。

### <a name="make-sure-your-organization-use-strong-authentication"></a>确保组织使用强身份验证

根据密码被猜出、钓鱼、被恶意软件盗窃或重复使用的频率，必须使用某种形式的强凭据来保护密码 – 详细了解 [Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)。

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>摒弃传统的复杂性规则和过期规则，开始禁止使用经常受到攻击的密码

许多组织使用传统的复杂性规则（例如特殊字符）和密码过期规则。 Microsoft 的研究表明，这些策略非常不利，会导致用户选择更容易猜出的密码。

根据 [NIST 指南](https://pages.nist.gov/800-63-3/sp800-63b.html)，Microsoft 建议实施以下三种规则：

1. 要求密码至少包含 8 个字符。 密码并非越长越好，因为这会导致用户选择容易预测的密码、将密码保存在文件中，或者写下密码。
2. 禁用过期规则，因为这种规则会促使用户选择容易猜出的密码，例如 **Summer2018!**。
3. 禁用字符组合要求，并防止用户选择经常受到攻击的密码，因为这些规则会导致用户在密码中选择容易预测的替代字符。

如果直接在 Azure AD 中创建标识，可以[使用 PowerShell 防止用户的密码过期](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)。 结合 Azure AD Connect 使用本地 AD 将标识同步到 Azure AD（也称为混合部署）的组织，应该使用[域组策略设置](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10))或 [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy) 实施本地[智能密码策略](https://aka.ms/passwordguidance)。

Azure Active Directory 的[动态禁止密码](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)功能使用当前的攻击者行为来防止用户设置容易猜出的密码。 此功能始终处于启用状态。使用混合部署的组织在启用[密码写回](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback)后可以受益于此功能，或者，可以部署[适用于 Windows Server Active Directory 的 Azure AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)。 Azure AD 密码保护会阻止用户选择一般性的常用密码，或者可以配置的自定义密码。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>防止凭据泄漏，并针对服务中断添加复原功能

如果组织使用混合标识解决方案，应该启用密码哈希同步，原因包括以下两点：

* Azure AD 管理系统中的[凭据已泄漏的用户](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)报告会警告用户名和密码对已在“黑暗网络”中透露。 日后遭到攻击的第三方站点上出现的网络钓鱼、恶意软件和密码重用，导致大量的密码泄漏。 Microsoft 会检测其中的许多泄漏凭据，如果它们与你组织中的凭据匹配，则会告诉你 – 但前提是已[启用密码哈希同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)！
* 如果发生本地服务中断（例如，在勒索软件攻击中），可以[使用密码哈希同步切换到云身份验证](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。 使用此备用身份验证方法，可以继续访问配置为使用 Azure Active Directory 进行身份验证的应用，包括 Office 365。

详细了解[密码哈希同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)的工作原理。

### <a name="implement-ad-fs-extranet-lockout"></a>实施 AD FS Extranet 锁定

将应用程序配置为直接向 Azure AD 进行身份验证的组织可以受益于 [Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)。 如果使用 AD FS，请实施 AD FS [Extranet 锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)。 Extranet 锁定可以防范针对 AD FS 的暴力攻击，同时可以防止用户在 Active Directory 中遭到锁定。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>利用本质安全的、易于使用的凭据

使用 [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 可在电脑和移动设备上将密码取代为强式双重身份验证。 这种身份验证包括一种与设备绑定的新型用户凭据，并使用生物识别技术或 PIN。

## <a name="step-2---reduce-your-attack-surface"></a>步骤 2 - 缩小受攻击面

密码攻击无处不在，尽量缩小组织中的受攻击面至关重要。 弃用不够安全的旧式协议、限制访问入口点、针对资源的管理访问实行更严格的控制有助于缩小受攻击面。

### <a name="block-legacy-authentication"></a>阻止传统身份验证

使用自身的传统方法在 Azure AD 中进行身份验证和访问公司数据的应用给组织带来了另一种风险。 使用传统身份验证的应用示例包括 POP3、IMAP4 或 SMTP 客户端。 传统身份验证应用会代表用户执行身份验证，并阻止 Azure AD 执行高级安全评估。 替代的新式身份验证可降低安全风险，因为它支持多重身份验证和条件访问。 我们建议采取以下三项措施：

1. [如果使用 AD FS，则阻止传统身份验证](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)。
2. [将 SharePoint Online 和 Exchange Online 设置为使用新式身份验证](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
3. [使用条件访问策略来阻止传统身份验证](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)。

### <a name="block-invalid-authentication-entry-points"></a>阻止无效的身份验证入口点

使用假设违规思路会减少用户凭据泄露造成的影响。 对于环境中的每个应用，请考虑有效的方案：要为哪些组、哪些网络、哪些设备和其他元素授权 – 然后阻止余下的部分。 谨慎限制[高特权帐户或服务帐户](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)的使用。 使用 [Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)，可以控制已获授权的用户根据定义的特定条件访问其应用和资源的方式。

请特别注意服务帐户（用于自动执行任务的帐户）。 使用条件访问可以确保只在适当的时间，通过适当的 IP 针对适当的服务运行此类帐户。

### <a name="implement-azure-ad-privileged-identity-management"></a>实施 Azure AD Privileged Identity Management

“假设违规”的另一个影响是，需要尽量减少受到攻击的帐户以特权角色运行的可能性。 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 有助于尽量减少帐户特权，因为它可以帮助：

* 识别和管理已分配到管理角色的用户。
* 了解应删除的未使用或过多的特权角色。
* 建立规则来确保特权角色受到多重身份验证的保护。
* 建立规则来确保授予特权角色的时间长短刚好足够用于完成特权任务。

启用 Azure AD PIM，然后查看分配有管理角色的用户，并删除这些角色中不必要的帐户。 对于剩余的特权用户，请将其角色分配从“永久”更改为“符合条件”。 最后，建立适当的策略，确保当这些用户需要访问这些特权角色时能够安全地访问。

在部署特权帐户的过程中，请遵循[至少创建两个紧急帐户的最佳做法](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)，确保自己被锁定时能够访问 Azure AD。

## <a name="step-3---automate-threat-response"></a>步骤 3 - 自动化威胁响应

Azure Active Directory 中的许多功能可以自动截获攻击，以消除检测与响应之间的延迟。 如果能够减少罪犯渗入你的环境所能利用的时间，则就可以降低成本和风险。 下面是可以采取的具体措施。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 实施用户风险安全策略

用户风险指示用户身份透露的可能性，它是根据与用户身份关联的[用户风险事件](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)计算的。 用户风险策略是一种条件访问策略，评估特定用户或组的风险级别。 根据“低”、“中”、“高”风险级别，可以配置一个策略来阻止访问，或者要求使用多重身份验证进行安全密码更改。 Microsoft 建议要求高风险用户进行安全密码更改。

![已标记为存在风险的用户](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>使用 Azure AD Identity Protection 实施登录风险策略

登录风险是指除帐户所有者以外的其他某人尝试使用标识登录的可能性。 [登录风险策略](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)是一种条件访问策略，评估特定用户或组的风险级别。 根据风险级别（高/中/低），可以配置一个策略来阻止访问，或强制实施多重身份验证。 请确保针对“中”或更高风险的登录强制实施多重身份验证。

![从匿名 IP 登录](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>步骤 4 - 提高意识

审核和记录与安全相关的事件以及相关警报是有效保护策略的至关重要组成部分。 安全日志和报告提供可疑活动的电子记录，并帮助检测可能指示试图或已成功的外部网络渗透以及内部攻击的模式。 可以使用审核来监控用户活动、记录法规遵从性以及执行取证分析等。 警报提供安全事件的通知。

### <a name="monitor-azure-ad"></a>监视 Azure AD

Microsoft Azure 服务和功能提供可配置的安全审核和日志记录选项，帮助识别安全策略和机制的缺口，并解决这些缺口，以防违规。 可以使用 [Azure 日志记录和审核](https://docs.microsoft.com/azure/security/azure-log-audit)，以及 [Azure Active Directory 门户中的审核活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>监视混合环境中的 Azure AD Connect Health

[使用 Azure AD Connect Health 监视 ADFS](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) 能够更好地洞察潜在问题以及 ADFS 基础结构受到的攻击。 Azure AD Connect Health 提供附带详细信息的警报、解决方法步骤、相关文档的链接、与身份验证流量相关的多个指标的使用情况分析、性能监视和报告。

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>监视 Azure AD Identity Protection 事件

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 是一个通知、监视和报告工具，可用于检测影响组织标识的潜在漏洞。 它可以检测风险事件，例如，泄漏的凭据、不可能的轨迹、来自受感染设备的登录、匿名的 IP 地址、与可疑活动关联的 IP 地址，以及未知的位置。 启用通知警报可以接收有风险用户的电子邮件，和/或每周摘要电子邮件。

![已标记为存在风险的用户](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>步骤 5 - 启用最终用户自助

我们希望在安全性与工作效率之间尽量实现平衡。 在达成目标的同时兼顾长期安全基准的制定，可以消除组织中存在的冲突，让用户获得所需的能力，并让他们保持警惕。 

### <a name="implement-self-service-password-reset"></a>实施自助密码重置

IT 管理员可以通过 Azure 的[自助密码重置 (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)，让用户方便重置或解锁其密码或帐户，而无需管理员的干预。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。 

### <a name="implement-self-service-group-management"></a>实施自助服务组管理

Azure AD 提供使用安全组和 Office 365 组管理资源访问权限的功能。 这些组可由组所有者管理，而无需 IT 管理员进行管理。 此功能称作[自助服务组管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，没有管理角色的组所有者可以使用它来创建和管理组，而无需依赖于管理员处理他们的请求。

### <a name="implement-azure-ad-access-reviews"></a>实施 Azure AD 访问评审

使用 [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)可以管理组成员身份、企业应用程序的访问权限和特权角色分配，以确保维持以下安全标准：不向用户授予超出所需时间范围的访问权限。

## <a name="summary"></a>摘要

保护安全标识基础结构涉及到很多方面，但此五步骤查检表可帮助你快速实现一个更可靠的安全标识基础结构：

* 增强凭据。
* 缩小受攻击面。
* 自动化威胁响应。
* 提高审核和监视的意识。
* 通过自助措施实现具有更高可预测性的全面最终用户安全性。

非常感谢你严肃看待标识安全性，希望本文档在帮助你提高组织的安全性上提供了有用的思路。

## <a name="next-steps"></a>后续步骤
如果在规划和部署这些建议方面需要帮助，请参阅 [Azure AD 项目部署计划](http://aka.ms/deploymentplans)。
