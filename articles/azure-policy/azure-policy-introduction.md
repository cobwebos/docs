---
title: "Azure 策略概述 | Microsoft 文档"
description: "Azure 策略是 Azure 中的一项服务，用于创建、分配和管理 Azure 环境中的策略定义。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 11/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: b784c79166eb614988d84b4553bb95e4d1e457af
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="what-is-azure-policy"></a>什么是 Azure 策略？

IT 治理清楚地区分了业务目标和 IT 项目。 良好的 IT 治理涉及在战略级别上规划各项举措和设置优先级。 你的公司是否正遇到了大量似乎难以解决的 IT 问题？ 实施这些策略有助于优化管理，预防问题。 Azure 策略正适合用在这些策略的实施处。

Azure 策略是 Azure 中的一项服务，可用于创建、分配和管理策略定义。 策略定义将在整个资源中强制实施不同的规则和操作，以便这些资源符合公司标准和服务级别协议。 Azure 策略对资源进行计算和扫描，并找到与所定义策略不相符的资源。 例如，可指定仅允许特定类型的虚拟机的策略。 或者，可要求所有资源都拥有特定标记。 在创建和更新资源时评估策略。

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？

策略和基于角色的访问控制 (RABC) 之间存在一些主要区别。 RBAC 关注不同范围内的用户操作。 例如，你可能被添加到所需范围的资源组的参与者角色。 该角色允许你对该资源组做出更改。 策略关注部署期间的资源属性，以及现有资源。 例如，可通过策略控制能够预配的资源类型。 或者，可限制能够预配资源的位置。 不同于 RBAC，策略是默认的允许和明确拒绝系统。

若要使用策略，必须通过 RBAC 完成身份验证。 具体而言，帐户需要：

- 定义策略的 `Microsoft.Authorization/policydefinitions/write` 权限。
- 分配策略的 `Microsoft.Authorization/policyassignments/write` 权限。

**参与者**角色中未包括这些权限。


## <a name="policy-definition"></a>策略定义

每种策略定义在其特定的条件下将被强制执行。 此外，在满足条件时还将出现随附操作。

在 Azure 策略中，我们将提供一些默认可供使用的内置策略。 例如：

- 需要 SQL Server 12.0：此策略定义具有条件/规则，以确保所有 SQL Server 均使用版本 12.0。 其操作是拒绝所有不符合这些条件的服务器。
- 允许的存储帐户 SKU：此策略定义具有一组条件/规则，可确定正在部署的存储帐户是否在 SKU 大小集内。 其操作是拒绝所有不符合定义的 SKU 大小集的服务器。
- 允许的资源类型：此策略定义具有一组条件/规则，以指定贵组织可以部署的资源类型。 其操作是拒绝所有不属于此定义列表的资源。
- **允许的位置**：通过此策略，可限制组织在部署资源时可指定的位置。 其操作用于强制执行地区符合性要求。
- **允许的虚拟机 SKU**：通过此策略，可指定组织可部署的一组虚拟机 SKU。
- 应用标记及其默认值：若用户未指定所需的标记及其默认值，则通过此策略来应用所需的标记及其默认值。
- **强制执行标记和值**：此策略将对资源强制执行所需的标记和值。
- **不允许的资源类型**：此策略用于指定组织不得部署的资源类型。

可通过 Azure 门户、PowerShell 或 Azure CLI 来分配上述任意策略。

若要了解有关策略定义结构的详细信息，请查看本文中的[策略定义结构](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure)部分。

## <a name="policy-assignment"></a>策略分配

策略分配是在特定作用域内发生的已分配的策略定义。 此作用域的范围是从管理组到资源组。 术语“作用域”指分配到策略定义的所有资源组、订阅或管理组。 策略分配由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。 但是，可以从策略分配中排除子作用域。 例如，可以在订阅作用域中分配阻止创建网络资源的策略。 但排除订阅中用于网络基础结构的一个资源组。 可以向信任的用户授予此网络资源组的访问权限，包括创建网络资源。

有关设置策略定义和分配的详细信息，请参阅[创建策略分配，识别 Azure 环境中的不合规资源](assign-policy-definition.md)。

## <a name="policy-parameters"></a>策略参数

策略参数通过减少必须创建的策略定义数量来帮助简化策略管理。 在创建策略定义时可定义参数，以使其更为通用。 然后就可以为不同方案重复使用该策略定义。 要执行此操作，请在分配策略定义时传入不同的值。 例如，为订阅指定一组位置。

在创建策略定义时定义/创建参数。 在定义参数后，会为它指定一个名称，并且可选择为其提供一个值。 例如，可以为标题为“位置”的策略定义一个参数。 然后，可在分配策略时赋予其不同的值，如 EastUS 或 WestUS。

<!--
Next link should point to new Concept page for Parameters
-->
有关策略参数的详细信息，请参阅[资源策略概述 - 参数](../azure-resource-manager/resource-manager-policy.md#parameters)。

## <a name="initiative-definition"></a>计划定义
计划定义是策略定义的集合，专为实现一个单一的总体目标而量身定制。 计划定义可以简化管理和分配策略定义。 它们通过将一组策略组合为一个单独的项来实现简化。 例如，可以创建一个标题为“启用 Azure 安全中心中的监视”的计划，用于专门监视 Azure 安全中心中的所有可用的安全建议。

在此计划中，将具有特定策略定义，例如：

1. 监视安全中心中未加密的 SQL 数据库 – 用于监视未加密的 SQL 数据库和服务器。
2. 监视安全中心中的操作系统漏洞 – 用于监视不满足配置基线的服务器。
3. 监视安全中心中缺失的终结点保护 – 用于监视不具备已安装终结点保护代理的服务器。

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>计划分配
类似于策略分配，计划分配是分配给特定作用域的计划定义。 计划分配将减少为每个作用域生成多个计划定义的需要。 另外，此作用域的范围也是从管理组到资源组。

根据前面的示例，可以将“启用 Azure 安全中心中的监视”计划分配给不同的作用域。 例如，可将一个赋值分配给 subscriptionA。 并将另一个分配给 subscriptionB。

## <a name="initiative-parameters"></a>计划参数
类似于策略参数，计划参数通过减少冗余来帮助简化计划管理。 实质上，计划参数是计划内的策略定义正在使用的参数列表。

例如，假设出现以下情况，有一个带有两个策略定义的计划定义 - initiativeC。 每个策略定义具有一个定义的参数：

| 策略 | 参数的名称 |参数的类型  |注意 |
|---|---|---|---|
| policyA | allowedLocations | 数组  |此参数要求将值设置为字符串列表，因为参数类型已定义为数组 |
| policyB | allowedSingleLocation |字符串 |此参数要求将值设置为一个字词，因为参数类型已定义为字符串 |

在此情况下，定义 initiativeC 的计划参数时，有三个选项可供选择：

1. 使用此计划中的策略定义参数：在此示例中，allowedLocations 和 allowedSingleLocation 为 initiativeC 的计划参数。
2. 向此计划定义中策略定义的参数提供值。 在此示例中，可以向 policyA 的参数 – allowedLocations 和 policyB 的参数 – allowedSingleLocation 提供位置列表。
此外，也可在分配此计划时提供值。
3. 分配此计划时，提供可供使用的值列表选项。 在分配此计划时，从计划内的策略定义继承的参数只能具有此提供列表中的值。

例如，你可以在计划定义中创建一个值选项列表，它包含 EastUS、WestUS、CentralUS 和 WestEurope。 如果执行此操作，则无法在计划分配期间输入其他值（如东 Southeast Asia，因为它不属于这个列表）。

## <a name="recommendations-for-managing-policies"></a>管理策略的建议

以下是在创建和管理策略定义及分配时建议要遵循的几个指标：

- 如果在环境中创建策略定义，我们建议从审核效果（而不是拒绝效果）开始，以跟踪环境中资源上策略定义的影响。 如果有用于自动纵向扩展应用程序的脚本，那么设置拒绝效果可能会影响这些已经执行的自动化任务。
- 请务必在创建定义和分配时考虑组织的层次结构。 我们建议在更高级别创建定义，例如，在管理组或订阅级别进行创建，并在下一子级别进行分配。 例如，如果在管理组级别创建策略定义，则可以在管理组中将该定义的一个策略分配缩小到订阅级别。
- 我们鼓励使用标准定价层，以更好地了解环境的符合性状态。 有关定价模型和每种模型优惠的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/azure-policy)。
- 即使在只需考虑一个策略的情况下，我们也建议始终使用计划定义，而不使用策略定义。 例如，假设你有一个策略定义 - policyDefA，并在计划定义 - initiativeDefC 下创建它，如果你决定在稍后使用类似 policyDefA 的目标为 policyDefB 创建另一个策略定义，则只需将其添加到 initiativeDefC 下，并通过这种方式来更好地跟踪它们。

   请记住，从计划定义创建计划分配后，添加到计划定义的任何新策略定义都将在该计划定义下的计划分配下自动滚动。 但是，如果为新的策略定义引入一个新参数，则需要通过编辑计划定义或分配来更新计划定义和分配。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure 策略以及我们介绍的一些关键概念，下面是建议的后续步骤：

- [分配策略定义](./assign-policy-definition.md)
- [使用 Azure CLI 分配策略定义](./assign-policy-definition-cli.md)
- [使用 PowerShell 分配策略定义](./assign-policy-definition-ps.md)
