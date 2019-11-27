---
title: Azure Active Directory 调控操作参考指南
description: 此操作参考指南介绍了安全监管管理应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535452"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 调控操作参考指南

本部分的[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)介绍了对环境进行的非特权和特权标识、审核和控制更改时所需的检查和操作。

> [!NOTE]
> 这些建议是发布日期之后的最新建议，但会随时间变化。 组织应在 Microsoft 产品和服务随着时间推移而不断地进行评估。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和进程，这可能不是一个部署项目的一部分。 设置这些任务以优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 存档 Azure AD SIEM 系统中的审核日志 | InfoSec 运营团队 |
| 发现管理不合规的应用程序 | IAM 操作团队 |
| 定期查看对应用程序的访问权限 | InfoSec 体系结构团队 |
| 定期查看对外部标识的访问权限 | InfoSec 体系结构团队 |
| 定期查看具有特权角色的人员 | InfoSec 体系结构团队 |
| 定义安全入口以激活特权角色 | InfoSec 体系结构团队 |
| 定期查看同意授权 | InfoSec 体系结构团队 |
| 为组织中的员工设计用于应用程序和资源的目录和访问包 | 应用所有者 |
| 定义安全策略以分配用户访问包 | InfoSec 团队 + 应用所有者 |
| 如果策略包含审批工作流，则会定期检查工作流审核 | 应用所有者 |
| 查看安全策略中的异常，如使用访问评审的条件性访问策略 | InfoSec 运营团队 |

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="owner-recommended-reading"></a>所有者建议阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>配置更改测试

在进行测试时需要特别注意的一些变化，从简单的技术（例如，向外扩展用户的目标子集到在并行测试租户中部署更改）。 如果尚未实现测试策略，则应根据下表中的准则定义测试方法：

| 场景| 建议 |
|-|-|
|将身份验证类型从联合更改为 PHS/PTA，反之亦然| 使用[分阶段推出](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout)来测试更改身份验证类型的影响。|
|推出新的条件访问（CA）策略或 Identity Protection 策略|创建新的 CA 策略并将其分配给测试用户。|
|载入应用程序的测试环境|将应用程序添加到生产环境，将其从 "MyApps" 面板中隐藏，并在质量保证（QA）阶段将其分配给测试用户。|
|更改同步规则|使用当前在生产环境中的相同配置（也称为暂存模式）执行测试 Azure AD Connect 中的更改，并分析 CSExport 结果。 如果满意，请在准备就绪时切换到生产环境。|
|更改品牌|在单独的测试租户中测试。|
|推出新功能|如果该功能支持向目标用户组推出，请确定试验用户并构建。例如，自助服务密码重置和多重身份验证可以针对特定的用户或组。|
|将应用程序从本地标识提供程序（IdP）转换为（）（例如 Active Directory）以 Azure AD|如果应用程序支持多个 IdP 配置（例如 Salesforce），请在更改窗口（如果应用程序引入 HRD 页）期间配置和测试 Azure AD。 如果应用程序不支持多个 Idp，请在更改控制窗口和计划停机期间计划测试。|
|更新动态组规则|使用新规则创建并行动态组。 与计算结果进行比较，例如，运行具有相同条件的 PowerShell。<br>如果测试通过，则交换使用旧组的位置（如果可行）。|
|迁移产品许可证|请参阅[在 Azure Active Directory 中更改许可组中单个用户的许可证](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)。|
|更改授权、颁发、MFA 等 AD FS 规则|使用组声明来面向部分用户。|
|更改 AD FS 身份验证体验或类似服务器场范围的更改|使用主机文件、NLB 路由规则或类似路由创建具有相同主机名、实现配置更改、从客户端测试的并行场。<br>如果目标平台不支持主机文件（例如移动设备），则控制更改。|

## <a name="access-reviews"></a>访问评审

### <a name="access-reviews-to-applications"></a>对应用程序的访问评审

随着时间的推移，当用户在不同的团队和位置移动时，用户可能会积累对资源的访问权限。 资源所有者需要定期审查对应用程序的访问权限，并删除在用户生命周期内不再需要的特权，这一点非常重要。 Azure AD[访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)使组织能够有效地管理组成员身份、访问企业应用程序和角色分配。 资源所有者应定期查看用户的访问权限，以确保只有正确的人继续访问。 理想情况下，应考虑对此任务使用 Azure AD 访问评审。

![访问评审起始页](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 与访问评审交互的每个用户都必须具有付费 Azure AD Premium P2 许可证。

### <a name="access-reviews-to-external-identities"></a>外部标识的访问评审

在需要时，只需将对外部标识的访问权限限制为所需的资源，这一点至关重要。 使用 Azure AD[访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，为所有外部标识和应用程序访问建立定期的自动访问评审流程。 如果某个进程已在本地存在，请考虑使用 Azure AD 访问评审。 在应用程序停用或不再使用后，请删除有权访问应用程序的所有外部标识。

> [!NOTE]
> 与访问评审交互的每个用户都必须具有付费 Azure AD Premium P2 许可证。

## <a name="privileged-account-management"></a>特权帐户管理

### <a name="privileged-account-usage"></a>特权帐户使用情况

黑客通常以管理员帐户和其他特权访问元素为目标，以快速获取对敏感数据和系统的访问权限。 由于具有特权角色的用户可能会在一段时间内累积，因此必须定期查看和管理管理员访问权限，并提供对 Azure AD 和 Azure 资源的实时特权访问。

如果你的组织中不存在管理特权帐户的进程，或者你当前拥有使用其常规用户帐户管理服务和资源的管理员，则你应该立即开始使用单独的帐户，例如，一个帐户用于日常日常activity另一种用于使用 MFA 进行特权访问和配置。 更好的是，如果你的组织有 Azure AD Premium P2 订阅，则应立即部署[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) （PIM）。 在同一标记中，还应查看这些特权帐户，并[分配更少的特权角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)（如果适用）。

应实现的特权帐户管理的另一个方面是，[通过 PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)手动或自动定义对这些帐户的[访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。

#### <a name="privileged-account-management-recommended-reading"></a>特权帐户管理建议阅读

- [Azure AD Privileged Identity Management 中的角色](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>紧急访问帐户

组织必须创建[紧急帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)，以便在诸如以下之类的身份验证中断的情况下管理 Azure AD：

- 身份验证基础结构（AD FS、本地 AD、MFA 服务）的中断组件
- 管理人员交易额

若要防止意外锁定租户，因为你不能以管理员身份登录或激活现有的个人用户帐户，则应创建两个或更多个紧急帐户，并确保它们已实现并且与[Microsoft 的最佳实践](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)和[中断玻璃过程](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)一致。

### <a name="privileged-access-to-azure-ea-portal"></a>对 Azure EA 门户的特权访问

利用[azure 企业协议（AZURE EA）门户](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)，你可以针对主企业协议创建 Azure 订阅，这是企业内功能强大的角色。 在准备好 Azure AD 之前，通常会启动创建此门户，因此，有必要使用 Azure AD 标识将其锁定、从门户中删除个人帐户、确保适当的委派并降低锁定风险.

若要清楚，如果 EA 门户授权级别当前设置为 "混合模式"，则必须从 EA 门户中的所有特权访问中删除任何[Microsoft 帐户](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)，并将 ea 门户配置为仅使用 Azure AD 帐户。 如果未配置 EA 门户委托角色，还应查找并实现部门和帐户的委派角色。

#### <a name="privileged-access-recommended-reading"></a>授权访问建议阅读

- [Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>权利管理

授权[管理（EM）](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)允许应用所有者捆绑资源，并将其分配给组织中的特定角色（内部和外部）。 EM 允许自助服务注册并委派给企业所有者，同时保留调控策略以授予访问权限、设置访问持续时间，并允许批准工作流。 

> [!NOTE]
> Azure AD 的权利管理要求 Azure AD Premium P2 许可证。

## <a name="summary"></a>总结

安全标识治理有8个方面。 此列表将帮助你确定应执行的操作，以便评估并证明授予对环境的非特权和特权标识、审核和控制更改的访问权限。

- 将所有者分配给关键任务。
- 实现测试策略。
- 使用 Azure AD 访问评审来有效地管理组成员身份、访问企业应用程序和角色分配。
- 为所有类型的外部标识和应用程序访问建立常规的自动访问评审流程。
- 建立访问评审流程，以便定期查看和管理管理员访问权限，并提供对 Azure AD 和 Azure 资源的实时特权访问。
- 预配紧急帐户，准备管理 Azure AD 意外中断。
- 锁定对 Azure EA 门户的访问。
- 实施授权管理，以提供对资源集合的管理访问权限。

## <a name="next-steps"></a>后续步骤

开始处理[Azure AD 操作检查和操作](active-directory-ops-guide-ops.md)。
