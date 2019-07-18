---
title: Azure Policy 概述
description: Azure Policy 是 Azure 中的一项服务，用于创建、分配和管理 Azure 环境中的策略定义。
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: da045a561fba81bf9a5e412a6f2bf0a6160acfa7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807452"
---
# <a name="overview-of-the-azure-policy-service"></a>Azure 策略服务概述

治理验证组织是否能够通过有效且高效地使用 IT 来实现其目标。 它通过详细说明业务目标和 IT 项目来满足这一需求。

你的公司是否正遇到了大量似乎难以解决的 IT 问题？
良好的 IT 治理涉及在战略级别上规划各项举措和设置优先级，以便管理和预防问题。 Azure Policy 迎合了此策略需求。

Azure Policy 是 Azure 中的一项服务，用于创建、分配和管理策略。 这些策略将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。 Azure Policy 通过评估资源是否符合指定策略来满足此需求。 例如，可以设置一项策略，仅允许环境中有特定 SKU 大小的虚拟机。 实施此策略后，将评估新资源和现有资源的符合性。 通过使用正确的策略类型，可以确保现有资源的符合性。 本文档后面将更详细地讲述如何使用 Azure Policy 创建和实施策略。

> [!IMPORTANT]
> 现在，无论定价层如何，为所有分配都提供了 Azure Policy 的符合性评估。 如果分配未显示符合性数据，请确保已向 Microsoft.PolicyInsights 资源提供程序注册订阅。

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？

Azure Policy 和基于角色的访问控制 (RBAC) 之间存在一些主要区别。 RBAC 关注不同范围内的用户操作。 你可能会被添加到资源组的参与者角色，可对该资源组做出更改。 Azure Policy 关注部署期间的资源属性，以及现有资源。 Azure Policy 控制各种属性，例如资源的类型或位置。 不同于 RBAC，Azure Policy 是默认的允许和明确拒绝系统。

### <a name="rbac-permissions-in-azure-policy"></a>Azure Policy 中的 RBAC 权限

Azure Policy 在两个资源提供程序中具有多个权限（称为操作）：

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

许多内置角色可授予对 Azure Policy 资源的权限。 “资源策略参与者(预览版)”角色包括大多数 Azure Policy 操作  。 “所有者”具有完全权限  。 “参与者”和“读者”都可以使用所有读取 Azure Policy 操作，但“参与者”还可以触发修正    。

如果没有任何内置角色具有所需的权限，可创建[自定义角色](../../role-based-access-control/custom-roles.md)。

## <a name="policy-definition"></a>策略定义

若要在 Azure Policy 中创建并实施策略，请先创建策略定义。 每种策略定义在其特定的条件下将被强制执行。 并且，在满足条件时将出现定义的效果。

在 Azure Policy 中，我们将提供一些默认可供使用的内置策略。 例如：

- **需要 SQL Server 12.0**：验证是否所有 SQL Server 都使用版本 12.0。 其效果是拒绝所有不符合这些条件的服务器。
- **允许的存储帐户 SKU**：确定正在部署的存储帐户是否在 SKU 大小集内。 其效果是拒绝所有不符合定义的 SKU 大小集的存储帐户。
- **允许的资源类型**：定义可以部署的资源类型。 其效果是拒绝所有不属于此定义列表的资源。
- **允许的位置**：限制新资源的可用位置。 其效果是用于强制执行异地符合性要求。
- **允许的虚拟机 SKU**：指定可以部署的虚拟机 SKU 集。
- **应用标记及其默认值**：如果部署请求未指定，则应用所需的标记及其默认值。
- **强制实施标记及其值**：对资源强制执行所需的标记及其值。
- **不允许的资源类型**：禁止部署资源类型的列表。

若要实现这些策略定义（包括内置定义和自定义定义），需将其分配出去。 可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

策略评估采用多种不同的操作，例如策略分配或策略更新。 有关完整列表，请参阅[策略评估触发器](./how-to/get-compliance-data.md#evaluation-triggers)。

若要了解有关策略定义结构的详细信息，请查看[策略定义结构](./concepts/definition-structure.md)。

## <a name="policy-assignment"></a>策略分配

策略分配是在特定作用域内发生的已分配的策略定义。 此作用域的范围是从[管理组](../management-groups/overview.md)到资源组。 术语“作用域”指分配到策略定义的所有资源组、订阅或管理组  。 策略分配由所有子资源继承。 此设计意味着应用于资源组的策略也应用于该资源组中的资源。 但是，可以从策略分配中排除子作用域。

例如，可以在订阅作用域中分配阻止创建网络资源的策略。 可以排除订阅中用于网络基础结构的资源组。 然后可以向信任的用户授予此网络资源组的访问权限，包括创建网络资源。

在另一个示例中，你可能想要在管理组级别分配资源类型允许列表策略。 然后为子管理组或者甚至直接为订阅分配更多权限策略（以允许更多资源类型）。 但是，此示例不会正常工作，因为策略是显式拒绝系统。 与此相反，你需要从管理组级别策略分配中排除子管理组或订阅。 然后，为子管理组或订阅级别分配更多权限策略。 如果任何策略导致资源被拒绝，则允许该资源的唯一方法是修改拒绝策略。

有关通过门户设置策略定义和分配的详细信息，请参阅[创建策略分配，识别 Azure 环境中的不合规资源](assign-policy-portal.md)。 还可以使用 [PowerShell](assign-policy-powershell.md) 和 [Azure CLI](assign-policy-azurecli.md) 的步骤。

## <a name="policy-parameters"></a>策略参数

策略参数通过减少必须创建的策略定义数量来帮助简化策略管理。 在创建策略定义时可定义参数，以使其更为通用。 然后就可以为不同方案重复使用该策略定义。 要执行此操作，请在分配策略定义时传入不同的值。 例如，为订阅指定一组位置。

在创建策略定义时定义参数。 在定义参数后，会为它指定一个名称，并且可选择为其提供一个值。 例如，可以为标题为“位置”的策略定义一个参数  。 然后，可在分配策略时赋予其不同的值，如 EastUS 或 WestUS   。

有关策略参数的详细信息，请参阅[定义结构 - 参数](./concepts/definition-structure.md#parameters)。

## <a name="initiative-definition"></a>计划定义

计划定义是策略定义的集合，专为实现一个单一的总体目标而量身定制。 计划定义可以简化管理和分配策略定义。 它们通过将一组策略组合为一个单独的项来实现简化。 例如，可以创建一个标题为“启用 Azure 安全中心中的监视”  的计划，用于专门监视 Azure 安全中心中的所有可用的安全建议。

在此计划中，将具有特定策略定义，例如：

-  监视安全中心中未加密的 SQL 数据库 – 用于监视未加密的 SQL 数据库和服务器。
- **监视安全中心中的操作系统漏洞** - 用于监视不满足配置基线的服务器。
-  监视安全中心中缺失的终结点保护 – 用于监视不具备已安装终结点保护代理的服务器。

## <a name="initiative-assignment"></a>计划分配

类似于策略分配，计划分配是分配给特定作用域的计划定义。 计划分配将减少为每个作用域生成多个计划定义的需要。 另外，此范围也是从管理组到资源组。

每个计划都可以分配给不同的作用域。 可以将一个计划分配给 subscriptionA 和 subscriptionB   。

## <a name="initiative-parameters"></a>计划参数

类似于策略参数，计划参数通过减少冗余来帮助简化计划管理。 计划参数是计划内的策略定义正在使用的参数。

例如，假设出现这样一种情况，有一个带有两个策略定义（**policyA** 和 **policyB**，每个都需要不同类型的参数）的计划定义 - initiativeC  ：

| 策略 | 参数的名称 |参数的类型  |注意 |
|---|---|---|---|
| policyA | allowedLocations | 数组  |此参数要求将值设置为字符串列表，因为参数类型已定义为数组 |
| policyB | allowedSingleLocation |字符串 |此参数要求将值设置为一个字词，因为参数类型已定义为字符串 |

在此情况下，定义 initiativeC  的计划参数时，有三个选项可供选择：

- 使用此计划中的策略定义参数：在此示例中，allowedLocations 和 allowedSingleLocation 为 initiativeC 的计划参数    。
- 向此计划定义中策略定义的参数提供值。 在此示例中，可以向 policyA 的参数 – allowedLocations  和 policyB 的参数 – allowedSingleLocation  提供位置列表。 此外，也可在分配此计划时提供值。
- 分配此计划时，提供可供使用的值  列表选项。 在分配此计划时，从计划内的策略定义继承的参数只能具有此提供列表中的值。

在计划定义中创建值选项时，无法在计划分配期间输入其他值，因为它不属于列表。

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy 对象的最大计数

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>管理策略的建议

请记住以下几个要点和提示：

- 从审核效果（而不是拒绝效果）开始，以跟踪策略定义对环境中资源的影响。 如果有用于自动缩放应用程序的脚本，那么设置拒绝效果可能会影响此类已经执行的自动化任务。

- 请在创建定义和分配时考虑组织的层次结构。 我们建议在更高级别创建定义，例如管理组或订阅级别。 然后，在下一子级别创建分配。 如果在管理组中创建定义，则可以将分配范围缩小到该管理组中的订阅或资源组。

- 我们建议创建并分配计划定义，即使对于单个策略定义，也是如此。
例如，你有策略定义 policyDefA 并在计划定义 initiativeDefC 下创建它   。 如果稍后为 policyDefB 创建另一个策略定义，其目标类似于 policyDefA，则可以在 initiativeDefC 下添加它并一起跟踪它们    。

- 创建计划分配后，添加到该计划中的策略定义也将成为该计划分配的一部分。

- 评估计划分配后，还会评估计划内的所有策略。 如果需要单独评估某个策略，最好不要将其包含在计划中。

## <a name="video-overview"></a>视频概述

以下 Azure Policy 概述来自 Build 2018。 如需下载幻灯片或视频，请访问第 9 频道的 [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030)（通过 Azure Policy 治理 Azure 环境）。

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure 策略以及一些关键概念，下面是建议的后续步骤：

- [使用门户分配策略定义](assign-policy-portal.md)。
- [使用 Azure CLI 分配策略定义](assign-policy-azurecli.md)。
- [使用 PowerShell 分配策略定义](assign-policy-powershell.md)。
- 参阅[使用 Azure 管理组来组织资源](..//management-groups/overview.md)，了解什么是管理组。
- 观看第 9 频道的 [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030)（通过 Azure Policy 治理 Azure 环境）。