---
title: Azure 标识和访问安全最佳实践 | Microsoft 文档
description: 本文提供一系列有关使用内置 Azure 功能进行标识管理和访问控制的最佳实践。
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: b354fda4d23d13b8defed4381335ab147bbece5d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835895"
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

撰写本文的目的是，以引导你了解我们的一些核心功能和服务的“[保护标识基础结构的 5 个步骤](steps-secure-identity.md)”清单为指导，提供在部署后实现更可靠的安全状况的总体路线图。

看法和技术将随着时间改变，本文会定期更新以反映这些更改。

本文中介绍的 Azure 标识管理和访问控制安全最佳实践包括：

* 将标识视为主要安全边界
* 集中化标识管理
* 管理已连接的租户
* 启用单一登录
* 启用条件访问
* 计划例程安全改进
* 启用密码管理
* 对用户强制执行多重身份验证
* 使用基于角色的访问控制
* 降低特权帐户的泄露风险
* 控制资源所在的位置
* 使用 Azure AD 进行存储身份验证

## <a name="treat-identity-as-the-primary-security-perimeter"></a>将标识视为主要安全边界

许多人认为标识是主要安全边界。 这与以网络安全为重点的传统做法不同。 网络边界出现越来越多的漏洞，在 [BYOD](https://aka.ms/byodcg) 设备和云应用程序激增之前相比，边界防御不再那样有效。

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 Azure AD 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 它将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。

以下部分列出了使用 Azure AD 实现标识和访问安全性的最佳做法。

**最佳做法**：围绕用户和服务标识进行安全控制和检测。
**详细信息**：使用 Azure AD 并置控制和标识。

## <a name="centralize-identity-management"></a>集中化标识管理

在[混合标识](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)方案中，我们建议集成本地目录和云目录。 通过集成，IT 团队可以在一个位置集中管理帐户，而不管帐户是在哪里创建的。 集成还通过提供用于访问云和本地资源的通用标识，从而帮助用户提高工作效率。

**最佳做法**：建立一个 Azure AD 实例。 一致性和一个权威源不仅会提高简明性，还会减少人为错误和配置复杂性带来的安全风险。
**详细信息**：指定一个 Azure AD 目录作为企业帐户和组织帐户的权威源。

**最佳做法**：将本地目录与 Azure AD 进行集成。  
**详细信息**：使用 [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) 将本地目录与云目录同步。

> [!Note]
> 存在[影响 Azure AD Connect 性能的因素](../../active-directory/hybrid/plan-connect-performance-factors.md)。 确保 Azure AD Connect 有足够的容量来防止性能不佳的系统影响安全性和工作效率。 大型或复杂的组织（预配超过 100,000 个对象的组织）应遵循[建议](../../active-directory/hybrid/whatis-hybrid-identity.md)来优化其 Azure AD Connect 实现。

**最佳做法**：不要将现有 Active Directory 实例中拥有高权限的帐户同步到 Azure AD。
**详细信息**：不要更改用于筛选掉这些帐户的默认 [Azure AD Connect 配置](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)。 这种配置降低了对手从云转向本地资产的风险（这可能引发重大事件）。

**最佳做法**：启用密码哈希同步。  
**详细信息**：密码哈希同步是用于将用户密码哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例的功能。 此同步有助于防止重放先前攻击中泄露的凭据。

即使决定使用 Active Directory 联合身份验证服务 (AD FS) 或其他标识提供者进行联合身份验证，也可以选择性地设置密码哈希同步作为备用机制，以应对本地服务器发生故障或临时不可用的情况。 借助此同步，用户可以使用与登录本地 Active Directory 实例相同的密码来登录服务。 如果用户对其他未连接到 Azure AD 的服务使用过相同的电子邮件地址和密码，此同步还可便于标识保护将同步的密码哈希与已知被盗用的密码进行比较，从而检测被盗用的凭据。

有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)。

**最佳做法**：对于新的应用开发，使用 Azure AD 进行身份验证。
**详细信息**：使用正确的功能来支持身份验证：

  - 面向员工的 Azure AD
  - 面向来宾用户和外部合作伙伴的 [Azure AD B2B](../../active-directory/b2b/index.yml)
  - 用于控制客户在使用应用时如何注册、登录和管理配置文件的 [Azure AD B2C](../../active-directory-b2c/index.yml)

未将其本地标识与云标识集成的组织在管理帐户方面可能开销更大。 这种开销增加了出错和安全漏洞的可能性。

> [!Note]
> 你需要选择关键帐户将驻留在哪些目录中，以及所使用的管理工作站是由新的云服务托管，还是由现有进程托管。 使用现有的管理和标识预配流程可以降低一些风险，但也可能会造成攻击者入侵本地帐户并转向云的风险。 不妨对不同的角色（例如，IT 管理员与业务部门管理员）使用不同的策略。 您有两种选择： 第一种选择是，创建不与本地 Active Directory 实例同步的 Azure AD 帐户。 将管理工作站加入到 Azure AD，这样可以使用 Microsoft Intune 进行管理和修补。 第二种选择是，通过同步到本地 Active Directory 实例来使用现有的管理员帐户。 使用 Active Directory 域中的现有工作站来实现管理和安全性。

## <a name="manage-connected-tenants"></a>管理已连接的租户
你的安全组织需要能够查看订阅来评估风险，并确定是否遵循了组织的策略和任何法规要求。 你应确保安全组织能够查看所有（通过 [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 或[站点到站点 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)）连接到生产环境和网络的订阅。 Azure AD 中的[全局管理员/公司管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)可以将自己的访问权限提升为[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色，并查看所有连接到环境的订阅和管理组。

请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)，以确保你和你的安全组可以查看所有连接到环境的订阅或管理组。 你应该在评估风险后撤消此提升的访问权限。

## <a name="enable-single-sign-on"></a>启用单一登录

在移动优先、云优先的世界中，你希望能够从任意位置实现对设备、应用和服务的单一登录 (SSO)，以便你的用户随时随地都能高效工作。 如果要管理多个标识解决方案，则不仅会给 IT 人员造成管理问题，而且用户还必须记住多个密码。

通过对所有应用和资源使用相同的标识解决方案，可以实现 SSO。 并且不论资源是位于本地还是云中，用户均可以使用相同凭据集登录和访问所需资源。

**最佳做法**：启用 SSO。  
**详细信息**：Azure AD [将本地 Active Directory](/azure/active-directory/connect/active-directory-aadconnect) 扩展到云。 用户可以将他们的主要工作或学校帐户用于他们加入域的设备、公司资源以及完成工作所需的所有 Web 和 SaaS 应用程序。 用户无需记住多组用户名和密码，系统会根据组织的组成员身份和员工身份的状态，自动预配（或取消设置）应用程序访问权限。 可以针对库应用或者通过 [Azure AD 应用程序代理](/azure/active-directory/active-directory-application-proxy-get-started)自行开发和发布的本地应用控制访问权限。

用户可使用 SSO 基于 Azure AD 中的工作或学校帐户访问 [SaaS 应用程序](/azure/active-directory/active-directory-appssoaccess-whatis)。 这不仅适用于 Microsoft SaaS 应用，还适用于其他应用，例如 [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) 和 [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial)。 应用程序可配置为使用 Azure AD 作为[基于 SAML 的标识](/azure/active-directory/fundamentals-identity)提供者。 作为安全控制机制，Azure AD 不会发出允许用户登录应用程序的令牌，除非用户已通过 Azure AD 获取了访问权限。 可以直接或者通过用户所属的组授予访问权限。

如果组织没有通过创建通用标识来为用户和应用程序实现 SSO，那么用户拥有多个密码的情况就更容易出现。 这种情况增加了用户重复使用同一密码或使用弱密码的可能性。

## <a name="turn-on-conditional-access"></a>启用条件访问

用户可能会从任意位置使用各种设备和应用访问组织的资源。 作为一名 IT 管理员，你需要确保这些设备符合安全性和符合性标准。 仅关注谁可以访问资源不再能满足需求。

为了平衡安全性与工作效率，在做出访问控制决策之前，需要考虑如何访问资源。 使用 Azure AD 条件访问，可以满足这一需求。 使用条件访问，可以根据访问云应用的条件做出自动访问控制决策。

**最佳做法**：管理和控制对公司资源的访问。  
**详细信息**：根据 SaaS 应用和 Azure AD 连接的应用的组、位置和应用敏感度，配置通用 Azure AD [条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)。

**最佳做法**：阻止旧身份验证协议。
**详细信息**：攻击者每天都在利用旧协议中的弱点，尤其是密码喷射攻击。 配置条件访问来[阻止旧协议](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md)。

## <a name="plan-for-routine-security-improvements"></a>计划例程安全改进

安全性一直在不断发展，在云和标识管理框架中构建一种定期显示安全性发展并发现保护环境的新方法是很重要的。

标识安全分数是 Microsoft 发布的一组建议的安全控制，旨在为你提供一个数字分数，以便客观地度量你的安全状况，并帮助计划未来的安全改进。 你还可以查看你的分数与其他行业分数的比较，以及你自己的分数在一段时间内的趋势。

**最佳做法**：根据你所在行业的最佳做法来计划例程安全评审和改进。
**详细信息**：使用标识安全分数功能对你在一段时间内的改进进行排名。

## <a name="enable-password-management"></a>启用密码管理

如果有多个租户或者你想要允许用户[重置自己的密码](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)，则必须使用适当的安全策略来防止滥用。

**最佳做法**：为用户设置自助式密码重置 (SSPR)。  
**详细信息**：使用 Azure AD [自助式密码重置](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)功能。

**最佳做法**：监视是否在使用 SSPR 及其使用情况。  
**详细信息**：通过使用 Azure AD [密码重置注册活动报表](/azure/active-directory/active-directory-passwords-get-insights)监视正在注册的用户。 Azure AD 提供的报表功能可帮助使用预生成的报表来回答问题。 如果有相应的授权，还可以创建自定义查询。

**最佳做法**：将基于云的密码策略扩展到本地基础结构。
**详细信息**：通过对本地密码更改执行与对基于云的密码更改执行的相同检查，增强组织中的密码策略。 为本地 Windows Server Active Directory 代理安装 [Azure AD 密码保护](/azure/active-directory/authentication/concept-password-ban-bad)，以将禁止的密码列表扩展到现有基础结构。 更改、设置或重置本地密码的用户或管理员必须与仅限云的用户遵循相同的密码策略。

## <a name="enforce-multi-factor-verification-for-users"></a>对用户强制执行多重身份验证

建议对所有用户要求进行双重验证。 这包括组织中的管理员和其他人员，如果他们的帐户泄露，可能会产生重大影响（例如，财务官员）。

要求双重验证有多种选项。 最佳选项取决于你的目标、正在运行的 Azure AD 版本以及许可计划。 请参阅[如何要求对用户进行双重验证](/azure/active-directory/authentication/howto-mfa-userstates)了解最佳选项。 有关许可和定价的详细信息，请参阅 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure 多重身份验证](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)定价页。

以下是启用双重验证的选项和优势：

**选项 1**：使用 Azure AD 安全默认值为所有用户和登录方法启用 MFA 优势：借助此选项，可以轻松、快速地为环境中的所有用户强制执行 MFA，同时采用严格的策略来执行以下操作：

* 质询管理帐户和管理登录机制
* 要求通过 Microsoft Authenticator 对所有用户进行 MFA 质询
* 限制旧身份验证协议。

此方法可用于所有许可层，但不能与现有的条件访问策略混合使用。 你可以在[Azure AD 安全默认值](/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)中查找详细信息

**选项 2**：[通过更改用户状态启用多重身份验证](../../active-directory/authentication/howto-mfa-userstates.md)。   
**优势**：这是要求进行双重验证的传统方法。 它适用于[云中的 Azure 多重身份验证和 Azure 多重身份验证服务器](/azure/active-directory/authentication/concept-mfa-whichversion)。 使用此方法要求用户在每次登录时都执行双重验证，并且会替代条件访问策略。

若要确定需要在哪里启用多重身份验证，请参阅 [Azure MFA 的哪个版本适合我的组织？](/azure/active-directory/authentication/concept-mfa-whichversion)。

**选项 3**：[使用条件访问策略启用多重身份验证](/azure/active-directory/authentication/howto-mfa-getstarted)。
**优势**：借助此选项，可以使用[条件访问](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)在特定条件下提示进行双重验证。 特定条件可以是用户从不同位置、不受信任的设备或你认为存在风险的应用程序登录。 定义要求双重验证的特定条件可以避免不断提示用户这种令人不快的用户体验。

这是为用户启用双重验证最灵活的方式。 启用条件访问策略只适用于云中的 Azure 多重身份验证，这是 Azure AD 的一项高级功能。 有关此方法的详细信息，请参阅[部署基于云的 Azure 多重身份验证](/azure/active-directory/authentication/howto-mfa-getstarted)。

选项 4：通过评估[基于风险的条件访问策略](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md)，使用条件访问策略启用多重身份验证。   
**优势**：此选项使你能够：

* 检测影响组织标识的潜在漏洞。
* 配置自动响应与组织标识相关的可疑操作。
* 调查可疑事件，并采取适当的措施进行解决。

此方法使用“Azure AD 标识保护”风险评估来确定是否需要基于所有云应用程序的用户和登录风险进行双重验证。 此方法需要 Azure Active Directory P2 授权。 有关此方法的详细信息，请参阅 [Azure Active Directory 标识保护](/azure/active-directory/identity-protection/overview)。

> [!Note]
> 选项2，通过更改用户状态启用多重身份验证会替代条件访问策略。 由于选项3和4使用条件性访问策略，因此不能将选项2与它们一起使用。

未添加额外标识保护层（如双重验证）的组织将更容易受到凭据窃取攻击。 凭据窃取攻击可能导致数据泄漏。

## <a name="use-role-based-access-control"></a>使用基于角色的访问控制

对于任何使用云的组织而言，云资源的访问管理至关重要。 Azure [RBAC) 的基于角色的访问控制 (](/azure/role-based-access-control/overview)可帮助你管理有权访问 Azure 资源的人员、他们可以对这些资源执行哪些操作以及他们有权访问哪些区域。

在 Azure 中指定负责特定职能的组或各个角色有助于避免混乱，这些混乱可能会导致造成安全风险的人为错误和自动化错误。 对于想要实施数据访问安全策略的组织而言，必须根据[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全策略限制访问权限。

你的安全团队需要能够查看 Azure 资源，以便评估和修正风险。 如果安全团队具有运营职责，则需要额外的权限来完成他们的作业。

可以使用 [RBAC](/azure/role-based-access-control/overview) 在一定范围内向用户、组和应用分配权限。 角色分配的范围可以是订阅、资源组或单个资源。

**最佳做法**：在团队中分离职责，只向用户授予执行作业所需的访问权限。 只允许在特定范围内执行特定操作，而不要在 Azure 订阅或资源中向每个人都授予无限制权限。
**详细信息**：使用 azure 中的[azure 内置角色](/azure/role-based-access-control/built-in-roles)向用户分配权限。

> [!Note]
> 特定的权限会造成不必要的复杂性和混乱，进而积累为很难在不担心造成破坏的情况下进行修复的“旧”配置。 避免特定于资源的权限。 而是将管理组用于企业范围内的权限，并将资源组用于订阅中的权限。 避免用户特定的权限。 而是向 Azure AD 中的组分配权限。

**最佳做法**：向具有 Azure 职责的安全团队授予对 Azure 资源的访问权限，以便他们可以评估和修正风险。
**详细信息**：向安全团队授予 RBAC [安全读取者](/azure/role-based-access-control/built-in-roles#security-reader)角色。 可以使用根管理组或段管理组，具体视职责范围而定：

* 根管理组：用于负责所有企业资源的团队
* 段管理组：用于范围有限的团队（通常是由于法规或其他组织边界所致）

**最佳做法**：向具有直接运营职责的安全团队授予适当的权限。
**详细信息**：审阅 RBAC 内置角色，以进行适当的角色分配。 如果内置角色不满足组织的特定需求，则可以创建[Azure 自定义角色](/azure/role-based-access-control/custom-roles)。 与内置角色一样，可以在订阅、资源组和资源范围内向用户、组和服务主体分配自定义角色。

**最佳做法**：向需要的安全角色授予 Azure 安全中心访问权限。 使用安全中心，安全团队可以快速发现和修正风险。
**详细信息**：将具有这些需求的安全团队添加到 RBAC [安全管理员](/azure/role-based-access-control/built-in-roles#security-admin)角色，这样他们就可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议，并能消除警报和建议。 为此，可以使用根管理组或段管理组，具体视职责范围而定。

如果组织没有使用 RBAC 等功能强制执行数据访问控制，可能会向用户分配不必要的权限。 允许用户访问他们不应该有权访问的数据类型（例如，对业务有重大影响的数据）可能会导致数据泄露。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特权帐户的泄露风险

保护特权访问是保护业务资产的首要步骤。 减少拥有访问权限的人员以保护信息或资源安全，这样可以减小恶意用户获得访问权限，或者已授权用户无意中影响敏感资源的可能性。

特权帐户是指掌控和管理 IT 系统的帐户。 网络攻击者会攻击这些帐户来获取组织数据和系统的访问权限。 为了保护特权访问，应隔离此类帐户和系统，使其免受恶意用户的威胁。

建议制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 有关创建详细路线图以保护在 Azure AD、Microsoft Azure、Office 365 和其他云服务中托管或报告的标识和访问的信息，请查看[确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)。

以下内容总结了[确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)中介绍的最佳做法：

**最佳做法**：管理、控制和监视对特权帐户的访问。   
**详细信息**：启用 [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)。 启用 Privileged Identity Management 以后，会收到有关特权访问角色更改的通知电子邮件。 向目录中的高特权角色添加更多用户时，这些通知相当于早期警告。

**最佳做法**：确保所有关键管理员帐户都是托管的 Azure AD 帐户。
**详细信息**：从关键管理员角色中删除所有使用者帐户（例如，hotmail.com、live.com 和 outlook.com 等 Microsoft 帐户）。

**最佳做法**：确保所有关键管理员角色都有一个单独的帐户来执行管理任务，以免发生网络钓鱼和其他入侵管理权限的攻击。
**详细信息**：创建一个单独的管理员帐户，向其分配执行管理任务所需的权限。 禁止将这些管理帐户用于日常效率提升工具（如 Microsoft Office 365 电子邮件）或任意 Web 浏览。

**最佳做法**：对特许权限高的角色中的帐户进行标识和分类。   
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

**最佳做法**：准备“破窗”流程，以备紧急情况时使用。
**详细信息**：按照[确保 Azure AD 中混合部署和云部署的特权访问安全性](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)中的步骤操作。

**最佳做法**：要求所有关键管理员帐户都是无密码的（首选），或要求进行多重身份验证。
**详细信息**：使用 [Microsoft Authenticator 应用](/azure/active-directory/authentication/howto-authentication-phone-sign-in)登录任何 Azure AD 帐户，而不需要使用密码。 与 [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 一样，Microsoft Authenticator 使用基于密钥的身份验证来启用与设备绑定的用户凭据，并使用生物识别身份验证或 PIN。

对于永久分配给一个或多个 Azure AD 管理员角色的所有个人用户，要求其在登录时进行多重身份验证：全局管理员、特权角色管理员、Exchange Online 管理员和 SharePoint Online 管理员。 [为管理员帐户启用多重身份验证](/azure/active-directory/authentication/howto-mfa-userstates)，并确保管理员帐户用户已注册。

**最佳做法**：对于关键管理员帐户，需要有不允许执行生产任务（例如，浏览和电子邮件）的管理工作站。 这会保护你的管理员帐户免受使用浏览和电子邮件的攻击途径的侵害，并大大降低发生重大事件的风险。
**详细信息**：使用管理工作站。 选择工作站安全级别：

- 高度安全的效率提升设备为浏览和其他效率提升任务提供高级安全性。
- [特权访问工作站 (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 为敏感任务提供免受 Internet 攻击和威胁攻击途径侵害的专用操作系统。

**最佳做法**：在员工离开组织时，取消设置管理员帐户。
**详细信息**：准备一个流程，在员工离开组织时禁用或删除管理员帐户。

**最佳做法**：使用最新的攻击技术定期测试管理员帐户。
**详细信息**：使用 Office 365 攻击模拟器或第三方产品/服务在组织中运行真实的攻击方案。 这样有助于在真正攻击发生之前发现易受攻击的用户。

**最佳做法**：采取措施来缓解最常用的攻击技术的冲击。  
**详细信息**：[确定管理角色中那些需要切换到工作或学校帐户的 Microsoft 帐户](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

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

**最佳做法**：采用一种方法来确定：

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
[Azure 存储](/azure/storage/common/storage-auth-aad)支持使用 Azure AD 对 Blob 存储和队列存储进行身份验证和授权。 借助 Azure AD 身份验证，可以使用基于 Azure 角色的访问控制向用户、组和应用（一直到各个 Blob 容器或队列的范围）授予特定权限。

建议使用 [Azure AD 验证对存储的访问](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)。

## <a name="next-step"></a>后续步骤

有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](best-practices-and-patterns.md)。
