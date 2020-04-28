---
title: 将策略设计为代码工作流
description: 了解如何设计工作流，以将 Azure Policy 定义部署为代码，并自动验证资源。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: fd77fdd4011c3e1e83f8dfa9f30045bb72881c25
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187726"
---
# <a name="design-policy-as-code-workflows"></a>将策略设计为代码工作流

在使用云监管的历程中，你希望将通过 Azure 门户或各种 SDK 手动管理每个策略定义的做法，过渡到可在整个企业中运用的、更易于管理且可重用的方法。 在云中大规模管理系统的两种主流方法是：

- 基础结构即代码：将定义环境的内容（从资源管理器模板，到 Azure Policy 定义，再到 Azure 蓝图的所有内容）视为源代码的做法。
- DevOps：人员、过程和产品的联合，可以实现向最终用户持续提供价值。

策略即代码是这些思路的组合。 从根本上讲，此方法是在源代码管理中保留策略定义，每当发生更改，就会对这种更改进行测试和验证。 但是，不应将此方法视为对基础结构即代码或 DevOps 策略参与的延伸。

此外，验证步骤应是其他持续集成或持续部署工作流的一个组成部分。 示例包括部署应用程序环境或虚拟基础结构。 如果将 Azure Policy 验证用作生成和部署过程的前期组成部分，应用程序和运营团队可以在尝试部署到生产环境之前，提前很长时间发现他们的更改是否不会造成投诉。

## <a name="workflow-overview"></a>工作流概述

策略即代码的一般建议工作流如下图所示：

:::image type="content" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="策略即代码工作流概述" border="false":::

### <a name="create-and-update-policy-definitions"></a>创建和更新策略定义

策略定义是使用 JSON 创建的，存储在源代码管理中。 每个策略有自身的文件集（例如参数、规则和环境参数），这些文件应存储在同一文件夹中。 以下结构是在源代码管理中保留策略定义的建议方式。

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

添加新策略或更新现有的策略时，工作流会自动更新 Azure 中的策略定义。 新的或已更新的策略定义将在后面的步骤中测试。

### <a name="create-and-update-initiative-definitions"></a>创建和更新计划定义

同样，计划也有自身的 JSON 文件和相关文件，这些文件应存储在同一文件夹中。 计划定义要求策略定义预先存在，因此，在源代码管理中更新策略的源，然后在 Azure 中进行更新之前，无法创建或更新计划定义。 以下结构是在源代码管理中保留计划定义的建议方式。

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

与策略定义一样，在添加新的或更新现有的计划时，工作流会自动更新 Azure 中的计划定义。 新的或已更新的计划定义将在后面的步骤中测试。

### <a name="test-and-validate-the-updated-definition"></a>测试和验证更新的定义

自动化过程提取新建或更新的策略定义或者计划定义，并对 Azure 中的对象进行更新后，可以开始测试所做的更改。 然后，应将这些定义所属的策略或计划，分配到离生产环境最远的环境中的资源。 此环境通常是“开发”环境。 

分配应该对 [enforcementMode](./assignment-structure.md#enforcement-mode) 使用 _disabled_ 值，以便不会阻止资源的创建和更新，但仍会审核现有资源是否符合更新的策略定义。 即使使用 enforcementMode，我们也建议分配范围是专门用于验证策略的资源组或订阅。

> [!NOTE]
> 尽管强制模式非常有用，但它并不能取代在各种条件下对策略定义进行全面测试的做法。 应针对 `PUT` 和 `PATCH` REST API 调用、合规和不合规的资源以及极端情况（例如资源中缺少属性）测试策略定义。

部署分配后，使用 Policy SDK [获取新分配的合规性数据](../how-to/get-compliance-data.md)。 用于测试策略和分配的环境应包含合规和不合规的资源。 与针对代码的合理单元测试一样，需要测试资源是否符合预期，同时不会出现误报或漏报。 如果仅针对预期结果进行测试和验证，则策略可能会产生意外且不可识别的影响。 有关详细信息，请参阅[评估新的 Azure 策略定义的影响](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>启用修正任务

如果分配验证符合预期，下一步是验证修正措施。
使用 [deployIfNotExists](./effects.md#deployifnotexists) 或 [modify](./effects.md#modify) 的策略可以转化成修正任务，纠正资源的不合规状态。

执行此任务的第一步是向策略分配授予策略定义中定义的角色分配。 此角色分配为策略分配托管标识提供足够的权限，让其做出所需的更改来使资源合规。

策略分配获得适当的权限后，会使用 Policy SDK 针对一组已知不合规的资源触发修正任务。 在继续之前，应针对这些修正任务完成三项测试：

- 验证修正任务是否已成功完成
- 运行策略评估，确定策略合规性结果是否已按预期更新
- 直接对资源运行环境单元测试，验证其属性是否已更改

直接测试更新的策略评估结果和环境可以确认，修正任务是否按预期做出了更改，以及策略定义是否按预期发生了合规性更改。

### <a name="update-to-enforced-assignments"></a>对强制分配进行更新

完成所有验证门限后，将分配更新为对 **enforcementMode** 使用 _enabled_ 值。 最初应在远离生产环境的同一环境中进行此项更改。 一旦验证该环境按预期方式工作，就应该指定新的更改范围，以逐步将后面的各个环境包含在内，直到该策略已部署到生产资源为止。

## <a name="process-integrated-evaluations"></a>集成过程的评估

策略即代码的一般工作流用于在环境中大规模开发和部署策略与计划。 但是，对于在 Azure 中部署或创建资源的任何工作流（例如部署应用程序，或运行资源管理器模板创建基础结构），策略评估应是部署过程的一部分。

在这种情况下，在测试订阅或资源组中完成应用程序或基础结构的部署后，应在该范围执行策略评估，以检查所有现有策略和计划的验证结果。 尽管此类环境中可能采用 **enforcementMode** _disabled_ 配置，但提前知道某个应用程序或基础结构部署是否违反策略定义会很有作用。 因此，此策略评估应是这些工作流中的一个步骤，并使得创建不合规资源的部署失败。

## <a name="review"></a>审阅

本文介绍了策略即代码的一般工作流，以及在哪种情况下策略评估应是其他部署工作流的一部分。 此工作流可在支持脚本化步骤和基于触发器的自动化的任何环境中使用。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。