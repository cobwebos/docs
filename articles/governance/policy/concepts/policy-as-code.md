---
title: 作为代码工作流的设计策略
description: 了解如何设计工作流以将 Azure 策略定义部署为代码并自动验证资源。
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e3fcb9996266af7e952538c7c92c665929bb9492
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518692"
---
# <a name="design-policy-as-code-workflows"></a>作为代码工作流的设计策略

当你使用云监管进行旅程时，需要从手动管理 Azure 门户中的每个策略定义，或通过各种 Sdk 进行切换，以便在企业范围内更易于管理和重复使用。 在云中大规模管理系统的两个主要方法是：

- 基础结构即代码：将定义你的环境的内容（从资源管理器模板到 azure 的所有策略定义）作为源代码进行处理的做法。
- DevOps：人员、流程和产品的联合，使最终用户能够持续交付价值。

作为代码的策略是这些观点的组合。 实质上，在源代码管理中保留策略定义，并在每次进行更改时，测试并验证该更改。 但是，这不应是作为代码或 DevOps 的基础结构的策略的范围。

验证步骤还应是其他持续集成或持续部署工作流的组件。 示例包括部署应用程序环境或虚拟基础结构。 通过使 Azure 策略验证成为生成和部署过程的早期组件，应用程序和运营团队会发现他们的更改是否是不投诉的，长时间之前太晚，他们正在尝试在生产环境中进行部署。

## <a name="workflow-overview"></a>工作流概述

作为代码的策略的建议常规工作流如下图所示：

![作为代码工作流概述的策略](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>创建和更新策略定义

使用 JSON 创建策略定义，并将其存储在源代码管理中。 每个策略都有自己的一组文件，如参数、规则和环境参数，它们应存储在同一文件夹中。 下面的结构是在源代码管理中保留策略定义的建议方法。

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

当添加新策略或更新现有策略时，工作流会自动更新 Azure 中的策略定义。 新的或更新的策略定义的测试将在后面的步骤中进行。

### <a name="create-and-update-initiative-definitions"></a>创建和更新计划定义

同样，计划还应将自己的 JSON 文件和相关文件存储在同一文件夹中。 计划定义需要策略定义已存在，因此在源代码管理中更新该策略的源，然后在 Azure 中更新之后，才能创建或更新该策略定义。 下面的结构是在源代码管理中保留计划定义的建议方法：

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

与策略定义一样，添加或更新现有计划时，工作流会自动更新 Azure 中的计划定义。 新的或更新的计划定义的测试将在后面的步骤中进行。

### <a name="test-and-validate-the-updated-definition"></a>测试并验证更新的定义

一旦自动执行新创建或更新的策略或计划定义并对 Azure 中的对象进行更新，就可以测试所做的更改。 然后，应将策略或其所属的计划分配给离生产最远的环境中的资源。 此环境通常是_开发_环境。

分配应使用_已禁用_的[enforcementMode](./assignment-structure.md#enforcement-mode) ，这样就不会阻止资源的创建和更新，但仍会对现有资源进行审核，以满足更新的策略定义。 即使使用 enforcementMode，也建议分配范围是专门用于验证策略的资源组或订阅。

> [!NOTE]
> 尽管强制模式非常有用，但它并不是在各种情况下对策略定义进行全面测试的替代方法。 应使用 `PUT` 和 `PATCH` REST API 的调用、符合和不符合的资源和边缘事例（如资源中缺少的属性）对策略定义进行测试。

部署分配后，请使用策略 SDK 获取新分配的[符合性数据](../how-to/get-compliance-data.md)。 用于测试策略和分配的环境应同时具有相容和不符合的资源。 与适用于代码的良好单元测试一样，您需要测试资源是否按预期进行，并且也没有误报或假负。 如果仅测试并验证所需的内容，则可能会对策略产生意外和不可识别的影响。 有关详细信息，请参阅[评估新 Azure 策略的影响](./evaluate-impact.md)。

### <a name="enable-remediation-tasks"></a>启用修正任务

如果对分配的验证达到预期，下一步是验证修正。
使用 " [deployIfNotExists](./effects.md#deployifnotexists) " 或 "[修改](./effects.md#modify)" 的策略可能会被转换为修正任务并从不符合状态更正资源。

执行此操作的第一步是向策略分配授予在策略定义中定义的角色分配。 此角色分配为策略分配管理的标识提供了足够的权限来进行所需的更改，以使资源符合。

策略分配具有适当的权限后，请使用策略 SDK 针对已知不合规的一组资源触发补救任务。 在继续之前，应针对这些修正任务完成三个测试：

- 验证修正任务是否已成功完成
- 运行策略评估以查看策略符合性结果是否按预期进行更新
- 直接对资源运行环境单元测试，以验证它们的属性是否已更改

同时测试更新后的策略评估结果和环境会直接提供确认：修正任务是否更改了预期，以及策略定义是否按预期方式更改了符合性更改。

### <a name="update-to-enforced-assignments"></a>更新为强制分配

所有验证入口都完成后，将分配更新为使用_已启用_的**enforcementMode** 。 此项更改最初应在与生产环境更远的环境中进行。 一旦将该环境验证为按预期方式工作，则在将该策略部署到生产资源之前，应将更改的范围限定为包括下一个环境等。

## <a name="process-integrated-evaluations"></a>流程集成评估

作为代码的策略的一般工作流是指在大规模环境中开发和部署策略和计划。 但是，对于在 Azure 中部署或创建资源的任何工作流（例如部署应用程序或运行资源管理器模板来创建基础结构），策略评估都应该是部署过程的一部分。

在这些情况下，在应用程序或基础结构部署对测试订阅或资源组进行部署后，应针对所有现有策略和计划验证此范围内的策略评估。 尽管可能在此类环境中将其配置为_禁用_ **enforcementMode** ，但在应用程序或基础结构部署的早期阶段，如果应用程序或基础结构部署违反了策略定义，则会很有帮助。 因此，此策略评估应为这些工作流中的一个步骤，并使创建不符合资源的部署失败。

## <a name="review"></a>审阅

本文介绍策略作为代码的常规工作流，还介绍了策略评估应该是其他部署工作流的一部分。 此工作流可用于支持基于触发器的脚本化步骤和自动化的任何环境。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/getting-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。