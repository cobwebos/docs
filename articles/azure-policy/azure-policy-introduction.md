---
title: Azure 策略概述
description: Azure 策略是 Azure 中的一项服务，用于创建、分配和管理 Azure 环境中的策略定义。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 51fd0c625ad7e600d54999ddd86e5e49a7c4f14d
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249856"
---
# <a name="what-is-azure-policy"></a>什么是 Azure 策略？

IT 治理可确保组织能够通过有效且高效地使用 IT 来实现其目标。 它通过区分业务目标和 IT 项目来实现这一点。

你的公司是否正遇到了大量似乎难以解决的 IT 问题？
良好的 IT 治理涉及在战略级别上规划各项举措和设置优先级，以便管理和预防问题。 这是 Azure 策略传入的位置。

Azure Policy 是 Azure 中的一项服务，可用于创建、分配和管理策略。 这些策略将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。 为此，Azure Policy 会对资源进行评估，找出那些与所创建的策略不符的资源。 例如，可以设置一项策略，仅允许环境中有特定 SKU 大小的虚拟机。 实施此策略以后，会在创建和更新资源时对其进行评估，并会针对现有资源对其进行评估。 本文档后面会更详细地讲述如何使用 Azure Policy 创建和实施策略。

> [!IMPORTANT]
> 现在，无论定价层如何，为所有分配都提供了 Azure 策略的符合性评估。 如果分配未显示符合性数据，请确保已向 Microsoft.PolicyInsights 资源提供程序注册订阅。

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？

策略和基于角色的访问控制 (RBAC) 之间存在一些主要区别。 RBAC 关注不同范围内的用户操作。 例如，你可能被添加到所需范围的资源组的参与者角色。 该角色允许你对该资源组做出更改。
策略关注部署期间的资源属性，以及现有资源。 例如，可通过策略控制能够预配的资源类型。 或者，可限制能够预配资源的位置。 不同于 RBAC，策略是默认的允许和明确拒绝系统。

### <a name="rbac-permissions-in-azure-policy"></a>Azure Policy 中的 RBAC 权限

Azure Policy 的权限在两个不同的资源提供程序中以操作形式体现：

- [Microsoft.Authorization](../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsight](../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

多个内置角色拥有对 Azure Policy 资源的不同级别权限，例如，**安全管理员**可以管理策略分配和定义，但不能查看符合性信息，而**读者**可以阅读有关策略分配和定义的详细信息，但不能进行更改，也不能查看符合性信息。 **所有者**拥有完全权限，而**参与者**则没有任何 Azure Policy 权限。 若要授予用户查看策略符合性详细信息的权限，请创建一个[自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="policy-definition"></a>策略定义

若要在 Azure Policy 中创建并实施策略，请先创建策略定义。 每种策略定义在其特定的条件下将被强制执行。 并且，在满足条件时将出现随附效果。

在 Azure 策略中，我们将提供一些默认可供使用的内置策略。 例如：

- 需要 SQL Server 12.0：此策略定义具有条件/规则，以确保所有 SQL Server 均使用版本 12.0。 其效果是拒绝所有不符合这些条件的服务器。
- 允许的存储帐户 SKU：此策略定义具有一组条件/规则，可确定正在部署的存储帐户是否在 SKU 大小集内。 其效果是拒绝所有不符合定义的 SKU 大小集的存储帐户。
- 允许的资源类型：此策略定义具有一组条件/规则，以指定贵组织可以部署的资源类型。 其效果是拒绝所有不属于此定义列表的资源。
- **允许的位置**：通过此策略，可限制组织在部署资源时可指定的位置。 其效果是用于强制执行异地符合性要求。
- **允许的虚拟机 SKU**：通过此策略，可指定组织可部署的一组虚拟机 SKU。
- 应用标记及其默认值：若用户未指定所需的标记及其默认值，则通过此策略来应用所需的标记及其默认值。
- **强制执行标记和值**：此策略将对资源强制执行所需的标记和值。
- **不允许的资源类型**：此策略用于指定组织不得部署的资源类型。

若要实现这些策略定义（包括内置定义和自定义定义），需将其分配出去。 可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

请记住，策略重新评估大约一小时进行一次，这意味着，如果在实施策略（创建策略分配）后更改策略定义，则会在一小时内针对资源重新评估该策略。

若要了解有关策略定义结构的详细信息，请查看[策略定义结构](policy-definition.md)。

## <a name="policy-assignment"></a>策略分配

策略分配是在特定作用域内发生的已分配的策略定义。 此作用域的范围是从[管理组](../azure-resource-manager/management-groups-overview.md)到资源组。 术语“作用域”指分配到策略定义的所有资源组、订阅或管理组。 策略分配由所有子资源继承。 这意味着，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。 但是，可以从策略分配中排除子作用域。

例如，可以在订阅作用域中分配阻止创建网络资源的策略。 但排除订阅中用于网络基础结构的一个资源组。 可以向信任的用户授予此网络资源组的访问权限，包括创建网络资源。

在另一个示例中，你可能想要在管理组级别分配资源类型允许列表策略。 然后为子管理组或者甚至直接为订阅分配更多权限策略（以允许更多资源类型）。 但是，此示例不会正常工作，因为策略是显式拒绝系统。 与此相反，你需要从管理组级别策略分配中排除子管理组或订阅。 然后，为子管理组或订阅级别分配更多权限策略。 总之，如果任何策略导致资源被拒绝，则允许该资源的唯一方法是修改拒绝策略。

有关设置策略定义和分配的详细信息，请参阅[创建策略分配，识别 Azure 环境中的不合规资源](assign-policy-definition.md)。

## <a name="policy-parameters"></a>策略参数

策略参数通过减少必须创建的策略定义数量来帮助简化策略管理。 在创建策略定义时可定义参数，以使其更为通用。 然后就可以为不同方案重复使用该策略定义。 要执行此操作，请在分配策略定义时传入不同的值。 例如，为订阅指定一组位置。

在创建策略定义时定义/创建参数。 在定义参数后，会为它指定一个名称，并且可选择为其提供一个值。 例如，可以为标题为“位置”的策略定义一个参数。 然后，可在分配策略时赋予其不同的值，如 EastUS 或 WestUS。

有关策略参数的详细信息，请参阅[资源策略概述 - 参数](policy-definition.md#parameters)。

## <a name="initiative-definition"></a>计划定义

计划定义是策略定义的集合，专为实现一个单一的总体目标而量身定制。 计划定义可以简化管理和分配策略定义。 它们通过将一组策略组合为一个单独的项来实现简化。 例如，可以创建一个标题为“启用 Azure 安全中心中的监视”的计划，用于专门监视 Azure 安全中心中的所有可用的安全建议。

在此计划中，将具有特定策略定义，例如：

- 监视安全中心中未加密的 SQL 数据库 – 用于监视未加密的 SQL 数据库和服务器。
- 监视安全中心中的操作系统漏洞 – 用于监视不满足配置基线的服务器。
- 监视安全中心中缺失的终结点保护 – 用于监视不具备已安装终结点保护代理的服务器。

## <a name="initiative-assignment"></a>计划分配

类似于策略分配，计划分配是分配给特定作用域的计划定义。 计划分配将减少为每个作用域生成多个计划定义的需要。 另外，此作用域的范围也是从管理组到资源组。

根据前面的示例，可以将“启用 Azure 安全中心中的监视”计划分配给不同的作用域。 例如，可将一个赋值分配给 subscriptionA。
并将另一个分配给 subscriptionB。

## <a name="initiative-parameters"></a>计划参数

类似于策略参数，计划参数通过减少冗余来帮助简化计划管理。 实质上，计划参数是计划内的策略定义正在使用的参数列表。

例如，假设出现这样一种情况，有一个带有两个策略定义（**policyA** 和 **policyB**，每个都需要不同类型的参数）的计划定义 - initiativeC：

| 策略 | 参数的名称 |参数的类型  |注意 |
|---|---|---|---|
| policyA | allowedLocations | 数组  |此参数要求将值设置为字符串列表，因为参数类型已定义为数组 |
| policyB | allowedSingleLocation |字符串 |此参数要求将值设置为一个字词，因为参数类型已定义为字符串 |

在此情况下，定义 initiativeC 的计划参数时，有三个选项可供选择：

- 使用此计划中的策略定义参数：在此示例中，allowedLocations 和 allowedSingleLocation 为 initiativeC 的计划参数。
- 向此计划定义中策略定义的参数提供值。 在此示例中，可以向 policyA 的参数 – allowedLocations 和 policyB 的参数 – allowedSingleLocation 提供位置列表。 此外，也可在分配此计划时提供值。
- 分配此计划时，提供可供使用的值列表选项。 在分配此计划时，从计划内的策略定义继承的参数只能具有此提供列表中的值。

例如，你可以在计划定义中创建一个值选项列表，它包含 EastUS、WestUS、CentralUS 和 WestEurope。 如果执行此操作，则无法在计划分配期间输入其他值（如东 Southeast Asia，因为它不属于这个列表）。

## <a name="maximum-count-of-policy-objects"></a>Policy 对象的最大计数

Azure Policy 的每个对象类型都有一个最大计数。 “作用域”条目是指订阅或管理组。

| 其中 | 对象 | 最大计数 |
|---|---|---|
| 范围 | 策略定义 | 250 |
| 范围 | 计划定义 | 100 |
| 租户 | 计划定义 | 1000 |
| 范围 | 策略分配 | 100 |
| 策略定义 | parameters | 20 |
| 计划定义 | 策略 | 100 |
| 计划定义 | parameters | 100 |
| 策略分配 | 排除项 (notScopes) | 100 |
| 策略规则 | 嵌套式条件语句 | 512 |

## <a name="recommendations-for-managing-policies"></a>管理策略的建议

以下是在创建和管理策略定义及分配时建议遵循的几个指标以及需注意的提示：

- 如果在环境中创建策略定义，我们建议从审核效果（而不是拒绝效果）开始，以跟踪环境中资源上策略定义的影响。 如果有用于自动纵向扩展应用程序的脚本，那么设置拒绝效果可能会影响这些已经执行的自动化任务。
- 请务必在创建定义和分配时考虑组织的层次结构。 我们建议在更高级别创建定义，例如，在管理组或订阅级别进行创建，并在下一子级别进行分配。 例如，如果在管理组级别创建策略定义，则可以在管理组中将该定义的一个策略分配缩小到订阅级别。
- 即使在只需考虑一个策略的情况下，我们也建议始终使用计划定义，而不使用策略定义。 例如，假设你有一个策略定义 - policyDefA，并在计划定义 - initiativeDefC 下创建它，如果你决定在稍后使用类似 policyDefA 的目标为 policyDefB 创建另一个策略定义，则只需将其添加到 initiativeDefC 下，并通过这种方式来更好地跟踪它们。
- 请记住，从计划定义创建计划分配后，添加到计划定义的任何新策略定义都将在该计划定义下的计划分配下自动滚动。
- 一旦触发计划分配，也会触发该计划中的所有策略。 但是，如果需要单独执行某个策略，最好不要将其包含在计划中。

## <a name="video-overview"></a>视频概述

以下 Azure Policy 概述来自 Build 2018。 如需下载幻灯片或视频，请访问第 9 频道的 [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030)（通过 Azure Policy 治理 Azure 环境）。

> [!VIDEO https://channel9.msdn.com/events/Build/2018/THR2030/player]

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure 策略以及一些关键概念，下面是建议的后续步骤：

- [分配策略定义](assign-policy-definition.md)
- [使用 Azure CLI 分配策略定义](assign-policy-definition-cli.md)
- [使用 PowerShell 分配策略定义](assign-policy-definition-ps.md)
- 参阅[使用 Azure 管理组来组织资源](../azure-resource-manager/management-groups-overview.md)，了解什么是管理组
- 观看第 9 频道的 [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030)（通过 Azure Policy 治理 Azure 环境）