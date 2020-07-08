---
title: 排查 Azure 指标警报问题
description: Azure Monitor 指标警报和可能的解决方案的常见问题。
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 06/21/2020
ms.subservice: alerts
ms.openlocfilehash: 36ff80bc0858d6d08cc120d126628de02ba6e703
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85130732"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>解决 Azure Monitor 指标警报中的问题 

本文讨论 Azure Monitor[指标警报](alerts-metric-overview.md)中的常见问题，以及如何解决这些问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅[Microsoft Azure 中的警报概述](alerts-overview.md)。

## <a name="metric-alert-should-have-fired-but-didnt"></a>指标警报应已触发但未发出 

如果你认为某个指标警报应已激发但未在 Azure 门户中进行触发，则请尝试执行以下步骤：

1. **配置**-查看指标警报规则配置，确保其配置正确：
    - 检查是否按预期方式配置了**聚合类型**、**聚合粒度（句点）** 以及**阈值**或**敏感度**
    - 对于使用动态阈值的警报规则，请检查是否已配置高级设置，因为**冲突数量**可能会筛选警报并**忽略数据**，然后才能影响阈值的计算方式

       > [!NOTE] 
       > 动态阈值在变为活动状态之前至少需要3天和30个指标样本。

2. 已**激发但无通知**-请查看[触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，查看是否可以找到触发的警报。 如果你可以在列表中看到警报，但某些操作或通知有问题，请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-troubleshoot#action-or-notification-on-my-alert-did-not-work-as-expected)的详细信息。

3. **已处于活动状态**-检查预计要为其获取警报的指标时序是否已触发警报。 指标警报是有状态警报，这意味着，一旦在特定指标时序上触发警报，就不会再激发该时间序列上的其他警报，直到不再发现该问题。 此设计选择减少了干扰。 对于三个连续评估不满足警报条件时，将自动解决警报。

4. **使用的维度**-如果已经[为某个指标](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)选择了某些维度值，则该警报规则将监视每个指标时间系列（由维度值组合定义），以应对阈值泄露。 若要监视聚合指标时序（没有选择任何维度），请在指标上配置附加警报规则而不选择维度。

5. **聚合和时间粒度**-如果要使用[度量值图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)直观呈现指标，请确保：
    * 度量值图中的选定**聚合**与警报规则中的**聚合类型**相同
    * 所选**时间粒度**与警报规则中的**聚合粒度（period）** 相同（并且未设置为 "自动"）

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>在不应使用时触发的指标警报

如果您认为您的指标警报不应已激发但确实如此，则以下步骤可能有助于解决此问题。

1. 查看[触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)以查找触发的警报，并单击查看其详细信息。 查看触发**此警报的原因**下提供的信息，以查看触发警报时的指标图表、**指标值**和**阈值**。

    > [!NOTE] 
    > 如果你使用动态阈值条件类型，并且认为使用的阈值不正确，请使用哭脸图标提供反馈。 此反馈将影响机器学习算法研究，并帮助改进未来的检测。

2. 如果已为某个指标选择了多个维度值，则当**任何**指标时间序列（由维度值组合定义）违反阈值时，就会触发警报。 有关在指标警报中使用维度的详细信息， [请参阅](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)。

3. 查看警报规则配置，确保它已正确配置：
    - 检查是否按预期方式配置了**聚合类型**、**聚合粒度（句点）** 以及**阈值**或**敏感度**
    - 对于使用动态阈值的警报规则，请检查是否已配置高级设置，因为**冲突数量**可能会筛选警报并**忽略数据**，然后才能影响阈值的计算方式

   > [!NOTE]
   > 动态阈值在变为活动状态之前至少需要3天和30个指标样本。

4. 如果要使用[度量值图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)直观呈现指标，请确保：
    - 度量值图中的选定**聚合**与警报规则中的**聚合类型**相同
    - 所选**时间粒度**与警报规则中的**聚合粒度（period）** 相同（并且未设置为 "自动"）

5. 如果已触发警报监视相同条件（未解决）时触发警报，请检查是否已将警报规则配置为 " *autoMitigate* " 属性设置为 " **false** " （此属性只能通过 REST/PowerShell/CLI 配置，因此请查看用于部署警报规则的脚本）。 在这种情况下，预警规则不会对触发的警报进行故障排除，并且不需要在再次触发之前解决触发的警报。


## <a name="cant-find-metric-to-alert-on---virtual-machines"></a>找不到用于在虚拟机上发出警报的指标 

若要对虚拟机上的来宾指标（对于内存、磁盘空间）发出警报，请确保已设置诊断设置，以便将数据发送到 Azure Monitor 接收器：
    * [对于 Windows VM](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)
    * [对于 Linux VM](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)
    
> [!NOTE] 
> 如果已将来宾指标配置为发送到 Log Analytics 工作区，则度量值将显示在 "Log Analytics" 工作区资源下，并且仅在创建监视这些度量值的警报规则之后**才**会开始显示数据。 为此，按照步骤[配置日志的指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#configuring-metric-alert-for-logs)。

## <a name="cant-find-the-metric-to-alert-on"></a>找不到要警报的指标

如果希望根据特定指标发出警报，但找不到资源的任何指标，请[检查指标警报是否支持该资源类型](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-near-real-time)。
如果可以看到资源的某些度量值，但找不到特定的指标，请[检查该指标是否可用](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)，如果是，请查看指标说明，查看其是否仅适用于特定版本的资源。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>找不到要对其发出警报的指标维度

如果你希望对[指标的特定维度值](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#using-dimensions)发出警报，但找不到这些值，请注意以下事项：

1. 维度值可能需要几分钟时间才能显示在“维度值”列表下
1. 显示的维度值基于在过去三天内收集到的指标数据
1. 如果尚未发出此维度值，请单击 "+" 符号以添加自定义值
1. 如果要对某个维度的所有可能的值（包括未来值）发出警报，请选中 "Select *" 复选框

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>仍在已删除的资源上定义指标警报规则 

删除 Azure 资源时，不会自动删除关联的指标预警规则。 若要删除与已删除资源关联的警报规则，请执行以下操作：

1. 打开在其中定义了删除的资源的资源组
1. 在显示资源的列表中，选中“显示隐藏的类型”复选框
1. 按类型 == microsoft.insights/metricalerts 筛选列表
1. 选择相关的警报规则，并选择 "**删除**"

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>每次满足条件时，都会出现指标警报

指标警报在默认情况下是有状态的，因此，如果在给定的时间序列上已经触发了警报，则不会触发其他警报。 如果希望使特定的指标警报规则无状态，并在每个满足警报条件的评估上收到警报，请以编程方式（例如，通过[资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)、 [PowerShell](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1)、 [REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate)、 [CLI](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)）创建警报规则，并将*autoMitigate*属性设置为 "False"。

> [!NOTE] 
> 将指标警报规则设为无状态可阻止触发警报，因此即使在不再满足条件后，触发的警报也会在30天的保留期内保持为已触发状态。


## <a name="metric-alert-rules-quota-too-small"></a>指标警报规则配额太小

每个订阅允许的指标警报规则数取决于[配额限制](https://docs.microsoft.com/azure/azure-monitor/service-limits)。

如果已达到配额限制，请执行以下步骤以帮助解决此问题：
1. 请尝试删除或禁用不再使用的指标警报规则。

2. 切换到使用监视多个资源的指标预警规则。 利用此功能，单个警报规则只能使用一个针对配额计数的警报规则监视多个资源。 要详细了解此功能和支持的资源类型，请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

3. 如果需要增加配额限制，请打开支持请求，并提供以下信息：

    - 需要为其增加配额限制的订阅 Id
    - 配额增加的资源类型：**指标警报**或**指标警报（经典）**
    - 请求的配额限制

## <a name="check-total-number-of-metric-alert-rules"></a>检查指标警报规则的总数

若要检查指标警报规则的当前使用情况，请执行以下步骤。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 打开“警报”屏幕，然后单击“管理预警规则” 
2. 使用 "**订阅**" 下拉列表控件筛选到相关订阅
3. 请确保不筛选到特定的资源组、资源类型或资源
4. 在 "**信号类型**" 下拉控件中，选择 "**指标**"
5. 验证 "**状态**" 下拉列表控件是否已设置为 "**已启用**"
6. 指标警报规则的总数显示在规则列表的上方

### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [按订阅列出](https://docs.microsoft.com/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>使用资源管理器模板、REST API、PowerShell 或 Azure CLI 管理警报规则

如果使用资源管理器模板、REST API、PowerShell 或 Azure 命令行接口（CLI）来创建、更新、检索或删除指标警报时遇到问题，以下步骤可帮助解决此问题。

### <a name="resource-manager-templates"></a>资源管理器模板

- 查看[常见 Azure 部署错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)列表并相应地进行故障排除
- 请参阅[指标警报 Azure 资源管理器模板示例](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)，以确保正确传递所有参数

### <a name="rest-api"></a>REST API

查看 [REST API 指南](https://docs.microsoft.com/rest/api/monitor/metricalerts/)来验证是否正确传递了所有参数

### <a name="powershell"></a>PowerShell

请确保对指标警报使用正确的 PowerShell cmdlet：

- [Az.Monitor 模块](https://docs.microsoft.com/powershell/module/az.monitor/?view=azps-3.6.1) 中提供了用于指标警报的 PowerShell cmdlet
- 请确保对新的（非经典）指标警报（例如， [AzMetricAlertRuleV2](https://docs.microsoft.com/powershell/module/az.monitor/Add-AzMetricAlertRuleV2?view=azps-3.6.1)）使用以 "V2" 结尾的 cmdlet

### <a name="azure-cli"></a>Azure CLI

请确保对指标警报使用正确的 CLI 命令：

- 适用于指标警报的 CLI 命令以 `az monitor metrics alert` 开头。 查看 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)来了解语法。
- 你可以查看[展示了如何使用指标警报 CLI 的示例](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
- 若要针对自定义指标发出警报，请确保使用相关指标命名空间为指标名称加上前缀：NAMESPACE.METRIC

### <a name="general"></a>常规

- 如果收到 `Metric not found` 错误：

   - 对于平台指标：请确保使用的是["Azure Monitor 支持的指标" 页](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)中的**指标**名称，而不是**指标显示名称**

   - 对于自定义指标：请确保已发出指标（您不能对不存在的自定义指标创建警报规则），并且提供自定义指标的命名空间（请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)的 ARM 模板示例）

- 如果要创建[关于日志的指标警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs)，请确保包括相应的依赖项。 参阅[示例模板](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-logs#resource-template-for-metric-alerts-for-logs)。

- 如果要创建包含多个条件的警报规则，请注意以下限制：

   - 在每个条件内，只能为每个维度选择一个值
   - 不能使用“\*”作为维度值
   - 当在不同标准中配置的度量值支持相同的维度时，必须为所有这些指标以相同的方式显式设置已配置的维度值（请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)的资源管理器模板示例）


## <a name="no-permissions-to-create-metric-alert-rules"></a>没有创建指标警报规则的权限

若要创建指标警报规则，你将需要具有以下权限：

- 警报规则的目标资源的读取权限
- 对在其中创建警报规则的资源组具有写入权限（如果是从 Azure 门户创建警报规则，则会在目标资源所在的同一资源组中创建警报规则）。
- 对任何与警报规则相关联的操作组的读取权限（如果适用）


## <a name="naming-restrictions-for-metric-alert-rules"></a>指标警报规则的命名限制

请注意对指标警报规则名称的以下限制：

- 指标警报规则名称在创建后无法更改（重命名）
- 度量警报规则名称在资源组中必须是唯一的
- 指标警报规则名称不能包含以下字符： * # & +：  < > ？ @ % { } \ / 
- 指标警报规则名称不能以以下字符结尾：。


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>在具有多个条件的指标警报规则中使用维度时的限制

指标警报支持对多维指标发出警报，并支持定义多个条件（每个警报规则最多5个条件）。

在包含多个条件的警报规则中使用维度时，请注意以下约束：
1. 在每个条件中，只能为每个维度选择一个值。
2. 不能使用选项 "选择所有当前和未来值" （Select \* ）。
3. 当在不同条件下配置的度量值支持相同的维度时，必须为所有这些指标（在相关条件下）以相同的方式显式设置配置的维度值。
例如：
    - 请考虑在存储帐户上定义的指标警报规则，并监视两个条件：
        * **事务**总数 > 5
        * 平均**SuccessE2ELatency** > 250 ms
    - 我想更新第一个条件，并且仅监视**ApiName**维度等于 *"GetBlob"* 的事务
    - 因为**事务**和**SuccessE2ELatency**度量值都支持**ApiName**维度，所以我需要同时更新这两个条件，并且它们都使用 *"GetBlob"* 值指定**ApiName**维度。


## <a name="next-steps"></a>后续步骤

- 有关警报和通知的常规疑难解答信息，请参阅[解决 Azure Monitor 警报中的问题](alerts-troubleshoot.md)。
