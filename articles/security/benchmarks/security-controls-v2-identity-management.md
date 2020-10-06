---
title: Azure 安全准则 V2-标识管理
description: Azure 安全基准 V2 标识管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e7447d03fc231d8d96b71c7d944a380c10add0d0
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757930"
---
# <a name="security-control-v2-identity-management"></a>安全控制 V2：标识管理

标识管理涵盖使用 Azure Active Directory 建立安全标识和访问控制的控件。 这包括使用单一登录、强身份验证、托管标识 (和服务原则) 用于应用程序、条件性访问和帐户异常监视。

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：标准化 Azure Active Directory 作为中心标识和身份验证系统

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-1 | 16.1、16.2、16.4、16。5 | IA-2、IA-8、AC-2、AC 3 |

Azure Active Directory (Azure AD) 是 Azure 的默认标识和访问管理服务。 你应该在 Azure AD 上标准化，以便在中管理你的组织的标识和访问管理：
- Microsoft 云资源，如 Azure 门户、Azure 存储、Azure 虚拟机 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 应用程序。

- 你组织的资源，如 Azure 上的应用程序或你的公司网络资源。

保护 Azure AD 在你的组织的云安全实践中应具有较高的优先级。 Azure AD 提供标识安全分数，有助于根据 Microsoft 的最佳实践建议评估标识安全状况。 使用评分来测量您的配置与最佳实践建议的匹配程度，并改善您的安全状况。

注意： Azure AD 支持外部标识提供程序，这些提供程序允许没有 Microsoft 帐户的用户使用其外部标识登录到其应用程序和资源。

- [Azure AD 中的租户](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [如何创建和配置 Azure AD 实例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [定义 Azure AD 租户](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [为应用程序使用外部标识提供程序](/azure/active-directory/b2b/identity-providers)

- [Azure AD 中的标识安全分数是什么](../../active-directory/fundamentals/identity-secure-score.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全自动地管理应用程序标识

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-2 | 空值 | AC-2、AC 3、IA-2、IA-4、IA-9 |

对于非人力帐户（如服务或自动化），请使用 Azure 托管标识，而不是创建功能更强大的人工帐户来访问资源或执行代码。 Azure 托管标识可以对支持 Azure AD 身份验证的 Azure 服务和资源进行身份验证。 通过预定义的访问授权规则启用身份验证，以避免在源代码或配置文件中使用硬编码的凭据。 

对于不支持托管标识的服务，请使用 Azure AD 在资源级别创建具有受限权限的服务主体。  建议使用证书凭据配置服务主体，并回退到客户端密码。 在这两种情况下，可以将 Azure Key Vault 与 Azure 托管标识结合使用，以便运行时环境 (例如 Azure function) 可以从密钥保管库中检索凭据。

- [Azure 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)

- [支持 Azure 资源托管标识的服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps)

- [使用证书创建服务主体](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

使用 Azure Key Vault 进行安全主体注册：身份验证 # 授权-a 安全主体-访问密钥保管库

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 单一登录 (SSO) 用于应用程序访问

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-3 | 4.4 | IA-2、IA-4 |

Azure AD 提供对 Azure 资源、云应用程序和本地应用程序的标识和访问管理。 身份验证和访问管理适用于企业标识，例如员工、合作伙伴、供应商和供应商等外部标识。

使用 Azure AD 单一登录 (SSO) 管理对本地和云中的组织数据和资源的访问。 将所有用户、应用程序和设备连接到 Azure AD，以便进行无缝、安全的访问和更好的可见性和控制。 

- [了解 Azure AD 的应用程序 SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：对基于 Azure Active Directory 的所有访问使用强身份验证控制

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-4 | 4.2、4.4 4.5、11.5、12.11、16。3 | AC-2、AC 3、IA-2、IA-4 |

Azure AD 通过多重身份验证 (MFA) 和强无密码方法支持强身份验证控制。  
- 多重身份验证：启用 Azure AD MFA，并按照 Azure 安全中心的标识和访问管理建议为你的 MFA 设置。 可以基于登录条件和风险因素，对所有用户、选择用户或按用户级别强制执行 MFA。 

- 无密码 authentication：有三个无密码 authentication 选项可用： Windows Hello 企业版、Microsoft Authenticator 应用和本地身份验证方法（如智能卡）。 

对于管理员和特权用户，请确保使用强身份验证方法的最高级别，然后将相应的强身份验证策略推出给其他用户。

如果仍使用传统的基于密码的身份验证进行 Azure AD 身份验证，请注意，直接在) Azure 中创建的仅限云的帐户 (的用户帐户具有默认的基准密码策略。  (来自本地 Active Directory) 的用户帐户的混合帐户遵循本地密码策略。 使用基于密码的身份验证时，Azure AD 提供密码保护功能，以防止用户设置容易猜测的密码。 Microsoft 提供了基于遥测进行更新的禁止密码的全局列表，客户可以根据其需要 (例如品牌、文化引用等 ) 来补充列表。 此密码保护可用于仅限云的帐户和混合帐户。 

注意：仅基于密码凭据的身份验证容易受到常见的攻击方法。 为了提高安全性，请使用强身份验证，如 MFA 和强密码策略。 对于可能具有默认密码的第三方应用程序和 marketplace 服务，应在初始服务设置期间更改这些设置。 

- [如何在 Azure 中启用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的无密码 authentication 选项简介](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 默认密码策略](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密码保护消除错误密码](../../active-directory/authentication/concept-password-ban-bad.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：监视并提醒帐户异常

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-5 | 4.8、4.9、16.12、16.13 | AC-2、AC 3、AC 7、AU 6 |

Azure AD 提供以下数据源： 
-   登录 - 登录报告提供有关托管应用程序的使用情况和用户登录活动的信息。

-   审核日志-通过日志提供通过 Azure AD 中的各种功能所做的所有更改的跟踪。 记录更改的示例审核日志包括添加或删除用户、应用、组、角色和策略。

-   风险登录 - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。

-   已标记为存在风险的用户 - 风险用户是指可能已泄露的用户帐户。

这些数据源可以与 Azure Monitor、Azure Sentinel 或第三方 SIEM 系统集成。

Azure 安全中心还可以针对某些可疑活动（如过量身份验证尝试次数过多）发出警报，以及订阅中的不推荐使用的帐户。 

Azure 高级威胁防护 (ATP) 是一种安全解决方案，可使用本地 Active Directory 信号来识别、检测和调查高级威胁、泄露身份和恶意内部操作。

- [Azure AD 中的审核活动报表](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何查看 Azure AD 风险登录](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何确定标记为存在风险活动的 Azure AD 用户](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](../../security-center/security-center-identity-access.md)

- [Azure 安全中心的威胁智能保护模块中的警报](/azure/security-center/alerts-reference)

- [如何将 Azure 活动日志集成到 Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [连接 Azure AD Identity Protection 的数据](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure 高级威胁防护](/azure-advanced-threat-protection/what-is-atp)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根据条件限制 Azure 资源访问

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-6 | 空值 | AC-2、AC 3 |

基于用户定义的条件，使用 Azure AD 的条件性访问权限进行更精细的访问控制，例如要求用户从特定 IP 范围登录到使用 MFA。 还可以在不同用例的 Azure AD 条件访问策略中使用粒度身份验证会话管理。 

- [Azure 条件访问概述](../../active-directory/conditional-access/overview.md)

- [常见条件访问策略](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用条件访问配置身份验证会话管理](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [威胁情报](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除意外的凭据公开

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-7 | 18.1, 18.7 | IA-5 |

实现 Azure DevOps Credential 扫描器来识别代码中的凭据。 凭据扫描器还鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。

对于 GitHub，你可以使用本机密钥扫描功能来识别代码中的凭据或其他形式的机密。

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub 机密扫描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8：保证用户对旧应用程序的访问权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| IM-8 | 14.6 | AC-2、AC-3、SC-11 |

确保为旧版应用程序和存储和处理的数据提供新式访问控制和会话监视。 虽然 Vpn 通常用于访问旧版应用程序，但它们通常仅具有基本的访问控制和有限的会话监视。

Azure AD 应用程序代理可让你使用单一登录 (SSO) 向远程用户发布旧的本地应用程序，同时使用 Azure AD 条件访问显式验证远程用户和设备的可信任性。 

另外，Microsoft Cloud App Security 是一种云访问安全代理 (CASB) 服务，它可提供控制，用于监视用户的应用程序会话，并为旧的本地应用程序和云软件作为服务 (SaaS) 应用程序) 提供阻止 (操作。 

- [Azure AD 应用程序代理](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Microsoft Cloud App Security 最佳实践](/cloud-app-security/best-practices)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
