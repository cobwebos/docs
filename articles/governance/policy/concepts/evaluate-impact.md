---
title: 评估新 Azure Policy 定义的影响
description: 了解将新策略定义引入 Azure 环境时要遵循的过程。
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 9d73d703c38dce1335a471bfad9171d8b30a83c5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873861"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>评估新 Azure Policy 定义的影响

Azure Policy 是一种功能强大的工具，用于管理 Azure 资源以符合业务标准并满足合规性需求。 当用户、过程或管道创建或更新资源时，Azure Policy 会评审请求。 当策略定义效果为 [Modify](./effects.md#modify)、 [Append](./effects.md#deny) 或 [DeployIfNotExists](./effects.md#deployifnotexists)时，策略将更改请求或将其添加到其中。 当策略定义效果为 [Audit](./effects.md#audit) 或 [AuditIfNotExists](./effects.md#auditifnotexists)时，策略将导致为新资源和更新的资源创建活动日志项。 当策略定义效果是 [Deny](./effects.md#deny) 时，Policy 会停止创建或更改请求。

如果你知道已正确定义了策略，则这些结果将完全符合预期。 不过，需要先验证新策略按预期方式工作，然后才允许它更改或阻止工作，这十分重要。 验证必须确保仅将预期资源确定为不合规，并且不会在结果中错误地包含任何合规资源（称为假正）。

验证新策略定义的建议方法是执行以下步骤：

- 严格定义策略
- 审核现有资源
- 审核新的或更新的资源请求
- 将策略部署到资源
- 持续监视

## <a name="tightly-define-your-policy"></a>严格定义策略

了解如何将业务策略作为策略定义来实现，以及 Azure 资源与其他 Azure 服务的关系十分重要。 此步骤通过[识别要求](../tutorials/create-custom-policy-definition.md#identify-requirements)以及[确定资源属性](../tutorials/create-custom-policy-definition.md#determine-resource-properties)来完成。
不过，超越业务策略的狭隘定义也十分重要。 例如，你的策略状态是否为“所有虚拟机都必须...”？ 使用 VM 的其他 Azure 服务（如 HDInsight 或 AKS）怎么处理？ 定义策略时，必须考虑此策略如何影响其他服务所使用的资源。

出于此原因，策略定义应严格进行定义，并尽可能侧重于需要评估合规性的资源和属性。

## <a name="audit-existing-resources"></a>审核现有资源

在希望使用新策略定义管理新的或更新的资源之前，最好了解如何评估现有资源的有限子集（如测试资源组）。 对策略分配使用“已禁用”(DoNotEnforce) 的[强制模式](./assignment-structure.md#enforcement-mode)，以防止触发[效果](./effects.md)或创建活动日志条目。

此步骤使你可以在不影响工作流的情况下评估新策略对现有资源的合规性结果。 检查是否没有合规资源标记为不合规（假正），并且是否正确标记了所有预期为不合规的资源。
资源的初始子集经验证符合预期之后，可慢慢将评估扩展到所有现有资源。

以这种方式评估现有资源还可以有机会在完整实现新策略之前修正不合规资源。 此清理可以手动完成，或通过[修正任务](../how-to/remediate-resources.md)完成（如果策略定义效果是 DeployIfNotExists）。

## <a name="audit-new-or-updated-resources"></a>审核新的或更新的资源

验证新策略定义可对现有资源正确报告后，便可以查看策略在创建或更新资源时的影响。 如果策略定义支持效果参数化，请使用 [Audit](./effects.md#audit)。 此配置使你可以监视资源的创建和更新，以查看新策略定义是否在 Azure 活动日志中为不合规资源触发条目，而不影响现有工作或请求。

建议更新并创建与策略定义相匹配的新资源，以查看是否在预期时正确触发 Audit 效果。 请注意本不应受触发 Audit 效果的新策略定义影响的资源请求。
这些受影响的资源是假正的另一个示例，在进行完整实现之前，必须在策略定义中得到修复。

如果在此测试阶段更改了策略定义，则建议从现有资源的审核重新开始验证过程。 在新的或更新的资源上针对假正的策略定义更改也可能会影响现有资源。

## <a name="deploy-your-policy-to-resources"></a>将策略部署到资源

通过现有资源以及新的或更新的资源请求完成对新策略定义的验证之后，可开始实现策略的过程。 建议为新的策略定义创建策略分配，将它先分配给所有资源的子集（如资源组）。 验证初始部署之后，将策略的范围扩展到越来越广泛的级别，如订阅和管理组。 此扩展通过以下方式实现：删除分配并在目标范围上创建新分配，直到分配给新策略定义旨在涵盖的资源的完整范围为止。

在推出过程中，如果找到应从新策略定义中排除的资源，请按以下方式之一进行处理：

- 更新策略定义以更明确地减少意外影响
- 更改策略分配的范围（通过删除并创建新分配）
- 将资源组添加到策略分配的排除列表中

对范围（级别或排除）进行的任何更改都应进行完整验证，并与安全和合规性组织进行沟通，以确保覆盖范围中没有缺口。

## <a name="monitor-your-policy-and-compliance"></a>监视策略和合规性

实现并分配策略定义并不是最后一步。 持续监视资源对新策略定义的[合规性](../how-to/get-compliance-data.md)级别，并设置适当的 [Azure Monitor 警报和通知](../../../azure-monitor/platform/alerts-overview.md)以用于识别出不合规设备的情况。 还建议按计划评估策略定义和相关分配，以验证策略定义是否满足业务策略和合规性需求。 如果不再需要，则应该删除策略。 随着底层 Azure 资源发展，以及添加新属性和功能，策略也需要时常更新。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。