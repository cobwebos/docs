---
title: Azure AD 部署清单（30 日、90 日及更长时间）
description: Azure Active Directory Premium P2 功能部署清单
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: ''
ms.openlocfilehash: 7931cd8a6f8b3de826e8dd563a837f80fc15d88a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310406"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 许可功能清单

为组织部署 Azure Active Directory (Azure AD) 并确保其安全，这似乎令人望而生畏。 本文确定了一些常见的任务，客户认为在 30 天、90 天或更长时限内完成这些任务有助于提高安全性。 即使已部署 Azure AD 的组织也可以使用此清单来确保从投资中获得最大的收益。

精心规划和得到良好执行的身份基础结构为仅经过身份验证的用户和设备更安全地访问生产力工作负载和数据铺平了道路。

## <a name="prerequisites"></a>先决条件

本指南假定你拥有 Azure AD Premium P2 许可证、企业移动 + 安全性 E5、Microsoft 365 E5 或等效的许可证捆绑包。

[Azure AD 许可](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 企业版](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise.aspx)

[企业移动性 + 安全性](https://www.microsoft.com/licensing/product-licensing/enterprise-mobility-security.aspx)

## <a name="plan-and-deploy-day-1-30"></a>计划和部署：第 1 到 30 日

- 指定多个全局管理员（break-glass 帐户）
   - [在 Azure AD 中管理紧急访问管理账户](../users-groups-roles/directory-emergency-access.md)
- 打开 Azure AD Privileged Identity Management (PIM) 查看报表
   - [开始使用 PIM](../privileged-identity-management/pim-getting-started.md)
- 尽可能使用非全局管理角色。
   - [在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)
- 身份验证
   - [推广自助服务密码重置](../authentication/howto-sspr-deployment.md)
   - 部署 Azure AD 密码保护（预览版）
      - [消除你的组织中的错误密码](../authentication/concept-password-ban-bad.md)
      - [为 Windows Server Active Directory 强制执行 Azure AD 密码保护](../authentication/concept-password-ban-bad-on-premises.md)
   - 配置帐户锁定策略
      - [Azure Active Directory 智能锁定](../authentication/howto-password-smart-lockout.md)
      - [AD FS Extranet 锁定和 Extranet 智能锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [使用条件访问策略部署 Azure AD 多重身份验证 ](../authentication/howto-mfa-getstarted.md)
   - [启用自助密码重置和 Azure AD 多重身份验证的聚合注册（预览版）](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [使用风险事件触发多重身份验证和密码更改](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [密码指南](https://www.microsoft.com/research/publication/password-guidance/)
      - 保持八个字符的最短长度要求，更长并不一定更好。
      - 取消字符组合要求。
      - [取消用户帐户的强制定期密码重置。](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- 从本地 Active Directory 同步用户
   - [安装 Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [实现密码哈希同步](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [实现密码写回](../authentication/howto-sspr-writeback.md)
   - [实现 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [按 Azure Active Directory 中的组成员资格将许可证分配给用户](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>计划和部署：第 31 至 90 日

- [计划来宾用户访问权限](../b2b/what-is-b2b.md)
   - [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../b2b/add-users-administrator.md)
   - [允许或阻止向特定组织中的 B2B 用户发送邀请](../b2b/allow-deny-list.md)
   - [向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](../b2b/hybrid-cloud-to-on-premises.md)
- 制定有关用户生命周期管理策略的决策
- [决定设备管理策略](../devices/overview.md)
   - [Azure AD Join 的使用方案和部署注意事项](../devices/azureadjoin-plan.md)
- [管理组织中的 Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>计划和部署：第 90 日及以后

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [在 PIM 中配置 Azure AD 目录角色设置](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [在 PIM 中分配 Azure AD 目录角色](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [在 PIM 中完成 Azure AD 目录角色的访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)
- 全面管理用户生命周期
   - Azure AD 具有管理标识生命周期的方法
   - 从员工帐户生命周期中删除手动步骤，以防止未经授权的访问：
      - 将真实来源（HR 系统）中的标识同步到 Azure AD。 （它链接到支持的 HR 系统）
      - [使用动态组根据来自 HR（或真实来源）的属性（例如部门、职位、区域和其他属性）自动向组分配用户。](../users-groups-roles/groups-dynamic-membership.md)
      - [使用预配为自动预配 SaaS 应用程序的用户的基于组的访问管理。](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>后续步骤

[标识和设备访问权限配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[常见的推荐标识和设备访问策略](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
