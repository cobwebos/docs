---
title: Azure 安全基准 V2-特权访问
description: Azure 安全基准 V2 特权访问
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 68fdff9444286a7f304c3a3361ad33a02e87a282
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758364"
---
# <a name="security-control-v2-privileged-access"></a>安全控制 V2：特权访问

特权访问涵盖了控制以保护对 Azure 租户和资源的特权访问。 这包括一系列控件，用于保护你的管理模型、管理帐户和特权访问工作站免受蓄意和无意的风险。

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保护和限制高特权用户

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-1 | 4.3、4。8 | AC-2 |

限制高特权用户帐户的数量，并在提升的级别保护这些帐户。 Azure AD 中的最关键内置角色是全局管理员和特权角色管理员，因为分配给这两个角色的用户可以委派管理员角色。 利用这些特权，用户可以直接或间接读取和修改 Azure 环境中的每个资源：

- 全局管理员/公司管理员：具有此角色的用户有权访问 Azure AD 中的所有管理功能，以及使用 Azure AD 标识的服务。

- 特权角色管理员：具有此角色的用户可以管理 Azure AD 中的角色分配，以及 Azure AD Privileged Identity Management (PIM) 中的角色分配。 此外，此角色允许管理 PIM 和管理单元的所有方面。

注意：如果你使用的是分配了特定特权权限的自定义角色，则你可能需要控制其他关键角色。 此外，您可能还希望将类似控件应用于关键业务资产的管理员帐户。  

可以使用 Azure AD Privileged Identity Management (PIM) 启用实时 (JIT) 对 Azure 资源和 Azure AD 的特权访问。 JIT 仅在用户需要权限任务时授予临时权限。 当 Azure AD 组织中存在可疑或不安全的活动时，PIM 还可以生成安全警报。

- [Azure AD 中的管理员角色权限](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [使用 Azure Privileged Identity Management 安全警报](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制对关键业务系统的管理访问

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-2 | 13.2、2.10 | AC-2、SC-3、SC-7 |

通过限制向其授予对订阅和管理组的特权访问权限的帐户，隔离对关键系统的访问。 确保你还限制对对业务关键资产具有管理访问权限的管理、标识和安全系统的访问权限，例如 Active Directory 域控制器 (DCs) 、安全工具和系统管理工具，以及安装在业务关键系统上的代理。 泄露这些管理和安全系统的攻击者可以立即 weaponize 它们来损害业务关键资产。 

所有类型的访问控制都应与企业分段策略对齐，以确保一致的访问控制。 

确保分配不同于用于电子邮件、浏览和工作效率任务的标准用户帐户的单独特权帐户。

- [Azure 组件和参考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理组访问](../../governance/management-groups/overview.md#management-group-access)

- [Azure 订阅管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期查看和协调用户访问权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-3 | 4.1、16.9、16.10 | AC-2 |

定期查看用户帐户和访问分配，以确保帐户及其访问级别有效。 你可以使用 Azure AD 访问评审来查看组成员身份、对企业应用程序的访问权限以及角色分配。 Azure AD 报告可以提供日志以帮助发现陈旧的帐户。 你还可以使用 Azure AD Privileged Identity Management 来创建便于审核过程的访问评审报表工作流。
此外，Azure Privileged Identity Management 可以配置为在创建过多的管理员帐户时发出警报，并识别过时或配置不正确的管理员帐户。 

注意：某些 Azure 服务支持不通过 Azure AD 管理的本地用户和角色。 您必须单独管理这些用户。

- [在 Privileged Identity Management (PIM 中创建 Azure 资源角色的访问评审) ](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中设置紧急访问权限

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-4 | 16 | AC 2、CP 2 |

若要防止意外锁定 Azure AD 组织，请在不能使用正常管理帐户时设置紧急访问帐户进行访问。 紧急访问帐户通常具有很高的特权，不应将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。
你应确保证书 (例如密码、证书或智能卡) 用于紧急访问帐户，且仅对有权在紧急情况下使用这些凭据的个人保密。

- [在 Azure AD 中管理紧急访问帐户](../../active-directory/users-groups-roles/directory-emergency-access.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [ (SecOps 的安全操作) ](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA 5：自动化授权管理

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA 5 | 16 | AC-2、AC 5、PM-10 |

使用 Azure AD 的权利管理功能来自动执行访问请求工作流，包括访问权限分配、审核和过期。 还支持双重或多阶段批准。
- [什么是 Azure AD 访问评审](../../active-directory/governance/access-reviews-overview.md) 

- [什么是 Azure AD 权限管理](../../active-directory/governance/entitlement-management-overview.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特权访问工作站

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-6 | 4.6、11.6、12.12 | AC-2、SC-3、SC-7 |

受保护的隔离工作站对于机密角色（如管理员、开发人员和关键服务操作员）的安全性至关重要。 使用高度安全的用户工作站和/或 Azure 堡垒执行管理任务。 使用 Azure Active Directory，Microsoft Defender 高级威胁防护 (ATP) 和/或 Microsoft Intune 为管理任务部署安全的托管用户工作站。 可以集中管理安全的工作站来强制实施安全配置，包括强身份验证、软件和硬件基线以及受限的逻辑和网络访问。 

- [了解特权访问工作站](../../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特权访问工作站](../../active-directory/devices/howto-azure-managed-workstation.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [ (SecOps 的安全操作) ](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循足够的管理 (最低权限原则) 

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-7 | 14.6 | AC-2、AC-3、SC-3 |

Azure RBAC) 的 azure 基于角色的访问控制 (允许通过角色分配来管理 Azure 资源访问权限。 你可以将这些角色分配给用户、组服务主体和托管标识。 某些资源有预定义的内置角色，可以通过 Azure CLI、Azure PowerShell 和 Azure 门户等工具来清点或查询这些角色。 通过 Azure RBAC 分配给资源的特权应始终限制为角色所需的权限。 有限权限可补充 Azure AD Privileged Identity Management (PIM) 的实时 (JIT) 方法，并应定期查看这些特权。
必要时，请使用内置角色分配权限，并仅创建自定义角色。 

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

- [如何配置 Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 标识和访问评审](../../active-directory/governance/access-reviews-overview.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：选择 Microsoft 支持的批准流程 

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-8 | 16 | AC-2、AC 3、AC 4 |

在 Microsoft 需要访问客户数据的支持方案中，客户密码箱提供一项功能，使你可以显式查看和批准或拒绝每个客户数据访问请求。

- [了解客户密码箱](../fundamentals/customer-lockbox-overview.md)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [标识和密钥管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

