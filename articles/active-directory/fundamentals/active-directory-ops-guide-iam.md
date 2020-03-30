---
title: Azure 活动目录标识和访问管理操作参考指南
description: 此操作参考指南描述为确保标识和访问管理操作而应采取的检查和操作
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298608"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure 活动目录标识和访问管理操作参考指南

Azure AD[操作参考指南](active-directory-ops-guide-intro.md)的这一部分介绍了保护和管理标识及其分配的生命周期时应考虑的检查和操作。

> [!NOTE]
> 这些建议自发布之日起是最新的，但可能会随时间而变化。 随着 Microsoft 产品和服务随时间推移而变化，组织应持续评估其身份实践。

## <a name="key-operational-processes"></a>关键操作流程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配给关键任务

管理 Azure 活动目录需要持续执行可能不是推出项目一部分的关键操作任务和进程。 设置这些任务以维护环境仍然很重要。 关键任务及其推荐的所有者包括：

| 任务 | “所有者” |
| :- | :- |
| 定义如何创建 Azure 订阅的过程 | 因组织而异 |
| 决定谁获得企业移动性和安全许可证 | IAM 运营团队 |
| 决定谁获得 Office 365 许可证 | 生产力团队 |
| 决定谁获得其他许可证，例如，动态，VSO | 应用程序所有者 |
| 分配许可证 | IAM 运营团队 |
| 排除和修复许可证分配错误 | IAM 运营团队 |
| 预配 Azure AD 中的应用程序标识 | IAM 运营团队 |

在查看列表时，您可能会发现需要为缺少所有者的任务分配所有者，或者调整所有者与上述建议不一致的任务的所有权。

#### <a name="assigning-owners-recommended-reading"></a>分配所有者建议阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>本地标识同步

### <a name="identify-and-resolve-synchronization-issues"></a>识别和解决同步问题

Microsoft 建议您对本地环境中可能导致云同步问题的问题有良好的基线和理解。 由于[IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)和 Azure [AD 连接运行状况](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health)等自动化工具可以生成大量误报，因此我们建议您通过清除错误对象来识别 100 天内未解决的同步错误。 长期未解决的同步错误可能会生成支持事件。 [同步期间的故障排除错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors)概述了不同类型的同步错误、导致这些错误的一些可能方案以及修复错误的潜在方法。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD 连接同步配置

要启用所有混合体验、基于设备的安全状态以及与 Azure AD 集成，需要同步员工用来登录到其桌面的用户帐户。

如果不同步林用户登录，则应将同步更改为来自正确的林。

#### <a name="synchronization-scope-and-object-filtering"></a>同步范围和对象筛选

删除不需要同步的已知对象存储桶具有以下操作优势：

- 同步错误源减少
- 更快的同步周期
- 减少从本地转发的"垃圾"，例如，云中不相关的本地服务帐户的全局地址列表污染

> [!NOTE]
> 如果您发现正在导入许多未导出到云的对象，则应按 OU 或特定属性进行筛选。

要排除的对象的示例包括：

- 不用于云应用程序的服务帐户
- 不应用于云方案（如用于授予资源访问权限）的组
- 旨在表示 Azure AD B2B 协作的外部标识的用户或联系人
- 员工不应从服务器（例如服务器）访问云应用程序的计算机帐户

> [!NOTE]
> 如果单个人的身份具有从旧域迁移、合并或获取等内容预配的多个帐户，则仅应同步用户日常使用的帐户，例如，他们用于登录到其计算机的内容.

理想情况下，您希望在减少要同步的对象数和规则中的复杂性之间取得平衡。 通常，OU/容器[筛选](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)与与云筛选属性的简单属性映射的组合是一种有效的筛选组合。

> [!IMPORTANT]
> 如果在生产中使用组筛选，则应过渡到其他筛选方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步故障转移或灾难恢复

Azure AD 连接在预配过程中扮演着关键角色。 如果同步服务器由于任何原因脱机，则无法在云中更新对本地的更改，并可能导致用户访问问题。 因此，请务必定义故障转移策略，允许管理员在同步服务器脱机后快速恢复同步。 这种战略可分为以下几类：

- **在暂存模式下部署 Azure AD 连接服务器**- 允许管理员通过简单的配置开关将暂存服务器"提升"到生产。
- **使用虚拟化**- 如果 Azure AD 连接部署在虚拟机 （VM） 中，则管理员可以利用其虚拟化堆栈进行实时迁移或快速重新部署 VM，从而恢复同步。

如果您的组织缺少 Sync 的灾难恢复和故障转移策略，则应毫不犹豫地在暂存模式下部署 Azure AD 连接。 同样，如果生产和暂存配置不匹配，则应重新基线 Azure AD Connect 暂存模式以匹配生产配置，包括软件版本和配置。

![Azure AD 连接暂存模式配置的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>了解最新功能

微软定期更新 Azure AD 连接。 保持最新状态，利用每个新版本提供的性能改进、错误修复和新功能。

如果 Azure AD Connect 版本落后六个月以上，则应升级到最新版本。

#### <a name="source-anchor"></a>源锚点

使用**ms-DS 一致性作为**[源锚点](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)可以更轻松地跨林和域迁移对象，这在 AD 域整合/清理、合并、收购和剥离中很常见。

如果您当前使用**ObjectGuid**作为源锚点，我们建议您切换到使用**ms-DS-一致性 Guid**。

#### <a name="custom-rules"></a>自定义规则

Azure AD Connect 自定义规则提供了控制本地对象和云对象之间属性流的能力。 但是，过度使用或误用自定义规则可能会带来以下风险：

- 故障排除复杂性
- 跨对象执行复杂操作时性能下降
- 生产服务器和暂存服务器之间配置差异的可能性更高
- 如果在优先级大于 100 的范围内创建自定义规则（由内置规则使用）则升级 Azure AD Connect 时的额外开销

如果使用过于复杂的规则，则应调查复杂性的原因，并找到简化的机会。 同样，如果您创建了优先级值超过 100 的自定义规则，则应修复这些规则，以便这些规则不会面临风险或与默认集冲突。

滥用自定义规则的示例包括：

- **补偿目录中的脏数据**- 在这种情况下，建议与 AD 团队的所有者合作，将目录中的数据清理为修正任务，并调整进程以避免重新引入错误数据。
- **单个用户的一次性修正**- 通常查找异常情况的规则，通常是由于特定用户的问题。
- **"云筛选"过于复杂**- 虽然减少对象数量是一种好的做法，但使用许多同步规则创建和过度复杂的同步范围存在风险。 如果存在复杂的逻辑来包括/排除 OU 筛选之外的对象，建议在同步之外处理此逻辑，并将对象标记为具有简单"云筛选"属性，该属性可以使用简单的同步规则流动。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD 连接配置文档

[Azure AD 连接配置文档是](https://github.com/Microsoft/AADConnectConfigDocumenter)一种工具，可用于生成 Azure AD Connect 安装的文档，以便更好地了解同步配置、建立正确操作的信心，并了解在应用 Azure AD Connect 的新生成或配置或添加或更新自定义同步规则时发生了哪些更改。 该工具的当前功能包括：

- Azure AD 连接同步的完整配置的文档。
- 记录两个 Azure AD Connect 配置中的任何更改同步服务器或从给定配置基线更改。
- 生成 PowerShell 部署脚本，以将同步规则差异或自定义项从一台服务器迁移到另一台服务器。

## <a name="assignment-to-apps-and-resources"></a>分配给应用和资源

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>基于组的许可，适用于 Microsoft 云服务

Azure 活动目录通过 Microsoft 云服务[基于组的许可](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)简化许可证管理。 这样，IAM 会将这些组基础结构和委派管理委托给组织中的适当团队。 在 Azure AD 中设置组的成员身份有多种，包括：

- **从本地同步**- 组可能来自本地目录，这非常适合已建立的组管理流程的组织，这些流程可以扩展以在 Office 365 中分配许可证。

- **基于属性/动态**- 可以基于基于用户属性的表达式在云中创建组，例如，部门等于"销售"。 Azure AD 维护组的成员，使其与定义的表达式保持一致。 将此类组用于许可证分配可启用基于属性的许可证分配，这非常适合其目录中具有高数据质量的组织。

- **委派的所有权**- 可以在云中创建组，也可以指定所有者。 这样，您可以授权业务所有者（例如协作团队或 BI 团队）定义谁应该有权访问。

如果您当前使用手动流程向用户分配许可证和组件，我们建议您实施基于组的许可。 如果当前流程不监视许可错误或"分配"与"可用"，则应定义流程的改进，以解决许可错误并监视许可分配。

许可证管理的另一个方面是服务计划（许可证组件）的定义，应基于组织中的作业功能启用。 授予对不需要的服务计划的权限，可能会导致用户在 Office 门户中看到尚未培训或不应使用的工具。 它可以推动额外的帮助台容量、不必要的预配，并危及合规性和治理，例如，在为可能不允许共享内容的个人预配 OneDrive 时。

使用以下准则向用户定义服务计划：

- 管理员应根据用户的角色（例如白领员工与地工）定义要提供给用户的"捆绑"服务计划。
- 按群集创建组，并通过服务计划分配许可证。
- 或者，可以定义属性以为用户保留包。

> [!IMPORTANT]
> Azure AD 中基于组的许可引入了处于许可错误状态的用户的概念。 如果您发现任何许可错误，则应立即[识别和解决](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems)任何许可证分配问题。

![自动生成的计算机屏幕的屏幕截图 描述](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命周期管理

如果您当前使用依赖本地基础结构的工具（如[Microsoft 标识管理器](https://docs.microsoft.com/microsoft-identity-manager/)或第三方系统），我们建议您从现有工具卸载分配，实现基于组的许可，并根据[组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)定义组生命周期管理。 同样，如果您的现有流程不考虑离开组织的新员工或员工，则应基于动态组部署基于组的许可，并定义组成员生命周期。 最后，如果针对缺少生命周期管理的本地组部署了基于组的许可，请考虑使用云组启用委派所有权或基于属性的动态成员身份等功能。

### <a name="assignment-of-apps-with-all-users-group"></a>具有"所有用户"组的应用分配

资源所有者可能认为，**所有用户**组仅包含**企业员工**，而实际上可能同时包含**企业员工**和**来宾**。 因此，在使用 **"所有用户**"组进行应用程序分配和授予对资源（如 SharePoint 内容或应用程序）的访问权限时，应特别注意。

> [!IMPORTANT]
> 如果启用了 **"所有用户**"组并用于条件访问策略、应用或资源分配，请确保在不希望[该组](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups)包含来宾用户时保护该组。 此外，还应通过创建和分配给仅包含**企业员工的**组来修复许可分配。 另一方面，如果您发现 **"所有用户**"组已启用，但未用于授予对资源的访问权限，请确保组织的运营指导是有意使用该组（包括**企业员工**和**来宾**）。

### <a name="automated-user-provisioning-to-apps"></a>自动用户预配到应用

[自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)到应用程序是跨多个系统创建一致配置、取消预配和生命周期的最佳方式。

如果您当前正在以临时方式预配应用，或者使用 CSV 文件、JIT 或不涉及生命周期管理的本地解决方案等内容，我们建议您为受支持的应用程序实现 Azure AD[的应用程序预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application)，并为 Azure AD 尚不支持的应用程序定义一致的模式。

![Azure AD 预配服务](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD 连接增量同步周期基线

了解组织中更改的数量并确保不会花太长的时间进行可预测的同步时间，这一点非常重要。

[默认增量同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)频率为 30 分钟。 如果增量同步持续时间超过 30 分钟，或者暂存和生产中的增量同步性能之间存在显著差异，则应调查和查看[影响 Azure AD 连接性能的因素](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD 连接故障排除建议读取

- [使用 IdFix 工具 （ Office 365）准备目录属性，以便与 Office 365 同步](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD 连接：在同步期间排除错误](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>总结

安全标识基础结构有五个方面。 此列表将帮助您快速查找并在必要时操作，以确保和管理组织中标识及其权利的生命周期。

- 将所有者分配给关键任务。
- 查找并解决同步问题。
- 定义灾难恢复的故障转移策略。
- 简化许可证管理和应用程序分配。
- 自动向应用预配用户。

## <a name="next-steps"></a>后续步骤

开始[使用身份验证管理检查和操作](active-directory-ops-guide-auth.md)。
