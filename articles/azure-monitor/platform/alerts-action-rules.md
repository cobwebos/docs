---
title: Azure 监视器将发出警报的操作规则
description: 了解什么是 Azure Monitor 中的操作规则以及如何配置和管理它们。
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656726"
---
# <a name="action-rules-preview"></a>操作规则 （预览版）

操作规则帮助您定义或取消任何 Azure 资源管理器范围 （Azure 订阅、 资源组或目标资源） 的操作。 它们具有不同的筛选器，帮助您缩小警报实例的特定子集，您需要采取措施。

## <a name="why-and-when-should-you-use-action-rules"></a>为什么以及何时应使用操作规则？

### <a name="suppression-of-alerts"></a>禁止显示的警报

有很多情况下，用于抑制的警报生成通知。 这些方案涵盖了从在计划内的维护时段禁止显示到抑制非营业时间内。 例如，负责的团队**ContosoVM**想要取消警报通知即将发布过周末了，因为**ContosoVM**正在进行计划内的维护。 

尽管团队可以禁用配置每个警报规则**ContosoVM**手动 （和维护后重新启用它），它不是一个简单的过程。 操作规则帮助您定义能够灵活地配置禁止显示的段的大规模警报抑制。 在上一示例中，团队可以上定义一个操作规则**ContosoVM**周末禁止显示所有警报的通知。


### <a name="actions-at-scale"></a>在规模较大的操作

虽然警报规则帮助您定义时将生成此警报触发的操作组，客户通常跨其操作的作用域具有一组常见的操作。 例如，一个团队负责的资源组**ContosoRG**可能定义中定义的所有警报规则的相同操作组**ContosoRG**。 

操作规则帮助您简化此过程。 通过定义在规模较大的操作，可以在配置的作用域生成任何警报的触发操作组。 在上一示例中，团队可以上定义一个操作规则**ContosoRG**会触发相同的操作组为在其中生成的所有警报。

> [!NOTE]
> 操作规则目前不适用于 Azure 服务运行状况警报。

## <a name="configuring-an-action-rule"></a>操作规则配置

可以通过选择访问功能**管理操作**从**警报**Azure Monitor 中的登录页。 然后，选择**操作规则 （预览版）** 。 可以通过选择访问规则**操作规则 （预览版）** 警报的登陆页面的仪表板。

![从 Azure 监视器登陆页面的操作规则](media/alerts-action-rules/action-rules-landing-page.png)

选择 **+ 新操作规则**。 

![添加新操作规则](media/alerts-action-rules/action-rules-new-rule.png)

或者，可以在你在重新配置警报规则创建操作规则。

![添加新操作规则](media/alerts-action-rules/action-rules-alert-rule.png)

现在应看到用于创建操作规则流页。 配置以下元素： 

![新操作规则创建流](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>范围

首先选择作用域 （Azure 订阅、 资源组或目标资源）。 您还可以多-选择单个订阅中的作用域的组合。

![操作规则作用域](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>筛选条件

此外，你可以定义筛选器以缩小其范围下，将发出警报的特定子集。 

可用的筛选器是： 

* **严重性**：要选择一个或多个警报的严重级别的选项。 **严重性 = Sev1**意味着操作规则适用的所有警报设置为 Sev1。
* **监视服务**:基于原始监视服务的筛选器。 此筛选器也是多个选择。 例如，**监视器服务 ="Application Insights"** 意味着操作规则适用于所有基于 Application Insights 的警报。
* **资源类型**:基于特定资源类型的筛选器。 此筛选器也是多个选择。 例如，**资源类型 ="虚拟机"** 意味着操作规则适用于所有虚拟机。
* **警报规则 ID**:若要使用警报规则的资源管理器 ID 筛选特定的警报规则的一个选项。
* **监视条件**:使用警报实例的筛选器**已触发**或**Resolved**作为监视器条件。
* **说明**：正则表达式 （正则表达式） 匹配项，用于定义对警报规则的一部分定义的说明的字符串匹配。 例如，**描述包含 'prod'** 将匹配包含字符串"prod"的所有警报及其说明。
* **警报上下文 （有效负载）** :正则表达式匹配项，用于定义对警报的有效负载的警报上下文字段的字符串匹配。 例如，**警报上下文 （有效负载） 包含"计算机-01"** 将匹配所有警报，其有效负载包含字符串"计算机-01。"

这些筛选器结合使用与另一个应用。 例如，如果您设置**资源类型 = 虚拟机**并**严重性 = Sev0**，然后筛选后的所有**Sev0**仅在 Vm 上的警报。 

![操作规则筛选器](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>禁止显示或操作组配置

接下来，配置警报抑制或操作组支持的操作规则。 您不能同时选择。 配置的作用与以前定义的范围和筛选器匹配的所有警报实例上。

#### <a name="suppression"></a>禁止显示

如果选择**抑制**，配置的操作和通知的禁止显示的持续时间。 选择以下选项之一：
* **从现在起 （始终）** :无限期地禁止显示所有通知。
* **在计划时间**:有限持续时间内将禁用通知。
* **并且指定了 recurrence**:禁止显示上重复执行的每日、 每周或每月计划的通知。

![操作规则禁止显示](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>操作组

如果选择**操作组**的切换按钮中添加现有操作组或新建一个。 

> [!NOTE]
> 可以将只有一个操作组和操作规则相关联。

![添加或通过选择操作组创建新操作规则](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>操作规则的详细信息

最后，配置操作规则的以下详细信息：
* 名称
* 在其中保存资源组
* 描述 

## <a name="example-scenarios"></a>示例方案

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>方案 1：基于严重性的警报中禁止显示

Contoso 想要取消的订阅中的所有 Vm 上的所有 Sev4 警报通知**ContosoSub**每个周末。

**解决方案：** 创建与操作规则：
* Scope = **ContosoSub**
* 筛选器
    * 严重性 = **Sev4**
    * 资源类型 =**虚拟机**
* 禁止显示有重复周期设置为每周，并**星期六**并**星期日**检查

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>方案 2：禁止显示的警报基于警报上下文 （有效负载）

Contoso 希望为所有日志针对生成的警报抑制通知**计算机-01**中**ContosoSub**无限期地为它当前正在维护。

**解决方案：** 创建与操作规则：
* Scope = **ContosoSub**
* 筛选器
    * Monitor Service = **Log Analytics**
    * 警报上下文 （有效负载） 包含**计算机-01**
* 禁止显示设置为**从现在起 （始终）**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>方案 3：在资源组中定义的操作组

具有定义的 Contoso[指标警报在订阅级别](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。 但它想要定义专用于从资源组生成的警报触发的操作**ContosoRG**。

**解决方案：** 创建与操作规则：
* Scope = **ContosoRG**
* 没有筛选器
* 操作组设置为**ContosoActionGroup**

> [!NOTE]
> *操作规则中定义的操作组和警报规则操作独立，与任何重复数据删除。* 中所述的方案更早版本，如果一个操作组定义警报规则，它与在操作规则中定义的操作组将触发一起使用。 

## <a name="managing-your-action-rules"></a>管理操作规则

可以查看和管理从列表视图中操作规则：

![操作规则列表视图](media/alerts-action-rules/action-rules-list-view.png)

在这里，可以启用、 禁用或删除操作规则在规模较大，通过选中其旁边的复选框。 当选择操作的规则时，其配置页将打开。 页面可帮助你更新操作规则的定义以及启用或禁用它。

## <a name="best-practices"></a>最佳实践

日志使用创建的警报[结果数](alerts-unified-log.md)选项使用整个搜索结果 （这可能会跨越多台计算机） 生成单个警报实例。 在此方案中，如果操作规则使用**警报上下文 （有效负载）** 筛选器，它将对警报实例，只要没有匹配项。 在方案 2 中，前面所述，如果生成的日志警报的搜索结果同时包含**计算机-01**并**计算机-02**，禁止显示整个通知。 为生成通知中不存在**计算机-02**根本。

![操作规则和日志警报 （结果数）](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

若要充分利用日志警报使用操作规则，创建与日志警报[公制度量单位](alerts-unified-log.md)选项。 此选项时，根据其定义的组字段由生成单独的警报实例。 然后，在方案 2 中，单独的警报实例为生成**计算机-01**并**计算机-02**。 由于操作规则中所述的方案中，仅为通知**计算机-01**被取消。 有关通知**计算机-02**继续照常触发。

![操作规则和日志警报 （结果数）](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>常见问题解答

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>虽然我配置的操作规则，我想要查看所有可能的重叠操作规则，以便避免重复的通知。 是否可以执行此操作？

配置操作规则定义的作用域后，可以看到操作重叠在同一作用域 （如果有） 的规则的列表。 这种重叠可以是下列选项之一：

* 完全匹配：例如，你定义的操作规则和重叠操作规则是在同一订阅。
* 子集：例如，你定义的操作规则是某一订阅和重叠操作规则是在订阅中的资源组。
* 超集：例如，你定义的操作规则是上一个资源组，和重叠操作规则位于包含资源组的订阅。
* 交集：例如，在定义操作规则位于**VM1**和**VM2**，和重叠操作规则位于**VM2**并**VM3**。

![重叠操作规则](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>我配置的警报规则，而是可能知道是否已有操作规则定义，它可能会对我定义的警报规则？

为警报规则定义的目标资源后，您所见的作用于同一作用域 （如果有） 选择的操作规则列表**配置操作视图**下**操作**部分。 此列表被填充基于作用域在以下方案：

* 完全匹配：例如，你定义的警报规则和操作规则是在同一订阅。
* 子集：例如，某一订阅是在定义警报规则和操作规则是在订阅中的资源组。
* 超集：例如，你定义的警报规则是上一个资源组，并且操作规则位于包含资源组的订阅。
* 交集：例如，你定义的警报规则是在**VM1**并**VM2**，和操作规则位于**VM2**并**VM3**。
    
![重叠操作规则](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>可以看到已取消操作规则的警报？

在中[警报列表页](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)，可以选择调用一个额外的列**禁止显示状态**。 如果一个警报实例的通知被禁止，它会在列表中显示该状态。

![禁止显示警报实例](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>如果使用操作组的操作规则，并禁止显示与另一个活动上的同一作用域，会发生什么情况？

禁止显示始终将优先于相同作用域。

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>如果我有两个单独的操作规则中受监视的资源，会发生什么情况？ 获取一个或两个通知？ 例如， **VM2**在以下方案：

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

对于每个警报在 VM1 和 VM3，操作组 AG1 会一次触发。 在每个警报**VM2**，AG1 会触发两次，因为操作规则不重复数据删除操作的操作组。 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>如果具有在两个单独的操作规则监视的资源，其中一个调用的操作，而另一个用于抑制，会发生什么情况？ 例如， **VM2**在以下方案：

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

对于在 VM1 上每个警报，操作组 AG1 会一次触发。 操作和 VM2 和 VM3 上每个警报的通知将被抑制。 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>如果我的警报规则，并为相同资源调用不同的操作组定义的操作规则，会发生什么情况？ 例如， **VM1**在以下方案：

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
对于在 VM1 上每个警报，操作组 AG1 会一次触发。 每当触发警报规则"rule1"时，它还将另外触发 AG2。 操作规则中定义的操作组和警报规则操作独立，与任何重复数据删除。 

## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 中的警报的详细信息](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
