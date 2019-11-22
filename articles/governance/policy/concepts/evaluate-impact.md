---
title: 评估新 Azure 策略的影响
description: 了解将新策略引入 Azure 环境时要遵循的过程。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: e39183b13d2b3cf8c7527f9372879372b2123648
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279424"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>评估新 Azure 策略的影响

Azure 策略是一种功能强大的工具，可用于管理 Azure 资源与业务标准并满足合规性需求。 当用户、进程或管道创建或更新资源时，Azure 策略会检查请求。 当策略定义效果为[Append](./effects.md#deny)或[DeployIfNotExists](./effects.md#deployifnotexists)时，策略将更改请求或将其添加到其中。 当策略定义效果为[Audit](./effects.md#audit)或[AuditIfNotExists](./effects.md#auditifnotexists)时，策略将导致创建活动日志条目。 当策略定义效果为 "[拒绝](./effects.md#deny)" 时，策略将停止创建或更改请求。

如果你知道已正确定义策略，则这些结果将完全按预期方式进行。 不过，验证新策略的工作方式非常重要，因为它允许更改或阻止工作。 验证必须确保仅将预期资源确定为不合规，并且不会在结果中错误地包含符合性资源（称为_误报_）。

验证新策略定义的建议方法是执行以下步骤：

- 严格定义策略
- 审核现有资源
- 审核新的或更新的资源请求
- 将策略部署到资源
- 持续监视

## <a name="tightly-define-your-policy"></a>严格定义策略

务必了解如何实现业务策略，作为策略定义以及 Azure 资源与其他 Azure 服务之间的关系。 此步骤通过[标识要求](../tutorials/create-custom-policy-definition.md#identify-requirements)并[确定资源属性](../tutorials/create-custom-policy-definition.md#determine-resource-properties)来完成。
但除了企业策略的窄定义外，还必须了解这一点。 你的策略状态是否为 "所有虚拟机必须 ..."？ 使用 Azure 的其他 Azure 服务（例如 HDInsight 或 AKS）有什么作用？ 定义策略时，必须考虑此策略如何影响其他服务使用的资源。

出于此原因，应严格定义策略定义，并将其重点放在需要评估符合性的资源和属性上。

## <a name="audit-existing-resources"></a>审核现有资源

在希望使用新策略定义管理新的或更新的资源之前，最好是了解它是如何评估现有资源（如测试资源组）的有限子集的。 在策略分配中使用[强制模式](./assignment-structure.md#enforcement-mode)
_禁用_（DoNotEnforce），以防止创建触发或活动日志条目的[影响](./effects.md)。

此步骤使你可以在不影响工作流的情况下评估新策略对现有资源的符合性结果。 检查是否未将符合标准的资源标记为不符合（_误报_），并检查是否所有预期为不符合的资源均已正确标记。
资源的初始子集按预期进行验证后，慢慢地将计算展开为所有现有资源。

以这种方式评估现有资源还可以在完整实现新策略之前修正不合规的资源。 如果策略定义效果为_DeployIfNotExists_，则可以手动或通过[修正任务](../how-to/remediate-resources.md)来执行此清理。

## <a name="audit-new-or-updated-resources"></a>审核新资源或更新的资源

验证新策略定义在现有资源上正确报告后，可以在创建或更新资源时查看策略的影响。 如果策略定义支持效果参数化，请使用[审核](./effects.md#audit)。 此配置允许你监视资源的创建和更新，以查看新策略定义是否在 Azure 活动日志中为不符合的资源触发一个条目，而不影响现有工作或请求。

建议更新并创建与策略定义相匹配的新资源，以查看在预期时正确触发了_审核_效果。 适用于资源请求的 lookout，这些请求不应受到触发_审核_效果的新策略定义的影响。
这些受影响的资源是_误报_的另一个示例，在完全实现之前必须在策略定义中修复。

如果在此测试阶段更改了策略定义，则建议你开始验证过程，并审核现有资源。 对新资源或更新资源的_误报_策略定义的更改也可能会影响现有资源。

## <a name="deploy-your-policy-to-resources"></a>将策略部署到资源

完成对新策略定义的验证以及现有资源和新的或更新的资源请求后，开始执行该策略的过程。 建议为新的策略定义创建策略分配，使其成为所有资源的子集，如资源组。 验证初始部署后，将策略的作用域扩展到更广泛、更广泛的级别，如订阅和管理组。 此扩展是通过以下方式实现的：删除分配并在目标范围内创建新的扩展，直到将其分配给新策略定义所涵盖的资源的完整范围为止。

在推出过程中，如果资源所在的资源应该从新策略定义中排除，请按以下方式之一进行处理：

- 更新策略定义以更明确地减少意外的影响
- 更改策略分配的范围（通过删除和创建新分配）
- 将资源组添加到策略分配的排除列表中

对范围（级别或排除项）所做的任何更改都应进行完全验证并与您的安全和合规性组织通信，以确保不会有覆盖范围。

## <a name="monitor-your-policy-and-compliance"></a>监视策略和符合性

实现并分配策略定义不是最后一步。 连续监视资源的[符合性](../how-to/get-compliance-data.md)级别，并设置适当的[Azure Monitor 警报和通知](../../../azure-monitor/platform/alerts-overview.md)，以确定何时识别不符合的设备。 还建议按计划评估策略定义和相关分配，以验证策略定义是否满足业务策略和合规性需求。 如果不再需要策略，则应该将其删除。 随着底层 Azure 资源的发展，策略也需要随时更新，并添加新的属性和功能。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。