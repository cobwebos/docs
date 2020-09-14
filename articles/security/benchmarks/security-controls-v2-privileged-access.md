---
title: Azure 安全基准 V2-特权访问
description: Azure 安全基准 V2 特权访问
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059180"
---
# <a name="security-control-privileged-access"></a>安全控制：特权访问

特权访问涵盖了控制以保护对 Azure 租户和资源的特权访问。 这包括一系列控件，用于保护你的管理模型、管理帐户和特权访问工作站免受蓄意和无意的风险。

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1：保护和限制全局管理员

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-1 | 4.3、4。8 | AC-2 |

已分配到全局管理员角色的用户可以读取和修改 Azure AD 组织中的每项管理设置。 将每个订阅的 Azure 全局管理员帐户的数目限制为不超过两个。 Azure AD 中的最关键内置角色是全局管理员和特权角色管理员，因为分配给这两个角色的用户可以委派管理员角色：
- 全局管理员/公司管理员：具有此角色的用户有权访问 Azure AD 中的所有管理功能，以及使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可以管理 Azure AD 中的角色分配，以及 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，此角色允许管理 PIM 和管理单元的所有方面。

注意：如果你使用的是分配了特定特权权限的自定义角色，则你可能需要控制其他关键角色。 此外，您可能还希望将类似控件应用于关键业务资产的管理员帐户。  

可以使用 Azure AD Privileged Identity Management (PIM) 启用实时 (JIT) 对 Azure 资源和 Azure AD 的特权访问。 JIT 仅在用户需要权限任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还可以生成安全警报。

注意：除 Azure AD 身份验证外，某些 Azure 服务（如 Azure SQL）还支持本地用户身份验证。 这种类型的本地用户身份验证不是通过 Azure AD 管理的。 需要单独管理这些用户。

- [Azure AD 中的管理员角色权限](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure 自定义角色](../../role-based-access-control/custom-roles.md)

- [使用 Azure Privileged Identity Management 安全警报](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [使用 Azure 安全中心来监视标识和访问](../../security-center/security-center-identity-access.md)

**责任**：客户

**客户安全利益干系人**：

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2：定期查看和协调用户访问权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-2 | 4.1、16.9、16.10 | AC-2 |

定期查看用户帐户和访问权限，以确保用户帐户及其访问有效。 

使用 Azure AD 的标识和访问评审来管理组成员身份、访问企业应用程序和角色分配。 Azure AD 报告可以提供日志以帮助发现陈旧的帐户。 你还可以使用 Azure AD Privileged Identity Management 来创建访问评审报表工作流，以便于审核过程。

对于 Azure 服务和工作负荷级别的管理用户，应进行更频繁的用户和访问评审。 你还可以使用 Azure Privileged Identity Management 安全警报来检测创建管理员帐户的时间，以及查找过时或配置不正确的管理员帐户。 

注意：某些 Azure 服务（如 Azure SQL）支持不通过 Azure AD 管理的本地用户。 需要单独管理这些用户。

- [如何使用 PowerShell 获取 Azure AD 中的目录角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 获取 Azure AD 中目录角色的成员](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [使用 Azure 安全中心来监视标识和访问](../../security-center/security-center-identity-access.md)

- [使用 Azure Privileged Identity Management 安全警报](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [跨团队协调管理责任](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [了解 Azure AD 报告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 的标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management 查看 Azure AD 角色的访问权限](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure 安全中心-监视标识和访问](../../security-center/security-center-identity-access.md)

**责任**：客户

**客户安全利益干系人**：

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3：在 Azure AD 中设置紧急访问帐户

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

若要防止意外锁定 Azure AD 组织，请在不能使用正常管理帐户时设置紧急访问帐户进行访问。 紧急访问帐户通常具有很高的特权，不应将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。

你应确保证书 (例如密码、证书或智能卡) 用于紧急访问帐户，且仅对有权在紧急情况下使用这些凭据的个人保密。

- [在 Azure AD 中管理紧急访问帐户](../../active-directory/users-groups-roles/directory-emergency-access.md)

**责任**：客户

**客户安全利益干系人**：

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全操作中心 (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4：自动执行 Azure 标识和访问请求工作流

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-4 | 空值 | AC-2、AC 5、PM-10 |

使用 Azure AD 的权利管理功能自动执行 Azure 访问请求工作流，包括访问权限分配、审核和过期。 还支持双重或多阶段批准。  

- [什么是 Azure AD 权限管理](../../active-directory/governance/entitlement-management-overview.md)

- [如何设置访问请求和审批过程](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**责任**：客户

**客户安全利益干系人**：

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA 5：对管理任务使用高度安全的计算机

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA 5 | 4.6、11.6、12.12 | AC-2、SC-7 |

受保护的隔离工作站对于机密角色（如管理员、开发人员和关键服务操作员）的安全性至关重要。 使用高度安全的用户工作站和/或 Azure 堡垒执行管理任务：
- 使用 Azure Active Directory，Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 为管理任务部署安全的托管用户工作站。 可以集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线、受限的逻辑和网络访问。 

- 使用 Azure 堡垒功能通过 RDP 或 SSH 访问虚拟机。 Azure 堡垒是一种完全托管的 PaaS 服务，可以在不创建新虚拟网络的情况下为每个虚拟网络进行设置。 

- [了解 Azure 托管的安全工作站](../../active-directory/devices/concept-azure-managed-workstation.md)

- [部署安全的 Azure 托管工作站](../../active-directory/devices/howto-azure-managed-workstation.md)

- [使用 Azure 堡垒主机](../../bastion/bastion-create-host-portal.md)

**责任**：客户

**客户安全利益干系人**：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全操作中心 (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6：使用 Azure RBAC 向资源分配权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-6 | 14.6 | AC-2、AC 3 |

使用 Azure 基于角色的访问控制 (RBAC) 使你可以通过角色分配管理对 Azure 资源访问权限的权限。 你可以将这些角色分配给用户、组服务主体和托管标识。 某些资源有预定义的内置角色，可以通过 Azure CLI、Azure PowerShell 或 Azure 门户等工具来清点或查询这些角色。 

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

- [如何在 Azure 中配置 RBAC](../../role-based-access-control/role-assignments-portal.md)

**责任**：客户

**客户安全利益干系人**：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7：选择 Microsoft 支持的批准流程

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-7 | 16 | AC-2、AC 3、AC 4 |

在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供一项功能，使你可以显式查看和批准或拒绝每个客户数据访问请求。

- [了解客户密码箱](../fundamentals/customer-lockbox-overview.md)

**责任**：客户

**客户安全利益干系人**：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性和符合性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [标识和密钥](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

