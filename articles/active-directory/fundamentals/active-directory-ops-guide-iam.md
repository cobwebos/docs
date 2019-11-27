---
title: Azure Active Directory 标识和访问管理操作参考指南
description: 此操作参考指南介绍了为保护标识和访问管理操作而应采取的检查和操作
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
ms.openlocfilehash: 36b3857f8827f8a33e5fc0981b22a49128f7c193
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535322"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Azure Active Directory 标识和访问管理操作参考指南

本部分的[Azure AD 操作参考指南](active-directory-ops-guide-intro.md)介绍了用于保护和管理标识及其分配的生命周期的检查和操作。

> [!NOTE]
> 这些建议是发布日期之后的最新建议，但会随时间变化。 组织应持续评估其身份实践，因为 Microsoft 产品和服务随时间推移而发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要连续执行关键操作任务和可能不属于推出项目的过程。 将这些任务设置为维护您的环境仍很重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 定义创建 Azure 订阅的过程 | 因组织而异 |
| 决定谁获得企业移动性 + 安全性许可证 | IAM 操作团队 |
| 决定谁可以获得 Office 365 许可证 | 生产力团队 |
| 决定谁可以获得其他许可证，例如 Dynamics、VSO | 应用程序所有者 |
| 分配许可证 | IAM 操作团队 |
| 排查和修正许可证分配错误 | IAM 操作团队 |
| 在 Azure AD 中将标识预配到应用程序 | IAM 操作团队 |

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或使用与上述建议不符的所有者来调整任务的所有权。

#### <a name="assigning-owners-recommended-reading"></a>分配所有者建议阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>本地标识同步

### <a name="identify-and-resolve-synchronization-issues"></a>确定并解决同步问题

Microsoft 建议你对本地环境中的问题有很好的基线和了解，这可能会导致云出现同步问题。 由于自动工具（例如[IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)和[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) ）可能会生成大量的假正值，因此，我们建议你通过清除错误中的这些对象来识别置之不理超过100天的同步错误。 长期未解决的同步错误可以生成支持事件。 [在同步过程中解决错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors)提供了不同类型的同步错误、导致这些错误的某些可能情况以及修复错误的可能方法。

### <a name="azure-ad-connect-sync-configuration"></a>Azure AD Connect 同步配置

若要启用所有混合体验、基于设备的安全状况以及与 Azure AD 的集成，需要同步员工用于登录到其桌面的用户帐户。

如果不同步林中用户的登录，则应将同步更改为来自正确的林。

#### <a name="synchronization-scope-and-object-filtering"></a>同步范围和对象筛选

删除不需要同步的已知对象的存储桶具有以下操作优势：

- 减少了同步错误源
- 更快的同步周期
- 更少从本地结转，例如，本地服务帐户的全局地址列表污染，此列表与云中无关

> [!NOTE]
> 如果你发现导入的多个对象未导出到云中，则应按 OU 或特定属性进行筛选。

要排除的对象的示例包括：

- 不用于云应用程序的服务帐户
- 不打算用于云方案的组，例如用于授予对资源的访问权限的组
- 要用 Azure AD B2B 协作表示的外部标识的用户或联系人
- 员工不打算从其访问云应用程序的计算机帐户，如服务器

> [!NOTE]
> 如果单个人体标识具有从诸如旧域迁移、合并或收购等内容预配的多个帐户，则只需同步用户在日常工作中使用的帐户，例如，他们用于登录到计算机的内容.

理想情况下，您会希望在减少要同步的对象数和规则中的复杂性之间达到平衡。 通常，OU/容器[筛选](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)与 cloudFiltered 属性的简单属性映射之间的组合是一种有效的筛选组合。

> [!IMPORTANT]
> 如果在生产环境中使用组筛选，则应转换为另一种筛选方法。

#### <a name="sync-failover-or-disaster-recovery"></a>同步故障转移或灾难恢复

Azure AD Connect 在预配过程中扮演着关键角色。 如果同步服务器出于任何原因进入脱机状态，则不能在云中更新本地更改，并可能会导致用户访问问题。 因此，请务必定义一个故障转移策略，使管理员能够在同步服务器脱机后快速恢复同步。 此类策略可能分为以下几类：

- **在暂存模式下部署 Azure AD Connect 服务器**-允许管理员通过简单的配置交换机将过渡服务器 "提升" 为生产环境。
- **使用虚拟化**-如果 Azure AD 连接部署在虚拟机（VM）中，则管理员可以利用其虚拟化堆栈实时迁移或快速重新部署 VM，进而恢复同步。

如果你的组织缺少用于同步的灾难恢复和故障转移策略，则不应担心在暂存模式下部署 Azure AD Connect。 同样，如果你的生产和暂存配置不匹配，则应重新设置基准 Azure AD Connect 过渡模式，以匹配生产配置，包括软件版本和配置。

![Azure AD Connect 过渡模式配置的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>了解最新功能

Microsoft 更新会定期 Azure AD Connect。 保持最新，以便充分利用性能改进、bug 修复和每个新版本提供的新功能。

如果你的 Azure AD Connect 版本超过六个月，你应升级到最新版本。

#### <a name="source-anchor"></a>源锚点

使用**msds-consistencyguid**作为[源锚点](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)，可以更轻松地跨林和域迁移对象，这在 AD 域合并/清理、合并、收购和 divestitures 中很常见。

如果当前正在使用**ObjectGuid**作为源定位点，建议使用**msds-consistencyguid**。

#### <a name="custom-rules"></a>自定义规则

Azure AD Connect 自定义规则提供了控制本地对象和云对象之间的属性流的能力。 但是，过度使用或滥用自定义规则可能会带来以下风险：

- 疑难解答复杂性
- 跨对象执行复杂操作时性能下降
- 生产服务器和过渡服务器之间的配置的不同偏差率
- 升级时的额外开销 Azure AD Connect 如果在优先级超过100的时间创建自定义规则（由内置规则使用）

如果你使用的是过于复杂的规则，则应该调查复杂性的原因并找到简化的机会。 同样，如果你创建了优先级值超过100的自定义规则，则应该修复这些规则，以使它们不存在风险或与默认集冲突。

滥用自定义规则的示例包括：

- **补偿目录中的脏数据**-在这种情况下，建议使用 AD 团队的所有者，并将目录中的数据作为修正任务进行清理，并调整进程以避免重新引入错误数据。
- **单个用户的一次性修正**-常见情况是查找特殊案例离群值的规则，通常是由于特定用户的问题。
- **Overcomplicated "CloudFiltering"** -减少对象数量是一种很好的做法，使用许多同步规则创建和 Overcomplicated 同步作用域的风险。 如果存在用于在 OU 筛选以外包含/排除对象的复杂逻辑，则建议在同步之外处理此逻辑，并使用简单的 "cloudFiltered" 属性来标记对象，此属性可以与简单同步规则一起流动。

#### <a name="azure-ad-connect-configuration-documenter"></a>Azure AD Connect 配置文档

[Azure AD Connect 配置文件管理](https://github.com/Microsoft/AADConnectConfigDocumenter)器是一种工具，可用于生成 Azure AD Connect 安装的文档，以便更好地了解同步配置，使其更好地理解，并了解在应用新的生成或配置时所发生 Azure AD Connect 的更改，或者添加或更新的自定义同步规则。 此工具的当前功能包括：

- Azure AD Connect 同步的完整配置文档。
- 记录了两个 Azure AD Connect 同步服务器的配置中的任何更改，或者给定的配置基线发生了更改。
- 生成 PowerShell 部署脚本以将同步规则差异或自定义从一台服务器迁移到另一台服务器。

## <a name="assignment-to-apps-and-resources"></a>分配到应用和资源

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>适用于 Microsoft 云服务的基于组的许可

Azure Active Directory 通过[基于组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)的 Microsoft 云服务许可简化对许可证的管理。 通过这种方式，IAM 提供组基础结构，并将这些组委派给组织中的适当团队。 可以通过多种方式在 Azure AD 中设置组的成员身份，包括：

- **从本地**-组同步可能来自本地目录，这对于已建立组管理过程的组织而言，这些组管理过程可以扩展以在 office 365 中分配许可证。

- 基于**属性/动态**组可以基于用户属性（例如，部门等于 "销售"）在云中创建。 Azure AD 维护组的成员，使其与定义的表达式一致。 将这种类型的组用于许可证分配可启用基于属性的许可证分配，这适用于在其目录中具有高数据质量的组织。

- **委托所有权**-可以在云中创建组，并且可以指定所有者。 通过这种方式，您可以使企业所有者（例如，协作团队或 BI 团队）定义谁有权访问。

如果你当前正在使用手动过程向用户分配许可证和组件，我们建议你实现基于组的许可。 如果当前进程没有监视授权错误或分配的内容与 "可用"，则应为解决授权错误和监视授权分配的过程定义改进。

许可证管理的另一个方面是服务计划（许可证的组成部分）的定义，应基于组织中的工作职能来启用。 如果不需要，授予对服务计划的访问权限，则可能会导致用户在 Office 门户中查看他们未经培训或不应使用的工具。 它可以推动其他技术支持的数量、不必要的预配，并使你的合规性和监管面临风险，例如，将 OneDrive for Business 预配到可能不允许共享内容的个人。

使用以下准则来定义用户的服务计划：

- 管理员应根据用户的角色定义要向用户提供的服务计划的 "捆绑包"，例如，"白环" 辅助角色与 "楼层工作人员"。
- 按群集创建组，并将许可证分配给服务计划。
- 还可以定义一个属性来容纳用户的包。

> [!IMPORTANT]
> Azure AD 中基于组的许可引入了用户处于许可错误状态的概念。 如果注意到任何许可错误，应立即[识别并解决](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems)任何许可证分配问题。

![自动生成的计算机屏幕说明的屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>生命周期管理

如果你当前使用的工具（如[Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/)或第三方系统）依赖于本地基础结构，我们建议你从现有工具中卸载分配，实现基于组的许可，并基于[组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)定义组生命周期管理。 同样，如果你的现有流程不考虑离开组织的新员工或员工，你应该根据动态组部署基于组的许可，并定义组成员的生命周期。 最后，如果针对缺乏生命周期管理的本地组部署了基于组的许可，则考虑使用云组来启用功能，如委派所有权或基于属性的动态成员身份。

### <a name="assignment-of-apps-with-all-users-group"></a>为应用分配 "所有用户" 组

资源所有者可能相信，"**所有用户**"**组在实际情况中**都包含**企业**员工和**来宾**。 因此，在使用 "**所有用户**" 组进行应用程序分配并授予对 SharePoint 内容或应用程序等资源的访问权限时，应该特别小心。

> [!IMPORTANT]
> 如果 "**所有用户**" 组已启用，并且用于条件访问策略、"应用" 或 "资源分配"，请确保在不想让用户包括来宾用户的情况下[保护组](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups#hardening-the-all-users-dynamic-group)。 而且，你应该通过创建并分配给只包含**企业员工**的组来修复你的授权分配。 另一方面，如果你发现 "**所有用户**" 组已启用但未被用于授予对资源的访问权限，请确保你的组织的操作指导有意使用该组（包括**企业员工**和**来宾**）。

### <a name="automated-user-provisioning-to-apps"></a>应用的自动用户预配

应用程序的[自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)是在多个系统之间创建一致的标识、取消设置、取消设置和生命周期的最佳方式。

如果你当前正在以特定方式预配应用，或使用 CSV 文件、JIT 或不满足生命周期管理的本地解决方案等功能，我们建议你使用支持的应用程序的 Azure AD[实现应用程序预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application)，并为 Azure AD 尚不支持的应用程序定义一致的模式。

![Azure AD 预配服务](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect 增量同步循环基线

务必要了解组织中的更改量，并确保不会花费太长时间来进行可预测的同步时间。

[默认的增量同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)频率为30分钟。 如果增量同步花费的时间超过30分钟，或者过渡和生产的增量同步性能之间存在明显的差异，则应调查并查看[影响 Azure AD Connect 性能的因素](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)。

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect 疑难解答建议阅读

- [使用 IdFix 工具为与 Office 365 同步准备目录属性-Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect：同步过程中的错误疑难解答](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>摘要

安全标识基础结构有五个方面。 此列表将帮助你快速查找和采取必要的措施来保护并管理组织中标识及其权利的生命周期。

- 将所有者分配给关键任务。
- 查找并解决同步问题。
- 定义灾难恢复的故障转移策略。
- 简化对许可证的管理以及应用的分配。
- 自动将用户预配到应用。

## <a name="next-steps"></a>后续步骤

[身份验证管理检查和操作](active-directory-ops-guide-auth.md)入门。
