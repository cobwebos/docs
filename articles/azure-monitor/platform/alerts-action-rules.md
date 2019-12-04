---
title: Azure Monitor 警报的操作规则
description: 了解 Azure Monitor 中的操作规则以及如何配置和管理它们。
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 04/25/2019
ms.openlocfilehash: e9de7a1fe4cee16cd1d22ba764ab9eccdf3979fd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767681"
---
# <a name="action-rules-preview"></a>操作规则（预览）

操作规则可帮助你在任何 Azure 资源管理器范围（Azure 订阅、资源组或目标资源）中定义或取消操作。 它们具有各种筛选器，可帮助您缩小要执行操作的警报实例的特定子集。

## <a name="why-and-when-should-you-use-action-rules"></a>为什么应使用操作规则？

### <a name="suppression-of-alerts"></a>禁止显示警报

在许多情况下，禁止显示警报生成的通知很有用。 这些方案的范围包括在计划内维护时段内禁止显示，在非工作时间内抑制。 例如，负责**ContosoVM**的团队希望在即将到来的周末禁止警报通知，因为**ContosoVM**正在进行计划内维护。 

尽管团队可以禁用在**ContosoVM**上手动配置的每个警报规则（并在维护后再次启用它），但这并不是一个简单的过程。 操作规则可帮助你按比例定义警报抑制，并可灵活地配置抑制时间段。 在上面的示例中，团队可以在**ContosoVM**上定义一个操作规则，以便取消周末的所有警报通知。


### <a name="actions-at-scale"></a>大规模操作

尽管警报规则可帮助你定义在警报生成时触发的操作组，但客户在其操作范围内通常具有一个公共操作组。 例如，负责资源组**ContosoRG**的团队可能会为在**ContosoRG**中定义的所有警报规则定义相同的操作组。 

操作规则可帮助您简化此过程。 通过定义大规模的操作，可以为在已配置作用域上生成的任何警报触发操作组。 在上面的示例中，团队可以在**ContosoRG**上定义一个操作规则，该操作规则将为在其中生成的所有警报触发相同的操作组。

> [!NOTE]
> 操作规则当前不适用于 Azure 服务运行状况警报。

## <a name="configuring-an-action-rule"></a>配置操作规则

可以通过从 Azure Monitor 中的 "**警报**" 登陆页中选择 "**管理操作**" 来访问此功能。 然后选择 "**操作规则（预览版）** "。 可以通过从 "警报" 登陆页的 "仪表板" 中选择 "**操作规则（预览版）** " 来访问规则。

![Azure Monitor 登陆页中的操作规则](media/alerts-action-rules/action-rules-landing-page.png)

选择 " **+ 新建操作规则**"。 

![添加新操作规则](media/alerts-action-rules/action-rules-new-rule.png)

或者，你可以在配置警报规则时创建操作规则。

![添加新操作规则](media/alerts-action-rules/action-rules-alert-rule.png)

现在应会看到用于创建操作规则的 "流" 页。 配置以下元素： 

![新建操作规则创建流](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>范围

首先选择范围（Azure 订阅、资源组或目标资源）。 您还可以在单个订阅中选择多个作用域的组合。

![操作规则作用域](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>筛选条件

你还可以定义筛选器，以将其缩小到警报的特定子集。 

可用的筛选器包括： 

* **严重性**：选择一个或多个警报严重性的选项。 **严重性 = Sev1**表示操作规则适用于设置为 Sev1 的所有警报。
* **监视服务**：基于发起监视服务的筛选器。 此筛选器也是多选。 例如， **Monitor Service = "Application Insights"** 表示操作规则适用于所有基于 Application Insights 的警报。
* **资源类型**：基于特定资源类型的筛选器。 此筛选器也是多选。 例如，**资源类型 = "虚拟机"** 意味着操作规则适用于所有虚拟机。
* **警报规则 ID**：使用警报规则资源管理器 ID 筛选特定警报规则的选项。
* **监视条件**：对警报实例的筛选器，该筛选器**触发**或**解析**为监视条件。
* **说明**：对定义为警报规则的一部分的说明定义字符串匹配的正则表达式匹配。 例如， **"说明" 包含 "生产"** 将匹配其说明中包含字符串 "生产" 的所有警报。
* **警报上下文（负载）** ：一个 regex 匹配，用于根据警报的有效负载的警报上下文字段定义字符串匹配。 例如，**警报上下文（负载）包含 "computer-01"** 将匹配其负载包含字符串 "computer-01" 的所有警报。

这些筛选器会彼此应用。 例如，如果设置了**资源类型 "= 虚拟机**和**严重性" = Sev0**，则只对 Vm 上的所有**Sev0**警报进行筛选。 

![操作规则筛选器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>禁止显示或操作组配置

接下来，为警报抑制或操作组支持配置操作规则。 不能同时选择两者。 此配置作用于与以前定义的作用域和筛选器匹配的所有警报实例。

#### <a name="suppression"></a>隐含

如果**选择 "取消"，** 请配置禁止显示操作和通知的持续时间。 选择以下选项之一：
* **从现在（总是）** ：无限期地取消所有通知。
* **在计划时间：在**限制的持续时间内取消通知。
* **定期**执行：取消每日、每周或每月计划的定期通知。

![操作规则禁止显示](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>操作组

如果在切换时选择 "**操作组**"，请添加现有操作组或创建一个新的操作组。 

> [!NOTE]
> 只能将一个操作组与一个操作规则关联。

![通过选择 "操作组" 添加或创建新操作规则](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>操作规则详细信息

最后，为操作规则配置以下详细信息：
* 名称
* 保存它的资源组
* 描述 

## <a name="example-scenarios"></a>示例方案

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>方案1：根据严重性禁止显示警报

Contoso 希望**在每个**周末为订阅中的所有 vm 上的所有 Sev4 警报抑制通知。

**解决方案：** 使用以下内容创建操作规则：
* 范围 = **ContosoSub**
* 筛选器
    * 严重性 = **Sev4**
    * 资源类型 =**虚拟机**
* 将重复设置为 "每周"，并选中 "**星期六**" 和 "**星期日**"

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>方案2：根据警报上下文抑制警报（有效负载）

Contoso 希望为**ContosoSub**中的**计算机 01**的所有日志警报禁止显示通知，因为它正在进行维护。

**解决方案：** 使用以下内容创建操作规则：
* 范围 = **ContosoSub**
* 筛选器
    * 监视服务 = **Log Analytics**
    * 警报上下文（负载）包含**计算机 01**
* 禁止显示设置为**从现在（总是）**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>方案3：在资源组中定义的操作组

Contoso 已[在订阅级别定义指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但它需要定义为资源组**ContosoRG**中生成的警报专门触发的操作。

**解决方案：** 使用以下内容创建操作规则：
* 范围 = **ContosoRG**
* 无筛选器
* 操作组设置为**ContosoActionGroup**

> [!NOTE]
> *操作规则和警报规则中定义的操作组独立操作，无重复数据删除。* 在前面所述的方案中，如果为警报规则定义了操作组，则该操作组将与操作规则中定义的操作组一起触发。 

## <a name="managing-your-action-rules"></a>管理操作规则

你可以从列表视图中查看和管理操作规则：

![操作规则列表视图](media/alerts-action-rules/action-rules-list-view.png)

在此处，可以通过选中其旁边的复选框来启用、禁用或删除扩展操作规则。 选择操作规则时，会打开其配置页面。 此页可帮助你更新操作规则的定义并启用或禁用它。

## <a name="best-practices"></a>最佳做法

使用 "[结果数](alerts-unified-log.md)" 选项创建的日志警报使用整个搜索结果（可能跨多台计算机）生成单个警报实例。 在这种情况下，如果某个操作规则使用**警报上下文（负载）** 筛选器，则只要有匹配项，它就会在警报实例上执行操作。 在前面所述的方案2中，如果生成的日志警报的搜索结果同时包含**计算机 01**和**计算机-02**，则会取消整个通知。 根本没有为**计算机-02**生成通知。

![操作规则和日志警报（结果数）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要最好地使用带有操作规则的日志警报，请使用 "[指标度量](alerts-unified-log.md)" 选项创建日志警报。 基于其定义组字段的此选项生成单独的警报实例。 然后，在方案2中，为**计算机 01**和**计算机-02**生成单独的警报实例。 由于方案中所述的操作规则，将仅抑制**计算机 01**的通知。 **计算机-02**的通知将继续正常激发。

![操作规则和日志警报（结果数）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常见问题解答

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>在配置操作规则时，我想要查看所有可能的重叠操作规则，以便避免重复的通知。 是否可以执行此操作？

在配置操作规则后定义作用域后，可以看到在同一作用域（如果有）重叠的操作规则的列表。 此重叠可以是以下选项之一：

* 完全匹配：例如，要定义的操作规则和重叠操作规则位于同一个订阅中。
* 子集：例如，要定义的操作规则位于订阅上，重叠操作规则位于订阅内的资源组中。
* 超集：例如，要定义的操作规则位于资源组上，重叠操作规则位于包含资源组的订阅上。
* 交集：例如，要定义的操作规则在**VM1**和**VM2**上，重叠操作规则在**VM2**和**VM3**上。

![重叠操作规则](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>在配置警报规则时，能否知道是否已定义了可作用于我定义的警报规则的操作规则？

为警报规则定义目标资源后，可以通过选择 "**操作**" 部分下的 "**查看已配置的操作**"，查看作用于相同作用域的操作规则的列表（如果有）。 此列表是根据范围的下列方案进行填充的：

* 完全匹配：例如，要定义的警报规则和操作规则位于同一个订阅中。
* 子集：例如，要定义的警报规则在订阅上，而操作规则位于订阅内的资源组中。
* 超集：例如，要定义的警报规则在资源组中，而操作规则位于包含资源组的订阅上。
* 交集：例如，要定义的警报规则在**VM1**和**VM2**上，而操作规则位于**VM2**和**VM3**。
    
![重叠操作规则](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>能否看到操作规则已取消的警报？

在 "[警报列表" 页](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)中，可以选择名为 "**禁止**显示" 状态的其他列。 如果已取消警报实例的通知，则它会在列表中显示该状态。

![抑制的警报实例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果某个操作规则具有操作组，而另一个操作规则在同一范围内处于活动状态，则会发生什么情况？

抑制始终优先于同一个作用域。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果在两个不同的操作规则中监视某个资源，会发生什么情况？ 我是否获取了一条或两条通知？ 例如，在以下情况下， **VM2** ：

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

对于 VM1 和 VM3 上的每个警报，将触发一次操作组 AG1。 对于**VM2**上的每个警报，将触发操作组 AG1 两次，因为操作规则不会删除重复操作。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果在两个不同的操作规则中监视了某个资源，而另一个操作调用而另一个用于抑制，会发生什么情况？ 例如，在以下情况下， **VM2** ：

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

对于 VM1 上的每个警报，将触发操作组 AG1 一次。 将抑制针对 VM2 和 VM3 上的每个警报的操作和通知。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果为调用不同操作组的同一资源定义了警报规则和操作规则，会发生什么情况？ 例如，在以下情况下， **VM1** ：

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
对于 VM1 上的每个警报，将触发操作组 AG1 一次。 每当触发警报规则 "rule1" 时，它也会触发 AG2。 操作规则和警报规则中定义的操作组独立操作，无重复数据删除。 

## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 中的警报的详细信息](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
