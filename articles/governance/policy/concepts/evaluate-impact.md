---
title: 评估新 Azure 策略的影响
description: 了解在将新策略定义引入 Azure 环境时要遵循的过程。
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 562fa2378356ddc1eac48b6ea5c160ebf655d525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74463519"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>评估新 Azure 策略的影响

Azure Policy 是一个功能强大的工具，可用于根据企业标准和法规需求管理 Azure 资源。 当用户、过程或管道创建或者更新资源时，Azure Policy 会评审请求。 当策略定义效果为 [Append](./effects.md#deny) 或 [DeployIfNotExists](./effects.md#deployifnotexists) 时，Policy 会更改请求或对其进行补充。 当策略定义效果为 [Audit](./effects.md#audit) 或 [AuditIfNotExists](./effects.md#auditifnotexists) 时，Policy 会导致创建活动日志项。 当策略定义效果为 [Deny](./effects.md#deny) 时，Policy 会停止请求的创建或更改。

如果你确认策略已正确定义，则完全可按预期实现这些结果。 不过，在允许新策略更改或阻止工作之前，必须先验证该策略是否按意图运行。 验证必须确保只将目标资源判定为不合规，而不会在结果中错误地包含不合规的资源（称为“误报”）。__

验证新策略定义的建议方法是执行以下步骤：

- 严格定义策略
- 审核现有资源
- 审核新的或更新的资源请求
- 将策略部署到资源
- 持续监视

## <a name="tightly-define-your-policy"></a>严格定义策略

必须了解如何将业务策略实施为策略定义，以及 Azure 资源与其他 Azure 服务之间的关系。 此步骤是通过[确定要求](../tutorials/create-custom-policy-definition.md#identify-requirements)并[确定资源属性](../tutorials/create-custom-policy-definition.md#determine-resource-properties)完成的。
但是，预见业务策略狭窄定义以外的影响也很重要。 例如，策略状态是否为“所有虚拟机必须...”？ 使用 VM 的其他 Azure 服务（例如 HDInsight 或 AKS）受到怎样的影响？ 定义策略时，必须考虑此策略如何影响其他服务使用的资源。

出于此原因，应严格定义策略定义，并尽量将其重点放在需要评估合规性的资源和属性上。

## <a name="audit-existing-resources"></a>审核现有资源

在寻求使用新的策略定义管理新的或更新的资源之前，最好是了解该定义如何评估有限的现有资源子集，例如某个测试资源组。 在策略分配上使用_禁用_（DoNot强制）[强制模式](./assignment-structure.md#enforcement-mode)
，以防止创建触发或创建活动日志条目[。](./effects.md)

此步骤使你有机会在不影响工作流的情况下，评估对现有资源使用新策略后的合规性结果。 检查是否未将任何合规的资源标记为不合规（误报），并且已正确标记预期不合规的所有资源。__
按预期验证资源的初始子集后，逐渐将评估扩展到所有现有资源。

以这种方式评估现有资源还有机会在全面实施新策略之前修正不合规的资源。 如果策略定义效果为 _DeployIfNotExists_，可以手动或通过[修正任务](../how-to/remediate-resources.md)完成此清理过程。

## <a name="audit-new-or-updated-resources"></a>审核新的或更新的资源

验证新策略定义可针对现有资源正确报告信息后，可以在创建或更新资源时查看该策略的影响。 如果策略定义支持效果参数化，请使用[审核](./effects.md#audit)。 此配置可让你监视资源的创建和更新，以确定新策略定义是否在 Azure 活动日志中对某个不合规的资源触发一个项，且不影响现有的工作或请求。

建议同时更新并创建与策略定义匹配的新资源，以确定 _Audit_ 效果是否按预期正常触发。 请当心触发 _Audit_ 效果的新策略定义不应该影响到的资源请求。
这些受影响资源是另一个误报示例，在全面实施该策略定义之前必须先将其修复。__

如果在此测试阶段策略定义发生更改，我们建议重新开始验证过程并审核现有资源。 在新的或更新的资源中对误报的策略定义进行更改也有可能会影响现有资源。__

## <a name="deploy-your-policy-to-resources"></a>将策略部署到资源

在现有资源以及新的或更新的资源请求中完成新策略定义的验证后，开始执行实施该策略的过程。 建议先为所有资源中的一部分资源（例如某个资源组）创建新策略定义的策略分配。 验证初始部署后，逐渐将策略范围扩展到更广泛的级别，例如订阅和管理组。 可以通过删除分配，然后在目标范围创建新的分配来实现这种扩展，直至已将此策略分配到新策略定义预期涵盖的整个资源范围为止。

在实施过程中，如果发现了应从新策略定义中排除的资源，请按以下方式之一进行处理：

- 更新策略定义，使之更明确，以减轻意外的影响
- 更改策略分配的范围（通过删除再新建分配）
- 将资源组添加到策略分配的排除列表

对范围（级别或排除项）做出的任何更改应受到全面的验证，并传达给安全与合规组织，以确保覆盖范围没有余隙。

## <a name="monitor-your-policy-and-compliance"></a>监视策略与合规性

实施和分配策略定义并非最后一步。 应根据新的策略定义持续监视资源的[合规性](../how-to/get-compliance-data.md)级别，并设置适当的 [Azure Monitor 警报和通知](../../../azure-monitor/platform/alerts-overview.md)，以便在识别出不合规的设备时收到通知。 此外，建议按计划评估策略定义和相关分配，以验证策略定义是否符合企业策略与合规需求。 应该删除不再需要的策略。 此外，随着底层 Azure 资源的不断演变，需要不时地更新策略，并添加新的属性和功能。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解[策略分配结构](./assignment-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修复不合规资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。