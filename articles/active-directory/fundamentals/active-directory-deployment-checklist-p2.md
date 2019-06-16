---
title: Azure AD 部署清单
description: Azure Active Directory 功能部署清单
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e668a5238859d8cd8c2a7797200a12197ce72be9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110477"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Azure Active Directory 功能部署指南

为组织部署 Azure Active Directory (Azure AD) 并确保其安全，这似乎令人望而生畏。 本文列出了一些常见任务，如果客户在 30、60、90 天或更长时间内分阶段完成这些任务，则有助于增强其安全态势。 即使已部署 Azure AD 的组织也可以使用本指南来确保从投资中获得最大的收益。

精心规划和得到良好执行的标识基础结构为使生产力工作负荷与数据仅供已知用户和设备进行安全访问铺平了道路。

此外，客户可以检查其[标识安全评分](identity-secure-score.md)，以确定他们与 Microsoft 最佳做法的相符程度。 请在实施这些建议之前和之后检查安全评分，以确定你与同行业中的其他公司或者与同等规模的其他组织之间的优劣。

## <a name="prerequisites"></a>必备组件

可以使用 Azure AD Free、Basic 实施本指南中所述的许多建议，完全没有许可证也可以实施。 如果需要许可证，本指南会指出完成相应任务最起码需要哪种许可证。

可在以下页面上找到有关许可的更多信息：

* [Azure AD 许可](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 企业版](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [企业移动性 + 安全性](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Azure AD B2B 许可指南](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>阶段 1：构建安全基础

在此阶段，管理员启用基准安全功能，以便在 Azure AD 中创建更安全且易用的基础，然后我们导入或创建普通用户帐户。 此基础阶段确保你从一开始就处于一种更安全的状态，并且只需向最终用户介绍新的概念一次。

| 任务 | Detail | 所需的许可证 |
| ---- | ------ | ---------------- |
| [指定多个全局管理员](../users-groups-roles/directory-emergency-access.md) | 至少分配两个仅限云的永久性全局管理员帐户，以便在紧急情况下使用。 这些帐户不是每日使用，应该具有复杂的长密码。 | Azure AD Free |
| [尽可能使用非全局管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 只为管理员分配他们必须访问的区域的访问权限。 并非所有管理员都需要是全局管理员。 | Azure AD Free |
| [启用 Privileged Identity Management 以跟踪管理员角色的用途](../privileged-identity-management/pim-getting-started.md) | 启用 Privileged Identity Management 以开始跟踪管理角色的用途。 | Azure AD Premium P2 |
| [推广自助服务密码重置](../authentication/howto-sspr-deployment.md) | 让员工使用管理员控制的策略重置自己的密码，减少支持台收到的密码重置呼叫次数。 | Azure AD Basic |
| [创建组织特定自定义受禁的密码列表](../authentication/howto-password-ban-bad-configure.md) | 防止用户创建包含你所在组织或区域中常用单词或短语的密码。 | Azure AD Basic |
| [启用与 Azure AD 密码保护的本地集成](../authentication/concept-password-ban-bad-on-premises.md) | 将受禁密码列表扩展到本地目录，以确保本地设置的密码也符合全局和特定于租户的受禁密码列表。 | Azure AD Premium P1 |
| [启用 Microsoft 的密码指导](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求用户按照设置的计划更改其密码，禁用复杂性要求，用户更倾向于记住他们习惯的密码，并妥善保管其密码。 | Azure AD Free |
| [对基于云的用户帐户禁用定期密码重置](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | 定期密码重置会促使用户增加其现有密码。 使用 Microsoft 密码指导文档中的指导原则，并将相同的本地策略运用到仅限云的用户。 | Azure AD Free |
| [自定义 Azure Active Directory 智能锁定](../authentication/howto-password-smart-lockout.md) | 停止锁定从基于云的用户复制到本地 Active Directory 用户 | Azure AD Basic |
| [为 AD FS 启用 Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS Extranet 锁定可以防范暴力密码猜测攻击，同时可让有效的 AD FS 用户继续使用其帐户。 | |
| [部署使用条件性访问策略的 Azure AD 多重身份验证](../authentication/howto-mfa-getstarted.md) | 要求用户访问敏感应用程序使用条件性访问策略时执行双重验证。 | Azure AD Premium P1 |
| [Azure Active Directory Identity Protection](../identity-protection/enable.md) | 针对组织中的用户启用有风险登录和已泄密凭据的跟踪。 | Azure AD Premium P2 |
| [使用风险事件触发多重身份验证和密码更改](../authentication/tutorial-risk-based-sspr-mfa.md) | 启用可以触发多重身份验证、密码重置和基于风险阻止登录等事件的自动化功能。 | Azure AD Premium P2 |
| [启用自助密码重置和 Azure AD 多重身份验证的聚合注册（预览版）](../authentication/concept-registration-mfa-sspr-converged.md) | 允许用户从 Azure 多重身份验证和自助式密码重置的一个常用体验进行注册。 | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>阶段 2：导入用户、启用同步和管理设备

接下来，我们通过导入用户、启用同步、规划来宾访问权限并准备支持其他功能，来对阶段 1 中的基础做出补充。

| 任务 | Detail | 所需的许可证 |
| ---- | ------ | ---------------- |
| [安装 Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | 准备将现有本地目录中的用户同步到云。 | Azure AD Free |
| [实现密码哈希同步](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | 同步密码哈希，以便能够复制密码更改、检测并补救错误密码，以及报告已泄漏的凭据。 | Azure AD Free |
| [实现密码写回](../authentication/howto-sspr-writeback.md) | 允许将云中的密码更改写回到本地 Windows Server Active Directory 环境。 | Azure AD Premium P1 |
| [实现 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | 为 Azure AD Connect 服务器、AD FS 服务器和域控制器启用关键运行状况统计信息的监视。 | Azure AD Premium P1 |
| [按 Azure Active Directory 中的组成员资格将许可证分配给用户](../users-groups-roles/licensing-groups-assign.md) | 创建许可组来按组启用或禁用功能，而无需按用户进行设置，这样可以节省时间和精力。 | |
| [针对来宾用户访问权限创建计划](../b2b/what-is-b2b.md) | 让来宾用户使用其自己的工作、学校或社交标识登录到你的应用和服务，借此来与他们协作。 | [Azure AD B2B 许可指南](../b2b/licensing-guidance.md) |
| [决定设备管理策略](../devices/overview.md) | 决定组织允许在设备上执行哪些操作。 这包括在自带设备与公司提供的设备上执行注册与加入操作。 | |
| [在组织中部署 Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | 使用 Windows Hello 准备无密码身份验证 | |

## <a name="phase-3-manage-applications"></a>阶段 3：管理应用程序

在前几个阶段中持续构建环境的过程中，我们识别了适合迁移并与 Azure AD 集成的应用程序，并完成了这些应用程序的设置。

| 任务 | Detail | 所需的许可证 |
| ---- | ------ | ---------------- |
| 识别应用程序 | 识别组织中正在使用的应用程序：本地应用程序、云中的 SaaS 应用程序和其他业务线应用程序。 确定这些应用程序是否可以，以及是否应该由 Azure AD 进行管理。 | 无需许可证 |
| [集成库中的受支持 SaaS 应用程序](../manage-apps/add-application-portal.md) | Azure AD 的某个库包含数千个预先集成的应用程序。 组织使用的某些应用程序可能就在该库中。可通过 Azure 门户直接访问该库。 | Azure AD Free |
| [使用应用程序代理集成本地应用程序](../manage-apps/application-proxy-add-on-premises-application.md) | 应用程序代理可让用户在使用其 Azure AD 帐户登录后访问本地应用程序。 | Azure AD Basic |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>阶段 4：审核特权标识、完成访问评审和管理用户生命周期

在第 4 阶段，管理员针对管理工作强制实施最低特权原则，完成首次访问评审，并启用常见用户生命周期任务的自动化。

| 任务 | Detail | 所需的许可证 |
| ---- | ------ | ---------------- |
| [强制使用 Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | 删除普通日常用户帐户的管理角色。 使管理用户能够在成功完成多重身份验证检查、提供业务理由或请求指定的审批者批准之后使用其角色。 | Azure AD Premium P2 |
| [在 PIM 中完成 Azure AD 目录角色的访问评审](../privileged-identity-management/pim-how-to-start-security-review.md) | 与安全和领导团队协作创建访问评审策略，以根据组织的策略评审管理访问权限。 | Azure AD Premium P2 |
| [实施动态组成员资格策略](../users-groups-roles/groups-dynamic-membership.md) | 使用动态组根据来自人力资源（或真实来源）的属性（例如部门、职位、区域和其他属性）自动向组分配用户。 |  |
| [实施基于组的应用程序预配](../manage-apps/what-is-access-management.md) | 使用基于组的访问管理预配为 SaaS 应用程序自动预配用户。 |  |
| [自动用户预配和取消预配](../manage-apps/user-provisioning.md) | 从员工帐户生命周期中删除手动步骤，以防止未经授权的访问。 将真实来源（HR 系统）中的标识同步到 Azure AD。 |  |

## <a name="next-steps"></a>后续步骤

[Azure AD 许可和定价详细信息](https://azure.microsoft.com/pricing/details/active-directory/)

[标识和设备访问权限配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[常见的推荐标识和设备访问策略](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
