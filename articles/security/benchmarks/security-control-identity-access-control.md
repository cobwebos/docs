---
title: Azure 安全控制 - 标识和访问控制
description: Azure 安全控制标识和访问控制
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2df534842c6f6a922579e1128757ee4b7f46e006
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90058591"
---
# <a name="security-control-identity-and-access-control"></a>安全控制：标识和访问控制

标识和访问管理建议侧重于解决与以下方面相关的问题：基于标识的访问控制、锁定管理访问权限、对与标识相关的事件发出警报、异常帐户行为和基于角色的访问控制。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：维护管理帐户的清单

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.1 | 4.1 | 客户 |

Azure AD 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行即席查询，以发现属于管理组的成员的帐户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2：在适用的情况下更改默认密码

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.2 | 4.2 | 客户 |

Azure AD 没有默认密码。 其他需要密码的 Azure 资源会强制创建具有复杂性要求和最小密码长度的密码，该长度因服务而异。 你对可能使用默认密码的第三方应用程序和市场服务负责。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.3 | 4.3 | 客户 |

围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。

还可以通过使用 Microsoft 服务的 Azure AD Privileged Identity Management 特权角色和 Azure 资源管理器来启用实时/足够访问权限。 

- [详细了解 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：将单一登录 (SSO) 与 Azure Active Directory 配合使用

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.4 | 4.4 | 客户 |

只要有可能，请使用 Azure Active Directory SSO，而不是为每个服务配置单独的单独凭据。 请使用 Azure 安全中心标识和访问管理建议。

- [了解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure Active Directory 的访问使用多重身份验证

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.5 | 4.5、11.5、12.11、16。3 | 客户 |

启用 Azure AD MFA，并遵循 Azure 安全中心的标识和访问管理建议。

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.6 | 4.6、11.6、12.12 | 客户 |

使用 Paw () 配置了 MFA 的特权访问工作站，并将其配置为登录和配置 Azure 资源。

- [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：记录来自管理帐户的可疑活动并对其发出警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.7 | 4.8、4。9 | 客户 |

当环境中发生可疑活动或不安全活动时，使用 Azure Active Directory 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何确定标记为存在风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 安全中心内监视用户的标识和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从批准的位置管理 Azure 资源

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.8 | 11.7 | 客户 |

使用条件访问命名位置，仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.9 | 16.1、16.2、16.4、16.5、16.6 | 客户 |

使用 Azure Active Directory 作为中心身份验证和授权系统。 Azure AD 通过对静态数据和传输中数据使用强加密来保护数据。 Azure AD 还会对用户凭据进行加盐、哈希处理和安全存储操作。

- [如何创建和配置 Azure AD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期审查和协调用户访问

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.10 | 16.9、16.10 | 客户 |

Azure AD 提供日志来帮助发现过时的帐户。 此外，请使用 Azure 标识访问评审来有效管理组成员身份、对企业应用程序的访问和角色分配。 可以定期评审用户的访问权限，确保只有适当的用户才持续拥有访问权限。 

- [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：监视尝试访问已停用凭据的行为

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.11 | 16.12 | 客户 |

你有权访问 Azure AD 登录活动、审核和风险事件日志源，从而使你可以与任何 SIEM/监视工具集成。

可以通过为 Azure Active Directory 用户帐户创建诊断设置，并将审核日志和登录日志发送到 Log Analytics 工作区，来简化此过程。 你可以在 Log Analytics 工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：针对帐户登录行为偏差发出警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.12 | 16.13 | 客户 |

使用 Azure AD 风险和标识保护功能来配置对检测到的与用户标识相关的可疑操作的自动响应。 还可以将数据引入 Azure Sentinel 中以便进一步调查。

- [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用标识保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何加入 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支持场合下为 Microsoft 提供对相关客户数据的访问权限

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 3.13 | 16 | 客户 |

在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供了一个用于查看、批准或拒绝客户数据访问请求的接口。

- [了解客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[数据保护](security-control-data-protection.md)