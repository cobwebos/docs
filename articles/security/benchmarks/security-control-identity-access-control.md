---
title: Azure 安全控制 - 标识和访问控制
description: Azure 安全控制标识和访问控制
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408531"
---
# <a name="security-control-identity-and-access-control"></a>安全控制：身份和访问控制

身份和访问管理建议侧重于解决与基于身份的访问控制相关的问题、锁定管理访问、对与身份相关的事件发出警报、异常帐户行为和基于角色的访问控制。

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1： 维持管理帐户的清单

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.1 | 4.1 | 客户 |

Azure AD 具有必须显式分配且可查询的内置角色。 使用 Azure AD PowerShell 模块执行临时查询以发现属于管理组成员的帐户。

- [如何使用 PowerShell 在 Azure AD 中获取目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2： 在适用的情况下更改默认密码

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.2 | 4.2 | 客户 |

Azure AD 没有默认密码的概念。 需要密码的其他 Azure 资源强制创建具有复杂性要求和最小密码长度的密码，具体取决于服务。 您负责可能使用默认密码的第三方应用程序和市场服务。

## <a name="33-use-dedicated-administrative-accounts"></a>3.3： 使用专用管理帐户

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.3 | 4.3 | 客户 |

围绕使用专用管理帐户创建标准操作程序。 使用 Azure 安全中心标识和访问管理监视管理帐户数。

还可以通过使用 Microsoft 服务和 Azure 资源管理器的 Azure AD 特权标识管理特权角色启用"及时/刚刚足够"访问。 

- [了解有关特权标识管理的更多信息](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4： 使用 Azure 活动目录的单一登录 （SSO）

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.4 | 4.4 | 客户 |

在可能的情况下，使用 Azure 活动目录 SSO 而不是配置每个服务的各个独立凭据。 使用 Azure 安全中心标识和访问管理建议。

- [使用 Azure AD 了解 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：对所有基于 Azure 活动目录的访问使用多重身份验证

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | 客户 |

启用 Azure AD MFA 并遵循 Azure 安全中心标识和访问管理建议。

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 安全中心内监视标识和访问](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6： 对所有管理任务使用专用计算机（特权访问工作站）

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | 客户 |

使用配置为登录和配置 Azure 资源的 MFA 的 PAW（特权访问工作站）。

- [了解特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中启用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7： 从管理帐户记录和警报可疑活动

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.7 | 4.8, 4.9 | 客户 |

当环境中发生可疑或不安全活动时，请使用 Azure Active Directory 安全报告生成日志和警报。 使用 Azure 安全中心监视标识和访问活动。

- [如何识别标记为有风险活动的 Azure AD 用户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何监视 Azure 安全中心中的用户身份和访问活动](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：仅从已批准的位置管理 Azure 资源

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.8 | 11.7 | 客户 |

使用条件访问命名位置仅允许从 IP 地址范围或国家/地区的特定逻辑分组进行访问。

- [如何在 Azure 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9： 使用 Azure 活动目录

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | 客户 |

使用 Azure 活动目录 （AAD） 作为中央身份验证和授权系统。 AAD 对静态和传输中的数据使用强加密来保护数据。 AAD 还对用户凭据进行盐渍、哈希和安全地存储。

- [如何创建和配置 AAD 实例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10： 定期审查和协调用户访问

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.10 | 16.9, 16.10 | 客户 |

Azure AD 提供日志以帮助发现陈旧的帐户。 此外，使用 Azure 标识访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。 可以定期查看用户访问权限，以确保只有正确的用户才能继续访问。 

- [了解 Azure AD 报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 标识访问审核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11： 监视访问已停用凭据的尝试

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.11 | 16.12 | 客户 |

您可以访问 Azure AD 登录活动、审核和风险事件日志源，该源允许您与任何 SIEM/监视工具集成。

您可以通过为 Azure 活动目录用户帐户创建诊断设置并将审核日志和登录日志发送到日志分析工作区来简化此过程。 您可以在日志分析工作区中配置所需的警报。

- [如何将 Azure 活动日志集成到 Azure 监视器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12： 帐户登录行为偏差警报

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.12 | 16.13 | 客户 |

使用 Azure AD 风险和标识保护功能配置自动响应，以检测与用户身份相关的可疑操作。 您还可以将数据引入 Azure 哨兵以进行进一步调查。

- [如何查看 Azure AD 风险登录](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何配置和启用身份保护风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何登上 Azure 哨兵](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13： 在支持方案期间向 Microsoft 提供对相关客户数据的访问

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 3.13 | 16 | 客户 |

在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供了一个界面，供您查看、批准或拒绝客户数据访问请求。

- [了解客户密码箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[数据保护](security-control-data-protection.md)