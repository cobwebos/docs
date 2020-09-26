---
title: 使用 Azure Monitor 创建、查看和管理日志警报 |Microsoft Docs
description: 使用 Azure Monitor 创建、查看和管理日志预警规则
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 40cf46746587cbd221bd958ccb528c9e40cf18bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299358"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理日志警报

## <a name="overview"></a>概述

日志警报允许用户使用 [Log Analytics](../log-query/get-started-portal.md) 查询来评估每个设置频率的资源日志，并根据结果触发警报。 规则可使用 [操作组](./action-groups.md)触发一个或多个操作。 [详细了解日志警报的功能和术语](alerts-unified-log.md)。

本文说明如何使用 Azure Monitor 创建和管理日志警报。 警报规则由三个组件定义：
- 目标：要监视的特定 Azure 资源。
- 条件：要计算的逻辑。 如果满足，则会触发警报。  
- 操作：通知或自动化-电子邮件、短信、webhook 等。

你还可以使用 Azure 资源管理器模板创建日志警报规则，这些模板在 [单独的文章](alerts-log-create-templates.md)中进行了介绍。

> [!NOTE]
> 可以将 [Log Analytics 工作区](../log-query/get-started-portal.md) 中的日志数据发送到 Azure Monitor 指标存储中。 指标警报具有[不同的行为](alerts-metric-overview.md)，该行为可能更可取，具体取决于你要使用的数据。 要了解如何将日志路由到指标，请参阅[日志的指标警报](alerts-metric-logs.md)。

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>使用 Azure 门户创建日志警报规则

下面是开始编写警报查询的步骤：

1. 请参阅要对其发出警报的资源。
1. 在 " **监视**" 下，选择 " **日志**"。
1. 查询可以指示问题的日志数据。 您可以使用 " [警报查询示例" 主题](../log-query/saved-queries.md) 来了解可以发现或 [开始编写您自己的查询的](../log-query/get-started-portal.md)内容。 此外，[了解如何创建优化的警报查询](alerts-log-query.md)。
1. 按 "+ 新建警报规则" 按钮，启动警报创建流。

    ![Log Analytics - 设置警报](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> 建议在使用日志的资源访问模式时，按比例创建警报，该模式在使用资源组或订阅范围的多个资源上运行。 大规模警报会降低规则管理开销。 若要能够以资源为目标，请在结果中包含 "资源 ID" 列。 [详细了解如何按维度拆分警报](alerts-unified-log.md#split-by-alert-dimensions)。

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Log Analytics 和 Application Insights 的日志警报

1. 如果查询语法正确，则查询的历史数据将显示为一个图形，其中包含用于将图表期间从过去六个小时调整到上一周的选项。
 
    如果查询结果包含汇总数据或特定于 [项目](/azure/kusto/query/projectoperator) 的列（没有时间列），则图表将显示单个值。

    ![配置警报规则](media/alerts-log/AlertsPreviewAlertLog.png)

1. 使用 " [**时间段**](alerts-unified-log.md#query-time-range) " 选项选择评估指定条件的时间范围。

1. 日志警报可以基于两种类型的 [**度量值**](alerts-unified-log.md#measure)：
    1. **结果数** -查询返回的记录数。
    1. **指标度量**  - 使用 "汇总" 按选定的表达式和[bin ( 第 B1](/azure/kusto/query/binfunction)个选定内容分组计算*值*。 例如：

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. 对于指标度量警报逻辑，您可以选择使用**聚合 on**选项指定如何[按维度拆分警报](alerts-unified-log.md#split-by-alert-dimensions)。 行分组表达式必须唯一且已排序。

    > [!NOTE]
    > 如果 [bin ( # B1 ](/azure/kusto/query/binfunction) 可能导致不一致的时间间隔，则在运行时，警报服务将自动将 [Bin ( # B3 ](/azure/kusto/query/binfunction) 函数转换为 [bin_at ( # B5 ](/azure/kusto/query/binatfunction) 函数，以确保具有固定点的结果。

    > [!NOTE]
    > 按警报维度分割仅适用于当前 scheduledQueryRules API。 如果使用旧版 [Log Analytics 警报 API](api-alerts.md)，则需要切换。 [了解有关切换的详细信息](./alerts-log-api-switch.md)。 仅在 API 版本及更高版本中支持规模为资源中心的警报 `2020-05-01-preview` 。

    ![“聚合基于”选项](media/alerts-log/aggregate-on.png)

1. 接下来，根据预览数据设置 [**运算符**、 **阈值**](alerts-unified-log.md#threshold-and-operator)和 [**频率**](alerts-unified-log.md#frequency)。

1. 你还可以选择通过使用**总计或连续泄露**[来设置触发警报的冲突数](alerts-unified-log.md#number-of-violations-to-trigger-alert)。

1. 选择“完成”。 

1. 定义 **警报规则名称**和 **描述**，然后选择警报 **严重性**。 所有警报操作都使用这些详细信息。 此外，还可以选择在创建 **时启用规则**，而不激活预警规则。

1. 选择是否要在触发警报后抑制规则操作，请使用 " [**抑制警报**](alerts-unified-log.md#state-and-resolving-alerts) " 选项。 规则仍将运行并创建警报，但不会触发操作来防止干扰。 "静音操作" 值必须大于警报的有效频率。

    ![对日志警报禁止显示警报](media/alerts-log/AlertsPreviewSuppress.png)

1. 指定在满足警报条件时，警报规则是否应触发一个或多个 [**操作组**](action-groups.md#webhook) 。

    > [!NOTE]
    > 有关可以执行的操作的限制，请参阅 [Azure 订阅服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)。  

1. 您可以选择在日志警报规则中自定义操作：

    - **自定义电子邮件主题**：替代电子邮件操作的 *电子邮件主题* 。 不能修改邮件正文，此字段 **不能用于电子邮件地址**。
    - **包括自定义 Json 有效负载**：替代操作组使用的 webhook Json，假定操作组包含 webhook 操作。 详细了解 [日志警报的 webhook 操作](./alerts-log-webhook.md)。

    ![日志警报的操作替代](media/alerts-log/AlertsPreviewOverrideLog.png)

1. 如果正确设置了所有字段，则可以单击 " **创建警报规则** " 按钮并创建警报。

    在几分钟后，警报将如前所述激活并触发。

    ![创建规则](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>为警报管理中的 Log Analytics 和 Application Insights 创建日志警报

> [!NOTE]
> 资源中心日志当前不支持从警报管理创建

1. 在 [门户](https://portal.azure.com/)中，选择 " **监视** "，然后选择 " **警报**"。

    ![监视](media/alerts-log/AlertsPreviewMenu.png)

1. 选择 " **新建警报规则**"。 

    ![添加警报](media/alerts-log/AlertsPreviewOption.png)

1. 随即显示“创建警报”窗格。 它有四个部分： 
    - 要应用警报的资源。
    - 要检查的条件。
    - 当条件为 true 时要执行的操作。
    - 命名和描述警报的详细信息。 

    ![创建规则](media/alerts-log/AlertsPreviewAdd.png)

1. 按 " **选择资源** " 按钮。 通过选择 " *订阅*" 和 " *资源类型*" 进行筛选，然后选择一个资源。 确保资源具有可用的日志。

   ![选择资源](media/alerts-log/Alert-SelectResourceLog.png)

1. 接下来，使用 "添加 **条件** " 按钮查看适用于该资源的信号选项的列表。 选择 " **自定义日志搜索** " 选项。

   ![选择资源 - 自定义日志搜索](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 警报门户列出了 Log Analytics 和 Application Insights 中保存的查询，这些查询可用作模板警报查询。

1. 选择后，在 " **搜索查询** " 字段中编写、粘贴或编辑警报查询。

1. 继续执行 [上一部分](#log-alert-for-log-analytics-and-application-insights)中所述的后续步骤。

### <a name="log-alert-for-all-other-resource-types"></a>所有其他资源类型的日志警报

> [!NOTE]
> 对于 API 版本 `2020-05-01-preview` 和以资源为中心的日志警报，当前没有额外的费用。  未来将公布预览版中的功能的定价，以及开始计费之前提供的通知。 如果你选择在通知期后继续使用新的 API 版本和以资源为中心的日志警报，则将按适用的费率向你收费。

1. 从 " **条件** " 选项卡开始：

    1. 检查 [**度量值**](alerts-unified-log.md#measure)、 [**聚合类型**](alerts-unified-log.md#aggregation-type)和 [**聚合粒度**](alerts-unified-log.md#aggregation-granularity) 是否正确。 
        1. 默认情况下，该规则将计算过去5分钟内的结果数。
        1. 如果检测到汇总查询结果，则该规则将在几秒钟内自动更新以捕获。

    1. 如果需要，选择 " [按维度拆分警报](alerts-unified-log.md#split-by-alert-dimensions)"： 
       - 将自动选择 "**资源 ID" 列**（如果检测到），并将触发的警报的上下文更改为记录的资源。 
       - 可以取消选择**资源 ID 列**，以便在订阅或资源组上激发警报。 如果查询结果基于跨资源，则取消选择非常有用。 例如，检查资源组的虚拟机80% 是否遇到 CPU 使用率高的查询。
       - 对于使用维度表的任何数字或文本列类型，还可以选择多达六个附加的 splittings。
       - 根据基于唯一组合的拆分，单独触发警报，警报负载包括此信息。
    
        ![选择聚合参数和拆分](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. **预览**图表显示一段时间内的查询评估结果。 您可以更改图表时间段，或选择由维度拆分的唯一警报产生的不同时间系列。

        ![预览图表](media/alerts-log/preview-chart.png)

    1. 接下来，根据预览数据设置 **警报逻辑**; [**运算符**、 **阈值**](alerts-unified-log.md#threshold-and-operator)和 [**频率**](alerts-unified-log.md#frequency)。

        ![预览具有阈值和警报逻辑的图表](media/alerts-log/chart-and-alert-logic.png)

    1. 您可以选择在 "**高级选项**" 部分中设置[**触发警报的冲突数**](alerts-unified-log.md#number-of-violations-to-trigger-alert)。
    
        ![高级选项](media/alerts-log/advanced-options.png)

1. 在 " **操作** " 选项卡中，选择或创建所需的 [操作组](action-groups.md)。

    ![操作选项卡](media/alerts-log/actions-tab.png)

1. 在 " **详细信息** " 选项卡中，定义 **警报规则详细信息**和 **项目详细信息**。 您可以选择设置是 **立即开始运行**，还是在警报规则触发后的某个时间段设置 [**静音操作**](alerts-unified-log.md#state-and-resolving-alerts) 。

    > [!NOTE]
    > 日志警报规则当前是无状态的，并且每次创建警报时都将触发操作，除非定义了静音。

    ![“详细信息”选项卡](media/alerts-log/details-tab.png)

1. 在 " **标记** " 选项卡中，在 "警报规则" 资源上设置所需的任何标记。

    ![标记选项卡](media/alerts-log/tags-tab.png)

1. 在 " **查看** " 和 "创建" 选项卡中，将运行验证并通知任何问题。 查看并批准规则定义。
1. 如果所有字段均正确，请选择 " **创建** " 按钮并完成警报规则创建。 可以通过警报管理查看所有警报。
 
    ![查看和创建选项卡](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>在 Azure 门户中查看和管理日志警报

1. 在 [门户](https://portal.azure.com/)中，选择相关资源或 **监视** 服务。 然后，在 "监视" 部分选择 **警报** 。

1. 警报管理将显示触发的所有警报。 [详细了解警报管理](alerts-managing-alert-instances.md)。

    > [!NOTE]
    > 日志警报规则当前是 [无状态的，不能解决](alerts-unified-log.md#state-and-resolving-alerts)。

1. 在顶部栏中选择 " **管理规则** " 按钮以编辑规则：

    ![ 管理警报规则](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>使用 PowerShell 管理日志警报

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> API 版本当前不支持 PowerShell `2020-05-01-preview`

下面列出的 PowerShell cmdlet 可用于使用 [计划的查询规则 API](/rest/api/monitor/scheduledqueryrules/)来管理规则。

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule)：用于新建日志警报规则的 PowerShell cmdlet。
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule)：用于更新现有日志警报规则的 PowerShell cmdlet。
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource)：用于创建或更新对象的 PowerShell cmdlet，该对象为日志警报指定源参数。 由 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 和 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 用作输入。
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule)：用于创建或更新对象的 PowerShell cmdlet，该对象为日志警报指定计划参数。 由 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 和 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 用作输入。
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction)：用于创建或更新对象的 PowerShell cmdlet，该对象为日志警报指定操作参数。 由 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) 和 [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet 用作输入。
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup)：用于创建或更新对象的 PowerShell cmdlet，该对象为日志警报指定操作组参数。 由 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet 用作输入。
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)：用于创建或更新对象的 PowerShell cmdlet，该对象为日志警报指定触发器条件参数。 由 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet 用作输入。
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger)：用于创建或更新对象的 PowerShell cmdlet，该对象为[指标度量值类型日志警报](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)指定触发器条件参数。 由 [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet 用作输入。
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)：用于列出现有日志警报规则或指定日志警报规则的 PowerShell cmdlet
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule)：用于启用或禁用日志警报规则的 PowerShell cmdlet
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule)：用于删除现有日志警报规则的 PowerShell cmdlet

> [!NOTE]
> ScheduledQueryRules PowerShell cmdlet 只能管理在当前 [计划的查询规则 API](/rest/api/monitor/scheduledqueryrules/)中创建的规则。 仅在[切换到计划的查询规则 api](alerts-log-api-switch.md)之后，才能使用旧版[Log Analytics 警报 api](api-alerts.md)创建的日志警报规则才能使用 PowerShell 进行管理。

下面是使用 PowerShell 创建日志警报规则的示例步骤：

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

还可以使用 PowerShell 创建日志警报 [和参数](./alerts-log-create-templates.md) 文件：

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>使用 CLI 管理日志警报

> [!NOTE]
> Azure CLI 支持仅适用于 scheduledQueryRules API 版本 `2020-05-01-preview` 和更高版本。 早期 API 版本可将 Azure 资源管理器 CLI 与模板配合使用，如下所述。 如果使用旧版 [Log Analytics 警报 API](api-alerts.md)，则需要切换到使用 CLI。 [了解有关切换的详细信息](./alerts-log-api-switch.md)。

前面的部分介绍了如何使用 Azure 门户创建、查看和管理日志警报规则。 本部分将介绍如何使用跨平台 [Azure CLI](/cli/azure/get-started-with-azure-cli) 实现相同的结果。 使用 Azure CLI 的最快捷方式是通过 [Azure Cloud Shell](../../cloud-shell/overview.md)。 对于本文，我们将使用 Cloud Shell。

1. 请参阅 Azure 门户，选择 **Cloud Shell**。

1. 在提示符下，可以结合 ``--help`` 选项使用命令来详细了解相应的命令及其用法。 例如，下面的命令显示了可用于创建、查看和管理日志警报的命令的列表：

    ```azurecli
    az monitor scheduled-query --help
    ```

1. 你可以创建日志警报规则来监视系统事件错误的计数：

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. 可以使用以下命令查看资源组中的所有日志警报：

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. 你可以使用规则的名称或资源 ID 来查看特定日志警报规则的详细信息：

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. 您可以使用以下命令禁用日志警报规则：

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. 您可以使用以下命令删除日志警报规则：

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

你还可以将 Azure 资源管理器 CLI 与 [模板](./alerts-log-create-templates.md) 文件一起使用：

```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

创建成功后，将返回201。 如果更新成功，则返回200。

## <a name="next-steps"></a>后续步骤

* 了解 [日志警报](./alerts-unified-log.md)。
* 使用 [Azure 资源管理器模板](./alerts-log-create-templates.md)创建日志警报。
* 了解 [日志警报的 webhook 操作](./alerts-log-webhook.md)。
* 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。

