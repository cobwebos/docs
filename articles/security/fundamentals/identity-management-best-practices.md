---
title: Azure 标识和访问安全最佳实践 | Microsoft 文档
description: 本文提供一系列有关使用内置 Azure 功能进行标识管理和访问控制的最佳实践。
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053343"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure 标识管理和访问控制安全最佳实践

本文介绍一系列 Azure 标识管理和访问控制安全最佳实践。 这些最佳做法衍生自我们的 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 经验和客户经验。

对于每项最佳做法，本文将说明：

* 最佳实践是什么
* 为何要启用该最佳实践
* 如果无法启用该最佳实践，可能的结果是什么
* 最佳实践的可能替代方案
* 如何学习启用最佳实践

这篇 Azure 标识管理和访问控制安全最佳实践以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。

本文撰写本文的目的是在部署后，通过 "[保护标识基础结构的5个步骤](steps-secure-identity.md)"，为你提供一种更可靠的安全状况，指导你完成一些核心功能，并服务器.

看法和技术将随着时间改变，本文会定期更新以反映这些更改。

本文中介绍的 Azure 标识管理和访问控制安全最佳实践包括：

* 将标识视为主要安全边界
* 集中化标识管理
* 管理连接的租户
* 启用单一登录
* 启用条件性访问
* 规划日常安全改进
* 启用密码管理
* 对用户强制执行多重身份验证
* 使用基于角色的访问控制
* 降低特权帐户的泄露风险
* 控制资源所在的位置
* 使用 Azure AD 进行存储身份验证

## <a name="treat-identity-as-the-primary-security-perimeter"></a>将标识视为主要安全边界

许多人认为标识是主要安全边界。 这与以网络安全为重点的传统做法不同。 网络边界出现越来越多的漏洞，在 [BYOD](https://aka.ms/byodcg) 设备和云应用程序激增之前，该边界防御不似之前那样有效。

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 Azure AD 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 它将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。

以下部分列出了使用 Azure AD 实现标识和访问安全性的最佳做法。

**最佳做法**：围绕用户和服务标识中心安全控制和检测。
**详细信息**：使用 Azure AD 归置控件和标识。

## <a name="centralize-identity-management"></a>集中化标识管理

在[混合标识](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)方案中，我们建议集成本地目录和云目录。 集成使你的 IT 团队可以从一个位置管理帐户，而不考虑帐户的创建位置。 通过提供公共标识来访问云和本地资源，集成还有助于提高用户的工作效率。

**最佳做法**：建立单个 Azure AD 实例。 一致性和单个权威源将提高清晰度并降低人为错误和配置复杂性带来的安全风险。
**详细信息**：指定单个 Azure AD 目录作为公司和组织帐户的权威来源。

**最佳做法**：将本地目录与 Azure AD 进行集成。  
**详细信息**：使用 [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) 将本地目录与云目录同步。

> [!Note]
> 存在[影响 Azure AD Connect 性能的因素](../../active-directory/hybrid/plan-connect-performance-factors.md)。 确保 Azure AD Connect 有足够的容量来防止绩效不佳系统降低安全和工作效率。 大型或复杂组织（组织预配超过100000个对象）应遵循[建议](../../active-directory/hybrid/whatis-hybrid-identity.md)来优化其 Azure AD Connect 实现。

**最佳做法**：不要将帐户同步到在现有 Active Directory 实例中具有高权限的 Azure AD。
**详细信息**：请勿更改用于筛选这些帐户的默认[Azure AD Connect 配置](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)。 此配置可减轻攻击者从云到本地资产（可能产生重大事件）的风险。

**最佳做法**：启用密码哈希同步。  
**详细信息**：密码哈希同步是一项功能，用于将用户密码哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例。 此同步可帮助防止泄漏的凭据从以前的攻击中重播。

即使决定使用 Active Directory 联合身份验证服务 (AD FS) 或其他标识提供者进行联合身份验证，也可以选择性地设置密码哈希同步作为备用机制，以应对本地服务器发生故障或临时不可用的情况。 此同步使用户能够使用登录到本地 Active Directory 实例时所用的相同密码登录到服务。 如果用户在未连接到 Azure AD 的其他服务上使用了相同的电子邮件地址和密码，则它还允许标识保护通过将已同步密码哈希与已知的密码进行比较来检测受损的凭据。

有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)。

**最佳做法**：对于新应用程序开发，请使用 Azure AD 进行身份验证。
**详细信息**：使用正确的功能支持身份验证：

  - 员工 Azure AD
  - 为来宾用户和外部合作伙伴[AZURE AD B2B](../../active-directory/b2b/index.yml)
  - [Azure AD B2C](../../active-directory-b2c/index.yml)控制客户在使用应用程序时如何注册、登录和管理其配置文件

未将其本地标识与云标识集成的组织在管理帐户方面可能开销更大。 这种开销增加了出错和安全漏洞的可能性。

> [!Note]
> 你需要选择关键帐户将驻留的目录，以及使用的管理工作站是否由新的云服务或现有的进程管理。 使用现有的管理和标识预配过程可能会降低某些风险，但也可能造成攻击者损害本地帐户并切换到云的风险。 你可能想要为不同的角色（例如，IT 管理员和业务单元管理员）使用不同的策略。 可以使用两个选项。 第一种方法是创建不与本地 Active Directory 实例同步的 Azure AD 帐户。 将你的管理员工作站加入到 Azure AD，你可以使用 Microsoft Intune 管理和修补。 第二种方法是通过同步到本地 Active Directory 实例，使用现有的管理员帐户。 使用 Active Directory 域中的现有工作站进行管理和安全。

## <a name="manage-connected-tenants"></a>管理连接的租户
你的安全组织需要了解来评估风险，并确定是否遵循组织的策略和任何法规要求。 你应确保安全组织能够查看与你的生产环境和网络（通过[Azure ExpressRoute](../../expressroute/expressroute-introduction.md)或[站点到站点 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)）连接的所有订阅。 Azure AD 中的[全局管理员/公司管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)可以将其访问权限提升到 "[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)" 角色，并查看连接到你的环境的所有订阅和托管组。

请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)，以确保你和你的安全组可以查看连接到你的环境的所有订阅或管理组。 评估风险后，应删除此提升的访问权限。

## <a name="enable-single-sign-on"></a>启用单一登录

在移动优先、云优先的世界中，你希望能够从任意位置实现对设备、应用和服务的单一登录 (SSO)，以便你的用户随时随地都能高效工作。 如果要管理多个标识解决方案，则不仅会给 IT 人员造成管理问题，而且用户还必须记住多个密码。

通过对所有应用和资源使用相同的标识解决方案，可以实现 SSO。 并且不论资源是位于本地还是云中，用户均可以使用相同凭据集登录和访问所需资源。

**最佳做法**：启用 SSO。  
**详细信息**：Azure AD [将本地 Active Directory](/azure/active-directory/connect/active-directory-aadconnect) 扩展到云。 用户可以将他们的主要工作或学校帐户用于他们加入域的设备、公司资源以及完成工作所需的所有 Web 和 SaaS 应用程序。 用户无需记住多组用户名和密码，系统会根据组织的组成员身份和员工身份的状态，自动预配（或取消设置）应用程序访问权限。 可以针对库应用或者通过 [Azure AD 应用程序代理](/azure/active-directory/active-directory-application-proxy-get-started)自行开发和发布的本地应用控制访问权限。

用户可使用 SSO 基于 Azure AD 中的工作或学校帐户访问 [SaaS 应用程序](/azure/active-directory/active-directory-appssoaccess-whatis)。 这不仅适用于 Microsoft SaaS 应用，还适用于其他应用，例如 [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) 和 [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial)。 应用程序可配置为使用 Azure AD 作为[基于 SAML 的标识](/azure/active-directory/fundamentals-identity)提供者。 作为安全控制机制，Azure AD 不会发出允许用户登录应用程序的令牌，除非用户已通过 Azure AD 获取了访问权限。 可以直接或者通过用户所属的组授予访问权限。

如果组织没有通过创建通用标识来为用户和应用程序实现 SSO，那么用户拥有多个密码的情况就更容易出现。 这种情况增加了用户重复使用同一密码或使用弱密码的可能性。

## <a name="turn-on-conditional-access"></a>启用条件性访问

用户可能会从任意位置使用各种设备和应用访问组织的资源。 作为 IT 管理员，你需要确保这些设备符合安全和合规性标准。 仅关注谁可以访问资源不再能满足需求。

若要平衡安全和工作效率，你需要考虑如何访问资源，然后才能做出有关访问控制的决策。 使用 Azure AD 条件性访问，可以满足此要求。 使用条件性访问，你可以根据访问云应用的条件进行自动访问控制决策。

**最佳做法**：管理和控制对公司资源的访问。  
**详细信息**：根据组、位置和应用程序敏感性为 SaaS 应用和 Azure AD 连接的应用配置 Azure AD[条件性访问](/azure/active-directory/active-directory-conditional-access-azure-portal)。

**最佳做法**：阻止旧的身份验证协议。
**详细信息**：攻击者每天利用旧版协议中的漏洞，尤其是对密码喷涂攻击。 配置条件访问以阻止旧协议。 有关详细信息，请参阅视频[Azure AD： Do 和不是](https://www.youtube.com/watch?v=wGk0J4z90GI)。

## <a name="plan-for-routine-security-improvements"></a>规划日常安全改进

安全始终在不断发展，因此必须将其内置于云和标识管理框架中，以便定期显示增长并发现用于保护环境的新方法。

"标识安全分数" 是一组建议的安全控制，Microsoft 发布这些控制可为您提供一个数字评分，用于客观地度量您的安全状况，并帮助规划将来的安全改进。 您还可以查看与其他行业中的分数比较的分数，以及随时间推移而变化的分数。

**最佳做法**：根据行业中的最佳实践规划日常安全检查和改进。
**详细信息**：使用 "标识" 安全分数功能，在一段时间内对改进进行排名。

## <a name="enable-password-management"></a>启用密码管理

如果有多个租户或者你想要允许用户[重置自己的密码](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)，则必须使用适当的安全策略来防止滥用。

**最佳做法**：为用户设置自助服务密码重置 (SSPR)。  
**详细信息**：使用 Azure AD [自助服务密码重置](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)功能。

**最佳做法**：监视 SSPR 的使用情况或者是否真的在使用 SSPR。  
**详细信息**：通过使用 Azure AD [密码重置注册活动报表](/azure/active-directory/active-directory-passwords-get-insights)监视正在注册的用户。 Azure AD 提供的报表功能可帮助使用预生成的报表来回答问题。 如果有相应的授权，还可以创建自定义查询。

**最佳做法**：将基于云的密码策略扩展到本地基础结构。
**详细信息**：通过对本地密码更改执行相同检查来增强你的组织中的密码策略，就像对基于云的密码更改执行相同的检查。 为本地 Windows Server Active Directory 代理安装[Azure AD 密码保护](/azure/active-directory/authentication/concept-password-ban-bad)，以将禁止的密码列表扩展到现有基础结构。 需要在本地更改、设置或重置密码的用户和管理员才能遵守与仅限云的用户相同的密码策略。

## <a name="enforce-multi-factor-verification-for-users"></a>对用户强制执行多重身份验证

建议对所有用户要求进行双重验证。 这包括组织中的管理员和其他人员，如果他们的帐户泄露，可能会产生重大影响（例如，财务官员）。

要求双重验证有多种选项。 最佳选项取决于你的目标、正在运行的 Azure AD 版本以及许可计划。 请参阅[如何要求对用户进行双重验证](/azure/active-directory/authentication/howto-mfa-userstates)了解最佳选项。 有关许可和定价的详细信息，请参阅 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure 多重身份验证](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)定价页。

以下是启用双重验证的选项和优势：

**选项 1**：使用 Azure AD 安全默认**权益**，为所有用户和登录方法启用 mfa：通过此选项，你可以使用严格的策略为环境中的所有用户轻松快速地强制执行 mfa：

* 质询管理帐户和管理登录机制
* 需要为所有用户 Microsoft Authenticator MFA 质询
* 限制旧的身份验证协议。

此方法可用于所有授权层，但不能与现有的条件性访问策略混合使用。 你可以在 Azure AD 安全默认值中查找详细信息

**选项 2**：[通过更改用户状态启用多重身份验证](../../active-directory/authentication/howto-mfa-userstates.md)。   
**优势**：这是要求进行双重验证的传统方法。 它适用于[云中的 Azure 多重身份验证和 Azure 多重身份验证服务器](/azure/active-directory/authentication/concept-mfa-whichversion)。 使用此方法要求用户在每次登录时执行双重验证，并覆盖条件访问策略。

若要确定需要启用多因素身份验证的位置，请参阅[哪个版本的 AZURE MFA 适合我的组织？](/azure/active-directory/authentication/concept-mfa-whichversion)。

**选项 3**：[使用条件访问策略启用多重身份验证](/azure/active-directory/authentication/howto-mfa-getstarted)。
**权益**：通过此选项，你可以通过使用[条件访问](/azure/active-directory/active-directory-conditional-access-azure-portal)在特定情况下提示进行双重验证。 特定条件可以是用户从不同位置、不受信任的设备或你认为存在风险的应用程序登录。 定义要求双重验证的特定条件可以避免不断提示用户这种令人不快的用户体验。

这是为用户启用双重验证最灵活的方式。 启用条件性访问策略仅适用于云中的 Azure 多重身份验证，是 Azure AD 的一项高级功能。 有关此方法的详细信息，请参阅[部署基于云的 Azure 多重身份验证](/azure/active-directory/authentication/howto-mfa-getstarted)。

**选项 4**：通过评估[Azure AD Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)的用户和登录风险，启用条件性访问策略的多重身份验证。   
**优势**：此选项使你能够：

* 检测影响组织标识的潜在漏洞。
* 配置自动响应与组织标识相关的可疑操作。
* 调查可疑事件，并采取适当的措施进行解决。

此方法使用“Azure AD 标识保护”风险评估来确定是否需要基于所有云应用程序的用户和登录风险进行双重验证。 此方法需要 Azure Active Directory P2 授权。 有关此方法的详细信息，请参阅 [Azure Active Directory 标识保护](/azure/active-directory/identity-protection/overview)。

> [!Note]
> 选项1：通过更改用户状态启用多重身份验证会替代条件访问策略。 由于选项2和3使用条件性访问策略，因此不能将选项1与它们一起使用。

未添加额外标识保护层（如双重验证）的组织将更容易受到凭据窃取攻击。 凭据窃取攻击可能导致数据泄漏。

## <a name="use-role-based-access-control"></a>使用基于角色的访问控制

对于使用云的任何组织而言，云资源的访问管理非常重要。 [基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview)可帮助你管理哪些用户有权访问 Azure 资源、他们可对这些资源执行哪些操作以及他们有权访问哪些区域。

在 Azure 中指定负责特定功能的组或单独角色可帮助避免发生导致产生安全风险的人工和自动化错误的混乱。 对于想要实施数据访问安全策略的组织而言，必须根据[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全策略限制访问权限。

安全团队需要了解你的 Azure 资源，以便评估和修正风险。 如果安全团队有运营责任，他们需要额外的权限来完成工作。

可以使用[RBAC](/azure/role-based-access-control/overview)向特定范围内的用户、组和应用程序分配权限。 角色分配的范围可以是订阅、资源组或单个资源。

**最佳做法**：在团队内隔离职责并仅向用户授予执行作业所需的访问权限。 只允许特定范围内的特定操作，而不是向每个人提供 Azure 订阅或资源的无限制权限。
**详细信息**：使用 Azure 中[内置的 RBAC 角色](/azure/role-based-access-control/built-in-roles)向用户分配权限。

> [!Note]
> 特定权限会造成不必要的复杂性和混乱，并将其累积到 "旧" 的配置中，这种情况很难解决，而不必担心中断。 避免特定于资源的权限。 相反，请使用管理组来实现企业范围的权限和资源组，以获取订阅中的权限。 避免用户特定的权限。 请改为将访问权限分配到 Azure AD 中的组。

**最佳做法**：向安全团队授予 azure 职责访问权限以查看 azure 资源，以便他们能够评估和修正风险。
**详细信息**：向安全团队授予 RBAC[安全读者](/azure/role-based-access-control/built-in-roles#security-reader)角色。 您可以使用根管理组或分段管理组，具体取决于职责范围：

* 负责所有企业资源的团队的**根管理组**
* 范围有限的团队的**段管理组**（通常是由于法规或其他组织边界）

**最佳做法**：向具有直接运营责任的安全团队授予适当的权限。
**详细信息**：查看用于适当角色分配的 RBAC 内置角色。 如果内置角色不满足组织的特定需求，则可以[为 Azure 资源创建自定义角色](/azure/role-based-access-control/custom-roles)。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和服务主体。

**最佳做法**：向 Azure 安全中心授予对需要该安全角色的访问权限。 安全中心允许安全团队快速识别和修正风险。
**详细信息**：将这些安全团队添加到 RBAC[安全管理员](/azure/role-based-access-control/built-in-roles#security-admin)角色，以便他们可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议，以及消除警报和建议。 您可以使用根管理组或分段管理组来执行此操作，具体取决于职责范围。

不使用 RBAC 等功能实施数据访问控制的组织可能会向其用户提供比所需权限更多的特权。 这可能会导致数据泄露，使用户能够访问他们不应拥有的数据类型（例如，对业务的影响很大）。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特权帐户的泄露风险

保护特权访问是保护业务资产的首要步骤。 减少拥有访问权限的人员以保护信息或资源安全，这样可以减小恶意用户获得访问权限，或者已授权用户无意中影响敏感资源的可能性。

特权帐户是指掌控和管理 IT 系统的帐户。 网络攻击者会攻击这些帐户来获取组织数据和系统的访问权限。 为了保护特权访问，应隔离此类帐户和系统，使其免受恶意用户的威胁。

建议制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 有关创建详细路线图以保护在 Azure AD、Microsoft Azure、Office 365 和其他云服务中托管或报告的标识和访问的信息，请查看[确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)。

以下内容总结了[确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)中介绍的最佳做法：

**最佳做法**：管理、控制和监视对特权帐户的访问权限。   
**详细信息**：启用 [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)。 启用 Privileged Identity Management 以后，会收到有关特权访问角色更改的通知电子邮件。 向目录中的高特权角色添加更多用户时，这些通知相当于早期警告。

**最佳做法**：确保所有关键管理员帐户都 Azure AD 帐户进行管理。
**详细信息**：从关键管理员角色（例如，Microsoft 帐户，如 hotmail.com、live.com 和 outlook.com）中删除任何使用者帐户。

**最佳做法**：确保所有关键管理员角色都有单独的管理任务帐户，以避免网络钓鱼和其他攻击破坏管理权限。
**详细信息**：创建一个单独的管理员帐户，为其分配执行管理任务所需的权限。 阻止使用这些管理帐户进行每日生产力工具，如 Microsoft Office 365 电子邮件或任意 web 浏览。

**最佳做法**：对角色为高特权角色的帐户进行标识和分类。   
**详细信息**：启用 Azure AD Privileged Identity Management 后，请查看角色为全局管理员、特权角色管理员和其他高特权角色的用户。 请删除在这些角色中不再需要的任何帐户，并对剩余的分配给管理员角色的帐户分类：

* 单独分配给管理用户，可用于非管理性目的（例如，个人电子邮件）
* 单独分配给管理用户，按规定只能用于管理目的
* 跨多个用户共享
* 适用于紧急访问情况
* 适用于自动化脚本
* 适用于外部用户

**最佳做法**：实行“实时”(JIT) 访问可进一步降低特权的曝光时间，并提高对特权帐户使用情况的可见性。   
**详细信息**：利用 Azure AD Privileged Identity Management，可以：

* 限制用户只接受他们的权限 JIT。
* 分配时限更短的角色，确信权限会自动撤消。

**最佳做法**：定义至少两个紧急访问帐户。   
**详细信息**：可以使用紧急访问帐户来帮助组织限制现有 Azure Active Directory 环境中的特权访问。 这些帐户拥有极高的特权，不要将其分配给特定的个人。 紧急访问帐户只能用于不能使用正常管理帐户的情况。 组织必须将紧急账户的使用限制在必要时间范围内。

评估已经获得或有资格获得全局管理员角色的帐户。 如果使用 `*.onmicrosoft.com` 域（用于紧急访问）看不到任何仅限云的帐户，请创建此类帐户。 有关详细信息，请参阅[在 Azure AD 中管理紧急访问管理帐户](/azure/active-directory/users-groups-roles/directory-emergency-access)。

**最佳做法**：在发生紧急情况时有一个 "休息杯" 进程。
**详细信息**：按照[Azure AD 中的保护混合和云部署的特权访问](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)中的步骤操作。

**最佳做法**：要求所有关键管理员帐户的密码不变（首选）或需要多重身份验证。
**详细信息**：使用[Microsoft Authenticator 应用](/azure/active-directory/authentication/howto-authentication-phone-sign-in)登录到任何 Azure AD 帐户，而无需使用密码。 与[Windows Hello 企业版](/windows/security/identity-protection/hello-for-business/hello-identity-verification)一样，Microsoft Authenticator 使用基于密钥的身份验证来启用与设备关联的用户凭据，并使用生物识别身份验证或 PIN。

对于永久分配给一个或多个 Azure AD 管理角色的单个用户，需要在登录时进行 Azure 多重身份验证：全局管理员、特权角色管理员、Exchange Online 管理员和 SharePoint Online管理. [为管理员帐户启用多重身份验证](/azure/active-directory/authentication/howto-mfa-userstates)，并确保管理员帐户用户已注册。

**最佳做法**：对于关键管理员帐户，请提供不允许生产任务的管理工作站（例如，浏览和发送电子邮件）。 这将保护你的管理员帐户免受使用浏览和电子邮件攻击的威胁，并大大降低重大事件的风险。
**详细信息**：使用管理工作站。 选择工作站安全级别：

- 高度安全的工作效率设备为浏览和其他工作效率任务提供高级安全性。
- [特权访问工作站（paw）](/windows-server/identity/securing-privileged-access/privileged-access-workstations)提供了一个专用的操作系统，该操作系统受 internet 攻击和敏感任务的威胁向量保护。

**最佳做法**：在员工离开组织时取消设置管理员帐户。
**详细信息**：有一个就地过程，该过程将在员工离开组织时禁用或删除管理帐户。

**最佳做法**：使用当前攻击方法定期测试管理员帐户。
**详细信息**：使用 Office 365 攻击模拟器或第三方产品/服务在你的组织中运行现实的攻击方案。 这可以帮助你在真正攻击发生之前查找有漏洞的用户。

**最佳做法**：采取措施来缓解最常用的攻击技术的冲击。  
**详细信息**：[确定管理角色中需要切换到工作或学校帐户的 Microsoft 帐户](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[对于全局管理员帐户，请确保使用单独的用户帐户和邮件转发功能](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[确保最近更改过管理帐户的密码](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[启用密码哈希同步](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[要求对所有特权角色用户和公开的用户进行多重身份验证](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[获取 Office 365 安全功能分数（如果使用 Office 365）](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[查看 Office 365 安全性和符合性指南（如果使用 Office 365）](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[配置 Office 365 活动监视（如果使用 Office 365）](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[确定事件/紧急情况响应计划所有者](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[保护本地特权管理帐户](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

如果不保护特权访问，你可能会拥有过多高特权角色用户，并且更易受到攻击。 恶意操作者（包括网络攻击者）通常会以管理员帐户和特权访问的其他元素为目标，通过凭据窃取获得敏感数据和系统的访问权限。

## <a name="control-locations-where-resources-are-created"></a>控制创建资源的位置

非常重要的一点是，既要允许云操作员执行任务，同时又要防止他们违反管理组织资源所需的惯例。 想要控制创建资源的位置的组织应该对这些位置进行硬编码。

可以使用 [Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)创建安全策略，其中的定义描述了会明确遭到拒绝的操作或资源。 可以在所需范围（例如订阅、资源组或是单个资源）分配这些策略定义。

> [!NOTE]
> 安全策略与 RBAC 不同。 它们实际上使用 RBAC 授权用户来创建这些资源。
>
>

无法控制资源创建方式的组织更容易因用户创建的资源超过所需数目，而产生滥用服务的情况。 强化资源创建过程是保护多租户方案的重要步骤。

## <a name="actively-monitor-for-suspicious-activities"></a>主动监视可疑活动

主动身份监视系统可以快速检测可疑行为并触发警报以进行进一步调查。 下表列出了两个可帮助组织监视其标识的 Azure AD 功能：

**最佳做法**：有一种方法可确定：

- [未受跟踪](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)的登录尝试。
- 针对特定帐户的[暴力](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures)攻击。
- 从多个位置的登录尝试。
- 从[受感染的设备](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)登录。
- 可疑 IP 地址。

**详细信息**：使用 Azure AD Premium [异常报告](/azure/active-directory/active-directory-view-access-usage-reports)。 制定相应的流程和过程，使 IT 管理员每天或按需（通常在事件响应方案中）运行这些报告。

**最佳做法**：安装一个主动监视系统，用于通知风险，并且可以根据业务需求调整风险等级（高、中或低）。   
**详细信息**：使用 [Azure AD 标识保护](/azure/active-directory/active-directory-identityprotection)，它会在自己的仪表板上标记当前风险并通过电子邮件发送每日摘要通知。 要帮助保护组织的标识，可以配置基于风险的策略，该策略可在达到指定风险级别时自动响应检测到的问题。

不主动监视其标识系统的组织将面临用户凭据泄露的风险。 如果不知道有人通过这些凭据实施可疑活动，组织就无法缓解这种类型的威胁。

## <a name="use-azure-ad-for-storage-authentication"></a>使用 Azure AD 进行存储身份验证
[Azure 存储空间](/azure/storage/common/storage-auth-aad)支持对 Blob 存储和队列存储 Azure AD 进行身份验证和授权。 使用 Azure AD 身份验证，可以使用 Azure 基于角色的访问控制向下授予用户、组和应用程序的特定权限，使其向下到单个 blob 容器或队列的作用域。

建议使用[Azure AD 对存储访问进行身份验证](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)。

## <a name="next-step"></a>后续步骤

有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](best-practices-and-patterns.md)。
