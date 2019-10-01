---
title: Azure Monitor 警报的操作规则
description: 了解 Azure Monitor 中的操作规则是什么，以及如何配置和管理操作规则。
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 34997c130094b7e8b209b3ad3030038670d0a254
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702990"
---
# <a name="action-rules-preview"></a>操作规则（预览版）

操作规则可帮助你在任何 Azure 资源管理器范围（Azure 订阅、资源组或目标资源）定义或消除操作。 它们包含各种筛选器，可帮助你将应用范围缩减到要处理的特定警报实例子集。

## <a name="why-and-when-should-you-use-action-rules"></a>为何以及何时应使用操作规则？

### <a name="suppression-of-alerts"></a>消除警报

在许多情况下，消除警报生成的通知会有好处。 这些情况包括计划内维护时段消除警报通知，以及在非营业时间消除警报通知。 例如，由于 **ContosoVM** 正在接受计划内维护，负责 **ContosoVM** 的团队希望在本周末消除警报通知。 

尽管该团队可以手动禁用针对 **ContosoVM** 配置的每条警报规则（并在维护后重新启用），则此过程并不简单。 操作规则可以帮助你灵活配置消除时段，从而大规模地定义警报消除。 在前面的示例中，该团队可以针对 **ContosoVM** 定义一条操作规则，以便在周末消除所有警报通知。


### <a name="actions-at-scale"></a>大规模操作

尽管警报规则可帮助你定义生成警报时触发的操作组，但客户往往会在其整个操作范围内使用一个通用的操作组。 例如，负责资源组 **ContosoRG** 的团队也许可以针对 **ContosoRG** 中定义的所有警报规则定义同一个操作组。 

操作规则可以帮助简化此过程。 通过大规模定义操作，针对配置范围内生成的任何警报触发操作组。 在前面的示例中，该团队可以针对 **ContosoRG** 定义一个操作规则，用于针对其中生成的所有警报触发同一个操作组。

> [!NOTE]
> 操作规则目前不适用于 Azure 服务运行状况警报。

## <a name="configuring-an-action-rule"></a>配置操作规则

在 Azure Monitor 中的“警报”登陆页上选择“管理操作”可以访问该功能。 然后选择“操作规则(预览版)”。 在警报登陆页的仪表板中选择“操作规则(预览版)”可以访问规则。

![Azure Monitor 登陆页中的操作规则](media/alerts-action-rules/action-rules-landing-page.png)

选择“+ 新建操作规则”。 

![添加新的操作规则](media/alerts-action-rules/action-rules-new-rule.png)

或者，也可以在配置警报规则时创建操作规则。

![添加新的操作规则](media/alerts-action-rules/action-rules-alert-rule.png)

此时应会看到用于创建操作规则的流程页。 配置以下元素： 

![新建操作规则的流程](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>范围

首先选择范围（Azure 订阅、资源组或目标资源）。 还可以多重选择单个订阅中的范围组合。

![操作规则范围](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>筛选条件

此外，可以定义筛选器，以将范围进一步缩小为特定的警报子集。 

可用的筛选器包括： 

* **严重性**：用于选择一个或多个警报严重性的选项。 **严重性 = Sev1** 表示该操作规则适用于设置为 Sev1 的所有警报。
* **监视服务**：根据原始监视服务进行筛选。 此筛选器也是多选的。 例如，**监视服务 =“Application Insights”** 表示该操作规则适用于所有基于 Application Insights 的警报。
* **资源类型**：根据特定的资源类型进行筛选。 此筛选器也是多选的。 例如，**资源类型 =“虚拟机”** 表示该操作规则适用于所有虚拟机。
* **警报规则 ID**：用于使用警报规则的资源管理器 ID 筛选特定警报规则的选项。
* **监视条件**：使用“已触发”或“已解决”作为监视条件来筛选警报实例。
* **说明**：针对定义为警报规则的一部分的说明定义字符串匹配项的 regex（正则表达式）匹配。 例如， **“说明”包含“prod”** 将匹配其说明中包含字符串“prod”的所有警报。
* **警报上下文(有效负载)** ：针对警报有效负载的警报上下文字段定义字符串匹配的 regex 匹配。 例如， **“警报上下文(有效负载)”包含“computer-01”** 将匹配其负载包含字符串“computer-01”的所有警报。

这些筛选器相互结合应用。 例如，如果设置 **“资源类型”= 虚拟机**， **“严重性”= Sev0**，则只会在 VM 上筛选所有的 **Sev0** 警报。 

![操作规则筛选器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>消除或操作组配置

接下来，针对警报消除或操作组支持配置操作规则。 不能同时选择两者。 该配置将作用于与前面定义的范围和筛选器匹配的所有警报实例。

#### <a name="suppression"></a>抑制

如果选择“消除”，请配置消除操作和通知的持续时间。 选择以下选项之一：
* **从现在起(始终)** ：无限期消除所有通知。
* **在计划时间**：在有限的持续时间内消除通知。
* **重复**：按每日、每周或每月重复计划消除通知。

![操作规则 - 消除](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>操作组

如果在切换选项中选择“操作组”，请添加现有的操作组或新建一个操作组。 

> [!NOTE]
> 只能将一个操作组关联到一个操作规则。

![通过选择操作组来添加或创建新的操作规则](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>操作规则详细信息

最后，配置操作规则的以下详细信息
* 姓名
* 该规则要保存到的资源组
* 描述 

## <a name="example-scenarios"></a>示例方案

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>方案 1：根据严重性消除警报

Contoso 希望在每个周末消除其订阅 **ContosoSub** 中所有 VM 上的所有 Sev4 警报的通知。

**解决方案：** 使用以下设置创建一个操作规则：
* 范围 = **ContosoSub**
* 筛选器
    * 严重性 = **Sev4**
    * 资源类型 = **虚拟机**
* 消除重复周期设置为“每周”，并选中“星期六”和“星期日”

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>方案 2：根据警报上下文（有效负载）消除警报

Contoso 希望无限期消除针对 **ContosoSub** 中 **Computer-01** 生成的所有日志警报的通知，因为该计算机即将接受维护。

**解决方案：** 使用以下设置创建一个操作规则：
* 范围 = **ContosoSub**
* 筛选器
    * 监视服务 = **Log Analytics**
    * 警报上下文（有效负载）包含 **Computer-01**
* 消除时间设置为“从现在起(始终)”

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>方案 3：在资源组中定义的操作组

Contoso [在订阅级别定义了一个指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但是，Contoso 想要定义专用针对其资源组 **ContosoRG** 生成的警报触发的操作。

**解决方案：** 使用以下设置创建一个操作规则：
* 范围 = **ContosoRG**
* 无筛选器
* 操作组设置为 **ContosoActionGroup**

> [!NOTE]
> *在操作规则和警报规则中定义的操作组会独立运行，不会进行任何重复数据删除。* 在上述方案中，如果为警报规则定义了某个操作组，该操作组将结合操作规则中定义的操作组一起触发。 

## <a name="managing-your-action-rules"></a>管理操作规则

可以通过列表视图查看和管理操作规则：

![操作规则列表视图](media/alerts-action-rules/action-rules-list-view.png)

在此处，可以通过选中相应的复选框，大规模启用、禁用或删除操作规则。 选择某个操作规则会打开其配置页。 此页可帮助你更新该操作规则的定义，以及启用或禁用该规则。

## <a name="best-practices"></a>最佳实践

结合[“结果数”](alerts-unified-log.md)选项创建的日志警报会使用整个搜索结果生成单个警报实例（可能跨多台计算机）。 在此方案中，如果操作规则使用“警报上下文(有效负载)”筛选器，则只要有匹配项，该规则就会作用于警报实例。 在上述方案 2 中，如果生成的日志警报的搜索结果包含 **Computer-01** 和 **Computer-02**，则会消除整个通知。 完全不会为 **Computer-02** 生成通知。

![操作规则和日志警报（结果数）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要充分利用日志警报和操作规则，请使用[“指标度量”](alerts-unified-log.md)选项创建日志警报。 此选项会根据定义的组字段生成单独的警报实例。 然后，在方案 2 中，将为 **Computer-01** 和 **Computer-02** 生成单独的警报实例。 由于该方案中所述的操作规则，只会消除 **Computer-01** 的通知。 **Computer-02** 的通知会继续照常激发。

![操作规则和日志警报（结果数）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常见问题

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>在配置操作规则时，我希望看到所有可能重叠的操作规则，以避免收到重复通知。 可以做到这一点吗？

在配置操作规则时定义范围后，可以看到在同一范围重叠的操作规则列表（如果有）。 这种重叠可能属于以下情况之一：

* 完全匹配：例如，你定义的操作规则和重叠的操作规则在同一订阅中。
* 子集：例如，你定义的操作规则在某个订阅中，而重叠的操作规则在该订阅的某个资源组中。
* 超集：例如，你定义的操作规则在某个资源组中，而重叠的操作规则在包含该资源组的订阅中。
* 交集：例如，你定义的操作规则在 **VM1** 和 **VM2** 中，而重叠的操作规则在 **VM2** 和 **VM3** 中。

![重叠的操作规则](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>在配置警报规则时，能够知道是否已存在可能作用于我所定义的警报规则的任何操作规则吗？

为警报规则定义目标资源后，可以选择“操作”部分下的“查看配置的操作”，来查看在同一范围发生作用的操作列表（如果有）。 此列表是根据以下范围方案填充的：

* 完全匹配：例如，你定义的警报规则和操作规则在同一订阅中。
* 子集：例如，你定义的警报规则在某个订阅中，而操作规则在该订阅的某个资源组中。
* 超集：例如，你定义的警报规则在某个资源组中，而操作规则在包含该资源组的订阅中。
* 交集：例如，你定义的警报规则在 **VM1** 和 **VM2** 中，而操作规则在 **VM2** 和 **VM3** 中。
    
![重叠的操作规则](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>是否可以看到操作规则消除的警报？

在[警报列表页](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)中，可以选择一个名为“消除状态”的附加列。 如果消除了某个警报实例的通知，该实例会在列表中显示该状态。

![消除的警报实例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果某个操作规则包含操作组，而同一范围的另一个操作规则启用了通知消除，会发生什么情况？

在同一范围内，消除操作始终优先。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果在两个不同的操作规则中监视资源，会发生什么情况？ 我会收到一条还是两条通知？ 以此方案中的 **VM2** 为例：

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

对于 VM1 和 VM3 上的每条警报，操作组 AG1 将触发一次。 对于 **VM2** 上的每条警报，操作组 AG1 会触发两次，因为操作规则不会删除重复的操作。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果在两个不同的操作规则监视资源，其中一个规则请求操作，而另一个规则请求消除，会发生什么情况？ 以此方案中的 **VM2** 为例：

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

对于 VM1 上的每条警报，操作组 AG1 将触发一次。 对于 VM2 和 VM3 上的每条警报，将消除操作和通知。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果为调用不同操作组的同一个资源定义了警报规则和操作规则，会发生什么情况？ 以此方案中的 **VM1** 为例：

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
对于 VM1 上的每条警报，操作组 AG1 将触发一次。 每当触发警报规则“rule1”时，会一并触发 AG2。 在操作规则和警报规则中定义的操作组会独立运行，不会进行任何重复数据删除。 

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 中的警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
