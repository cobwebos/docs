---
title: Azure 策略和 Azure 蓝图的安全遵从性
description: 确保符合性并强制实施适用于澳大利亚政府机构的 Azure 策略和 Azure 蓝图的安全性, 因为它与一种
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571739"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Azure 策略和 Azure 蓝图的安全遵从性

在 IT 环境中强制实施管理的挑战, 无论是本地、云本机还是混合环境, 都适用于所有组织。 为了确保您的 Microsoft Azure 环境符合设计、法规和安全要求, 需要具备可靠的技术管理框架。

对于澳大利亚政府机构, 他们需要考虑的是在 "[澳大利亚网络安全中心" (ACSC) 信息安全手册](https://acsc.gov.au/infosec/ism/index.htm)(ISM) 中评估风险时要考虑的关键控制措施。 ISM 中详细介绍的大部分控件都需要有效地管理和实施技术调控。 重要的是, 可以使用适当的工具在环境中评估和实施配置。

Microsoft Azure 提供了两个免费服务来帮助解决这些难题: Azure 策略和 Azure 蓝图。

## <a name="azure-policy"></a>Azure Policy

使用 Azure 策略可以应用组织 IT 治理的技术元素。 Azure 策略包含一个不断增长的内置策略库。 每个策略强制实施针对目标 Azure 资源的规则和影响。

将策略分配给资源后, 就可以评估该策略的总体符合性, 并根据需要进行修正。

利用此内置 Azure 策略库, 组织可以快速地强制执行 ACSC ISM 中的控件类型。 控件的示例包括:

* 监视虚拟机是否缺少系统更新
* 审核具有多重身份验证的提升权限的帐户
* 标识未加密的 SQL 数据库
* 监视使用自定义 Azure 基于角色的访问控制 (RBAC)
* 限制可在其中创建资源的 Azure 区域

如果内置 Azure 策略定义不满足监管或法规控制要求, 则可以创建和分配自定义定义。 所有 Azure 策略定义均以 JSON 定义, 并遵循标准[定义结构](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)。 还可以复制现有的 Azure 策略定义, 并将其用于构成自定义策略定义的基础。

将单个 Azure 策略分配给资源 (尤其是在复杂的环境中, 或者在具有严格的法规要求的环境中) 时, 可能会为管理员带来大的开销。 为了帮助解决这些难题, 可以将一组 Azure 策略组合在一起以形成 Azure 策略计划。 策略计划用于合并相关的 Azure 策略, 在将其作为一个组一起应用时, 构成特定安全性或符合性目标的基础。 Microsoft 正在添加内置的 Azure 策略计划定义, 包括旨在满足特定法规要求的定义:

![法规遵从性策略计划](media/regulatory-initiatives.png)

所有 Azure 策略和计划都分配给分配作用域。 此作用域是在 Azure 订阅、Azure 管理组或 Azure 资源组级别定义的。 分配所需的 Azure 策略或策略计划后, 组织将能够对所有新创建的 Azure 资源强制实施配置要求。

分配新的 Azure 策略或计划不会影响现有的 Azure 资源。 Azure 策略可以是;不过, 使组织能够查看现有 Azure 资源的符合性。 已标识为不符合的任何资源都可以根据组织的需要进行修正

### <a name="azure-policy-and-initiatives-in-action"></a>Azure 策略和正在进行的行动

可以在 "Azure 门户的" 策略 "部分中的" 定义 "节点下找到可用的内置 Azure 策略和计划定义:

![内置 Azure 策略定义](media/policy-definitions.png)

使用内置定义的库, 可以快速搜索符合组织要求的策略, 查看策略定义, 并将策略分配给相应的资源。 例如, ISM 需要为所有特权用户提供多重身份验证 (MFA), 并为有权访问重要数据存储库的所有用户提供多重身份验证 (MFA)。 在 Azure 策略中, 可以在 Azure 策略定义之间搜索 "MFA":

![Azure MFA 策略](media/mfa-policies.png)

标识适当的策略后, 将策略分配到所需的作用域。 如果没有满足你的要求的内置策略, 你可以复制现有策略并进行所需的更改:

![复制现有 Azure 策略](media/duplicate-policy.png)

Microsoft 还在[GitHub](https://github.com/Azure/azure-policy)上提供了 Azure 策略示例的集合, 作为用于构建自定义 Azure 策略的 "快速入门"。 这些策略示例可直接复制到 Azure 门户中的 Azure 策略编辑器。

创建 Azure 策略计划时, 可以将可用策略定义的列表 (内置和自定义) 排序, 并添加所需的定义。

例如, 可以在可用的 Azure 策略定义列表中搜索与 Windows 虚拟机相关的所有策略。 然后, 将这些定义用于旨在强制实施推荐的虚拟机强化做法的计划:

![Azure 策略列表](media/initiative-definitions.png)

将 Azure 策略或策略计划分配给分配作用域时, 可以通过排除 Azure 管理组或 Azure 资源组, 从策略的影响中排除 Azure 资源。

### <a name="real-time-enforcement-and-compliance-assessment"></a>实时强制和符合性评估

满足以下条件时, 将会对范围内的 Azure 资源执行 azure 策略符合性扫描:

* 分配 Azure 策略或 Azure 策略计划时
* 如果更改了现有 Azure 策略或计划的作用域
* 按需通过 API, 每小时最多可扫描10次
* 每24小时一次-默认行为

对资源进行更改后, 对单个 Azure 资源执行策略符合性扫描的时间为15分钟。

可以通过 "策略符合性仪表板" 在 Azure 门户中查看资源的 Azure 策略符合性概述:

![Azure 策略符合性分数](media/simple-compliance.png)

总体资源相容性百分比图是所有分配的 Azure 策略的所有范围内部署资源的符合性的聚合。 这样, 你便可以确定不符合的环境中的资源, 并设计该计划以最好地修正这些资源。

策略相容性仪表板还包括每个资源的更改历史记录。 如果将某个资源标识为不再符合分配的策略, 而未启用自动修正, 则可以查看执行更改的人员、更改的内容以及更改该资源的时间。

## <a name="azure-blueprints"></a>Azure 蓝图

Azure 蓝图通过将其与结合使用来扩展 Azure 策略的功能:

* Azure RBAC
* Azure 资源组
* [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

蓝图允许创建从资源管理器模板部署 Azure 资源的环境设计、配置 RBAC, 并通过分配 Azure 策略来强制和审核配置。 蓝图窗体可编辑和 redeployable 环境模板。 创建蓝图后, 可以将其分配给 Azure 订阅。 分配后, 将创建蓝图中定义的所有 Azure 资源并应用 Azure 策略。 可以从 Azure 门户中的 Azure 蓝图控制台监视 Azure 蓝图中定义的资源的部署和配置。

已编辑的 Azure 蓝图必须在 Azure 门户中重新发布。 每次重新发布蓝图时, 蓝图的版本号都会增加。 版本号允许确定已将蓝图的特定版本部署到组织的 Azure 订阅。 如果需要, 可以将当前分配的蓝图版本更新到最新版本。

使用 Azure 蓝图部署的资源可以在部署时配置[Azure 资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。 资源锁阻止意外修改或删除资源。

Microsoft 正在开发各种行业和法规要求的 Azure 蓝图模板。 可用 Azure 蓝图定义的当前库可以在服务信任门户中的 Azure 门户或[Azure 安全性与合规性蓝图](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/)页中查看。

### <a name="azure-blueprint-artifacts"></a>Azure 蓝图项目

若要创建 Azure 蓝图, 可以从空白蓝图模板开始, 或使用现有的示例蓝图之一作为起点。 你可以将项目添加到将配置为部署的一部分的蓝图:

![Azure 蓝图项目](media/blueprint-artifacts.png)

这些项目可以包括 Azure 资源组和资源, 以及用于强制执行环境所需的配置以符合你的法规要求的配置, 例如, ISM 控件用于系统强化。

还可以为这些项目中的每个项目配置参数。 当蓝图分配到 Azure 订阅并进行部署时, 将提供这些值。 参数允许创建单个蓝图并用于将资源部署到不同的环境中, 而无需编辑基础蓝图。

Microsoft 正在开发 Azure PowerShell 和 CLI cmdlet 来创建和管理 Azure 蓝图, 目的是通过 CI/CD 管道将蓝图维护和部署。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure 策略和 Azure 蓝图来强制实施调控和安全性。 现在, 你已从较高层次公开, 接下来了解如何更详细地使用每个服务:

* [Azure 策略概述](https://docs.microsoft.com/azure/governance/policy/overview)
* [Azure 蓝图概述](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Azure 策略示例存储库](https://github.com/Azure/azure-policy)
* [Azure 策略定义结构](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure 策略影响](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
