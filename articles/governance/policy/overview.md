---
title: Azure Policy 概述
description: Azure Policy 是 Azure 中的一项服务，用于创建、分配和管理 Azure 环境中的策略定义。
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 4ec09c8a38e22fc14980422bfe9a80a2bf3edda4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097364"
---
# <a name="what-is-azure-policy"></a>什么是 Azure Policy？

Azure Policy 可帮助实施组织标准并大规模评估合规性。 Azure Policy 通过其合规性仪表板提供一个聚合视图来评估环境的整体状态，并允许用户按资源、按策略粒度向下钻取。 它还通过对现有资源的批量修正以及对新资源的自动修正，帮助资源符合规范。

Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。 Azure 环境中已经内置了这些常见用例的策略定义，帮助你入门。

## <a name="overview"></a>概述

Azure Policy 通过将 Azure 中资源的属性与业务规则进行比较，来评估这些资源。 以 [JSON 格式](./concepts/definition-structure.md)描述的这些业务规则称为[策略定义](#policy-definition)。 为了简化管理，可以组合多个业务规则来构成一个[策略计划](#initiative-definition)（有时称为“策略集”）。  构成业务规则后，策略定义或计划将[分配](#assignments)到 Azure 支持的任何资源范围，例如[管理组](../management-groups/overview.md)、订阅、[资源组](../../azure-resource-manager/management/overview.md#resource-groups)或单个资源。 分配会应用到该分配的[范围](../../azure-resource-manager/management/overview.md#understand-scope)内的所有资源。
必要时可以排除子范围。

Azure Policy 使用 [JSON 格式](./concepts/definition-structure.md)构成评估机制用来确定某个资源是否合规的逻辑。 定义包括元数据和策略规则。 定义的规则可以使用与所需方案完全匹配的函数、参数、逻辑运算符、条件和属性[别名](./concepts/definition-structure.md#aliases)。 策略规则确定要评估分配范围内的哪些资源。

### <a name="understand-evaluation-outcomes"></a>了解评估结果

资源将在资源生命周期、策略分配生命周期内的特定时间进行评估，并接受日常进行的合规性评估。 出现以下时机或事件时，就会对资源进行评估：

- 在策略分配范围内创建、更新或删除了资源。
- 最近已将策略或计划分配到某个范围。
- 更新了已分配到某个范围的策略或计划。
- 在标准合规性评估周期内（每 24 小时发生一次）。

有关何时以及如何进行策略评估的详细信息，请参阅[评估触发器](./how-to/get-compliance-data.md#evaluation-triggers)。

### <a name="control-the-response-to-an-evaluation"></a>控制对评估的响应

处理不合规资源的业务规则根据组织的不同而有很大的不同。 下面以示例方式说明了组织希望平台如何对不合规资源进行响应：

- 拒绝资源更改
- 记录对资源的更改
- 在更改之前改变资源
- 在更改之后改变资源
- 部署相关的合规资源

Azure Policy 通过应用[效果](./concepts/effects.md)来实现这其中的每种业务响应方式。 效果在[策略定义](./concepts/definition-structure.md)的“策略规则”部分进行设置。 

### <a name="remediate-non-compliant-resources"></a>修正不符合资源

尽管这些效果主要是在创建或更新资源时影响资源，但 Azure Policy 还支持处理现有的不合规资源，而无需改变该资源。 若要详细了解如何使现有资源合规，请参阅[修正资源](./how-to/remediate-resources.md)。

### <a name="video-overview"></a>视频概述

以下 Azure Policy 概述来自 Build 2018。 如需下载幻灯片或视频，请访问第 9 频道的 [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030)（通过 Azure Policy 治理 Azure 环境）。

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>入门

### <a name="azure-policy-and-rbac"></a>Azure Policy 和 RBAC

Azure Policy 和基于角色的访问控制 (RBAC) 之间存在一些主要区别。 Azure Policy 通过检查资源管理器中显示的资源属性和某些资源提供程序的属性来评估状态。 Azure Policy 不会限制操作。  Azure Policy 确保资源状态符合业务规则，而不考虑更改是谁做出的或者谁有权做出更改。

RBAC 重点关注如何管理不同范围的用户[操作](../../role-based-access-control/resource-provider-operations.md)。 如果需要控制某项操作，则 RBAC 是可以使用的适当工具。 即使个人有权执行操作，但如果结果是不合规的资源，Azure Policy 也仍会阻止创建或更新操作。

RBAC 和 Azure Policy 的组合在 Azure 中提供了全范围控制。

### <a name="rbac-permissions-in-azure-policy"></a>Azure Policy 中的 RBAC 权限

Azure Policy 在两个资源提供程序中具有多个权限（称为操作）：

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

许多内置角色可授予对 Azure Policy 资源的权限。 “资源策略参与者”角色包括大多数 Azure Policy 操作  。 “所有者”具有完全权限  。 “参与者”和“读取者”都有权访问所有 Azure Policy 读取操作。    “参与者”可以触发资源修正，但无法创建定义或分配。  

如果没有任何内置角色具有所需的权限，可创建[自定义角色](../../role-based-access-control/custom-roles.md)。

> [!NOTE]
> deployIfNotExists 策略分配的托管标识需有足够的权限才能创建或更新模板中包含的资源。  有关详细信息，请参阅[配置有关修正的策略定义](./how-to/remediate-resources.md#configure-policy-definition)。

### <a name="resources-covered-by-azure-policy"></a>Azure Policy 涵盖的资源

Azure Policy 评估 Azure 中的所有资源。 对于某些资源提供程序（例如 [Guest Configuration](./concepts/guest-configuration.md)、[Azure Kubernetes 服务](../../aks/intro-kubernetes.md)和 [Azure Key Vault](../../key-vault/key-vault-overview.md)），可以使用一个更深度的集成来管理设置和对象。 有关详细信息，请参阅[资源提供程序模式](./concepts/definition-structure.md)。

### <a name="recommendations-for-managing-policies"></a>管理策略的建议

请记住以下几个要点和提示：

- 从审核效果（而不是拒绝效果）开始，以跟踪策略定义对环境中资源的影响。 如果有用于自动缩放应用程序的脚本，那么设置拒绝效果可能会影响此类已经执行的自动化任务。

- 请在创建定义和分配时考虑组织的层次结构。 我们建议在更高级别创建定义，例如管理组或订阅级别。 然后，在下一子级别创建分配。 如果在管理组中创建定义，则可以将分配范围缩小到该管理组中的订阅或资源组。

- 我们建议创建并分配计划定义，即使对于单个策略定义，也是如此。
  例如，你有策略定义 policyDefA 并在计划定义 initiativeDefC 下创建它   。 如果稍后为 policyDefB 创建另一个策略定义，其目标类似于 policyDefA，则可以在 initiativeDefC 下添加它并一起跟踪它们    。

- 创建计划分配后，添加到该计划中的策略定义也将成为该计划分配的一部分。

- 评估计划分配后，还会评估计划内的所有策略。
  如果需要单独评估某个策略，最好不要将其包含在计划中。

## <a name="azure-policy-objects"></a>Azure Policy 对象

### <a name="policy-definition"></a>策略定义

若要在 Azure Policy 中创建并实施策略，请先创建策略定义。 每种策略定义在其特定的条件下将被强制执行。 并且，在满足条件时将出现定义的效果。

在 Azure Policy 中，我们将提供一些默认可供使用的内置策略。 例如：

- **允许的存储帐户 SKU**（拒绝）：确定正在部署的存储帐户是否在 SKU 大小集内。 其效果是拒绝所有不符合定义的 SKU 大小集的存储帐户。
- **允许的资源类型**（拒绝）：定义可以部署的资源类型。 其效果是拒绝所有不属于此定义列表的资源。
- **允许的位置**（拒绝）：限制新资源的可用位置。 其效果是用于强制执行异地符合性要求。
- **允许的虚拟机 SKU**（拒绝）：指定可以部署的虚拟机 SKU 集。
- **将标记添加到资源**（修改）：如果部署请求未指定，则应用所需的标记及其默认值。
- **追加标记及其默认值**（追加）：对资源强制执行所需的标记及其值。
- **不允许的资源类型**（拒绝）：禁止部署资源类型的列表。

若要实现这些策略定义（包括内置定义和自定义定义），需将其分配出去。 可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

策略评估采用多种不同的操作，例如策略分配或策略更新。 有关完整列表，请参阅[策略评估触发器](./how-to/get-compliance-data.md#evaluation-triggers)。

若要了解有关策略定义结构的详细信息，请查看[策略定义结构](./concepts/definition-structure.md)。

策略参数通过减少必须创建的策略定义数量来帮助简化策略管理。 在创建策略定义时可定义参数，以使其更为通用。 然后就可以为不同方案重复使用该策略定义。 要执行此操作，请在分配策略定义时传入不同的值。 例如，为订阅指定一组位置。

在创建策略定义时定义参数。 在定义参数后，会为它指定一个名称，并且可选择为其提供一个值。 例如，可以为标题为“位置”的策略定义一个参数  。 然后，可在分配策略时赋予其不同的值，如 EastUS 或 WestUS   。

有关策略参数的详细信息，请参阅[定义结构 - 参数](./concepts/definition-structure.md#parameters)。

### <a name="initiative-definition"></a>计划定义

计划定义是策略定义的集合，专为实现一个单一的总体目标而量身定制。 计划定义可以简化管理和分配策略定义。 它们通过将一组策略组合为一个单独的项来实现简化。 例如，可以创建一个标题为“启用 Azure 安全中心中的监视”  的计划，用于专门监视 Azure 安全中心中的所有可用的安全建议。

> [!NOTE]
> Azure CLI 和 Azure PowerShell 等 SDK 使用名为 PolicySet 的属性和参数来引用计划  。

在此计划中，将具有特定策略定义，例如：

-  监视安全中心中未加密的 SQL 数据库 – 用于监视未加密的 SQL 数据库和服务器。
- **监视安全中心中的操作系统漏洞** - 用于监视不满足配置基线的服务器。
-  监视安全中心中缺失的终结点保护 – 用于监视不具备已安装终结点保护代理的服务器。

类似于策略参数，计划参数通过减少冗余来帮助简化计划管理。 计划参数是计划内的策略定义正在使用的参数。

例如，假设出现这样一种情况，有一个带有两个策略定义（**policyA** 和 **policyB**，每个都需要不同类型的参数）的计划定义 - initiativeC  ：

| 策略 | 参数的名称 |参数的类型  |注意 |
|---|---|---|---|
| policyA | allowedLocations | array  |此参数要求将值设置为字符串列表，因为参数类型已定义为数组 |
| policyB | allowedSingleLocation |字符串 |此参数要求将值设置为一个字词，因为参数类型已定义为字符串 |

在此情况下，定义 initiativeC  的计划参数时，有三个选项可供选择：

- 使用此计划中的策略定义参数：在此示例中，allowedLocations 和 allowedSingleLocation 为 initiativeC 的计划参数    。
- 向此计划定义中策略定义的参数提供值。 在此示例中，可以向 policyA 的参数 – allowedLocations  和 policyB 的参数 – allowedSingleLocation    提供位置列表。 此外，也可在分配此计划时提供值。
- 分配此计划时，提供可供使用的值  列表选项。 在分配此计划时，从计划内的策略定义继承的参数只能具有此提供列表中的值。

在计划定义中创建值选项时，无法在计划分配期间输入其他值，因为它不属于列表。

### <a name="assignments"></a>分配

分配是指已分配的策略定义或计划，在特定范围内生效。 此作用域的范围是从[管理组](../management-groups/overview.md)到单个资源。 术语“范围”指定义分配到的所有资源、资源组、订阅或管理组  。 分配由所有子资源继承。 此设计意味着应用到某个资源组的定义也会应用到该资源组中的资源。 但是，可以从分配中排除子范围。

例如，可以在订阅范围分配一个阻止创建网络资源的定义。 可以排除订阅中用于网络基础结构的资源组。 然后可以向信任的用户授予此网络资源组的访问权限，包括创建网络资源。

另举一例：你可能想要在管理组级别分配资源类型允许列表定义， 然后为子管理组或者甚至直接为订阅分配更宽松的策略（以允许更多资源类型）。 但是，此示例无法实现，因为 Azure Policy 是显式拒绝系统。 你不需要那样做，只需要从管理组级别分配中排除子管理组或订阅， 然后为子管理组或订阅级别分配更宽松的定义。 如果任何分配导致资源被拒绝，则允许该资源的唯一方法是修改拒绝分配。

有关通过门户设置分配的详细信息，请参阅[创建策略分配以识别 Azure 环境中的不合规资源](assign-policy-portal.md)。 还可以使用 [PowerShell](assign-policy-powershell.md) 和 [Azure CLI](assign-policy-azurecli.md) 的步骤。

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy 对象的最大计数

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure Policy 以及一些关键概念，下面是建议的后续步骤：

- [查看策略定义结构](./concepts/definition-structure.md)。
- [使用门户分配策略定义](./assign-policy-portal.md)。
