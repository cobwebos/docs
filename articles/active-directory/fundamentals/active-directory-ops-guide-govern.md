---
title: Azure 活动目录治理操作参考指南
description: 此操作参考指南描述为确保治理管理而应采取的检查和操作
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535452"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure 活动目录治理操作参考指南

Azure AD[操作参考指南](active-directory-ops-guide-intro.md)的这一部分介绍了为评估和证明授予的非特权和特权标识、审核和控制对环境的更改而应执行的检查和操作。

> [!NOTE]
> 这些建议自发布之日起是最新的，但可能会随时间而变化。 随着 Microsoft 产品和服务随时间推移而变化，组织应持续评估其治理实践。

## <a name="key-operational-processes"></a>关键操作流程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配给关键任务

管理 Azure 活动目录需要持续执行关键操作任务和进程，这些任务和进程可能不是推出项目的一部分。 设置这些任务以优化环境仍然很重要。 关键任务及其推荐的所有者包括：

| 任务 | “所有者” |
| :- | :- |
| 在 SIEM 系统中存档 Azure AD 审核日志 | InfoSec 运营团队 |
| 发现不符合合规性的应用程序 | IAM 运营团队 |
| 定期审查对应用程序的访问 | InfoSec 架构团队 |
| 定期审查对外部身份的访问 | InfoSec 架构团队 |
| 定期审查谁拥有特权角色 | InfoSec 架构团队 |
| 定义安全门以激活特权角色 | InfoSec 架构团队 |
| 定期审查同意授予 | InfoSec 架构团队 |
| 为组织中的员工设计应用程序和资源的目录和访问包 | 应用所有者 |
| 定义安全策略以将用户分配给访问包 | InfoSec 团队 + 应用程序所有者 |
| 如果策略包括审批工作流，则定期审核工作流审批 | 应用所有者 |
| 使用访问审核查看安全策略中的异常，如条件访问策略 | InfoSec 运营团队 |

在查看列表时，您可能会发现需要为缺少所有者的任务分配所有者，或者调整所有者与上述建议不一致的任务的所有权。

#### <a name="owner-recommended-reading"></a>业主推荐阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>配置更改测试

在测试时需要特殊考虑的更改，从简单的技术（如推出目标用户子集）到在并行测试租户中部署更改。 如果尚未实施测试策略，则应根据下表中的指南定义测试方法：

| 方案| 建议 |
|-|-|
|将身份验证类型从联合更改为小到小到条/PTA，反之亦然| 使用[暂存卷展栏](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout)测试更改身份验证类型的影响。|
|推出新的条件访问 （CA） 策略或身份保护策略|创建新的 CA 策略并分配给测试用户。|
|加入应用程序的测试环境|将应用程序添加到生产环境，将其从 MyApps 面板中隐藏，并将其分配给质量保证 （QA） 阶段中的测试用户。|
|更改同步规则|在测试中执行 Azure AD 使用当前正在生产的配置（也称为暂存模式）进行更改，并分析 CSExport 结果。 如果满足，则在准备就绪时交换到生产。|
|改变品牌|在单独的测试租户中进行测试。|
|推出新功能|如果该功能支持向目标用户集推出，请确定试点用户并进行构建。例如，自助服务密码重置和多重身份验证可以针对特定用户或组。|
|将应用程序从本地标识提供程序 （IdP，例如活动目录）到 Azure AD 进行剪切|如果应用程序支持多个 IdP 配置（例如 Salesforce），则在更改窗口期间配置和测试 Azure AD（如果应用程序引入了 HRD 页）。 如果应用程序不支持多个 IdP，请安排更改控制窗口期间的测试和程序停机时间。|
|更新动态组规则|使用新规则创建并行动态组。 与计算的结果进行比较，例如，运行具有相同条件的 PowerShell。<br>如果测试通过，则交换使用旧组的位置（如果可行）。|
|迁移产品许可证|请参阅[更改 Azure 活动目录中许可组中的单个用户的许可证](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)。|
|更改 AD FS 规则，如授权、颁发、MFA|使用组声明来定位用户子集。|
|更改 AD FS 身份验证体验或类似服务器场范围更改|创建具有相同主机名的并行服务器场、实现配置更改、使用 HOSTS 文件、NLB 路由规则或类似路由从客户端进行测试。<br>如果目标平台不支持 HOSTS 文件（例如移动设备），则控制更改。|

## <a name="access-reviews"></a>访问评审

### <a name="access-reviews-to-applications"></a>访问应用程序审核

随着时间的推移，用户可能会在不同团队和职位中移动时累积对资源的访问权限。 重要的是，资源所有者定期查看对应用程序的访问权限，并删除用户整个生命周期中不再需要的权限。 Azure AD[访问审核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)使组织能够高效地管理组成员身份、访问企业应用程序和角色分配。 资源所有者应定期查看用户的访问权限，以确保只有合适的人员才能继续访问。 理想情况下，应考虑为此任务使用 Azure AD 访问审核。

![访问评审启动页](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 与访问评审交互的每个用户都必须拥有付费 Azure AD Premium P2 许可证。

### <a name="access-reviews-to-external-identities"></a>访问外部标识的审核

在需要的时间内，保持对外部标识的访问仅限于所需的资源，这一点至关重要。 使用 Azure AD[访问审核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)为所有外部标识和应用程序访问建立常规的自动访问审核过程。 如果进程已在本地存在，请考虑使用 Azure AD 访问审核。 一旦应用程序停用或不再使用，请删除有权访问该应用程序的所有外部标识。

> [!NOTE]
> 与访问评审交互的每个用户都必须拥有付费 Azure AD Premium P2 许可证。

## <a name="privileged-account-management"></a>特权帐户管理

### <a name="privileged-account-usage"></a>特权帐户使用

黑客通常以管理员帐户和特权访问的其他元素为目标，以便快速访问敏感数据和系统。由于具有特权角色的用户往往会随着时间的推移而累积，因此定期查看和管理管理员访问权限并提供对 Azure AD 和 Azure 资源的实时特权访问非常重要。

如果您的组织中不存在管理特权帐户的进程，或者您当前有使用其常规用户帐户管理服务和资源的管理员，则应立即开始使用单独的帐户，例如，用于常规日常帐户活动;另一个用于特权访问，并配置 MFA。 更妙的是，如果您的组织具有 Azure AD 高级 P2 订阅，则应立即部署[Azure AD 特权标识管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements)（PIM）。 出于同一令牌，您还应查看这些特权帐户，并[分配特权较低的角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)（如果适用）。

应实现的特权帐户管理的另一个方面是手动或通过[PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)定义这些帐户[的访问审核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。

#### <a name="privileged-account-management-recommended-reading"></a>特权帐户管理推荐阅读

- [Azure AD Privileged Identity Management 中的角色](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>紧急访问帐户

组织必须创建[紧急帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)，以便为身份验证中断等情况管理 Azure AD：

- 身份验证基础结构的中断组件（AD FS、本地 AD、MFA 服务）
- 行政人员更替

为了防止由于无法以管理员身份登录或激活现有个人用户帐户而无意中被锁定在租户之外，应创建两个或多个紧急帐户，并确保这些帐户已实现并与[Microsoft 的最佳做法](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure)保持一致，并[打破玻璃程序](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)。

### <a name="privileged-access-to-azure-ea-portal"></a>对 Azure EA 门户的特权访问

[Azure 企业协议 （Azure EA） 门户](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)使您能够根据主企业协议创建 Azure 订阅，这是企业内部的强大角色。 在将 Azure AD 放在原位之前，通常先引导创建此门户，因此有必要使用 Azure AD 标识将其锁定、从门户中删除个人帐户、确保已正确委派到位并降低锁定风险.

要明确，如果 EA 门户授权级别当前设置为"混合模式"，则必须从 EA 门户中的所有特权访问中删除任何[Microsoft 帐户](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)，并将 EA 门户配置为仅使用 Azure AD 帐户。 如果未配置 EA 门户委派的角色，则还应查找和实现部门和帐户的委派角色。

#### <a name="privileged-access-recommended-reading"></a>推荐读取特权访问

- [Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>权利管理

[授权管理 （EM）](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)允许应用所有者捆绑资源并将其分配给组织中的特定角色（内部和外部）。 EM 允许自助服务注册和委派给业务所有者，同时保持治理策略以授予访问权限、设置访问持续时间和允许审批工作流。 

> [!NOTE]
> Azure AD 授权管理需要 Azure AD 高级 P2 许可证。

## <a name="summary"></a>总结

安全标识治理有八个方面。 此列表将帮助您确定应执行的操作，以评估和证明授予非特权和特权标识、审核和控制对环境更改的访问权限。

- 将所有者分配给关键任务。
- 实施测试策略。
- 使用 Azure AD 访问审核可高效地管理组成员身份、对企业应用程序的访问和角色分配。
- 为所有类型的外部身份和应用程序访问建立定期、自动化的访问审核流程。
- 建立访问审核流程，定期查看和管理管理员访问权限，并提供对 Azure AD 和 Azure 资源的及时特权访问。
- 预配紧急帐户，以便为意外中断管理 Azure AD 做好准备。
- 锁定对 Azure EA 门户的访问。
- 实现授权管理，以提供对资源集合的受治理访问。

## <a name="next-steps"></a>后续步骤

开始使用[Azure AD 操作检查和操作](active-directory-ops-guide-ops.md)。
