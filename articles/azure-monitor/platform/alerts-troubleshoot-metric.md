---
title: 排查 Azure 指标警报问题
description: Azure Monitor 指标警报的常见问题和可能的解决方案。
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 07/21/2020
ms.subservice: alerts
ms.openlocfilehash: 3e691e3f32404af792c852636a257659b629eef4
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824556"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>排查 Azure Monitor 指标警报的问题 

本文介绍了 Azure Monitor [指标警报](alerts-metric-overview.md)的常见问题，以及如何排查这些问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅[Microsoft Azure 中的警报概述](alerts-overview.md)。

## <a name="metric-alert-should-have-fired-but-didnt"></a>指标警报应当已触发但未触发 

如果你认为某个指标警报应当已触发但未触发且在 Azure 门户中找不到该警报，则请尝试执行以下步骤：

1. **配置** - 检查指标警报规则配置以确保它正确配置：
    - 检查是否按预期配置了“聚合类型”、“聚合粒度(周期)”和“阈值”或“敏感度”   
    - 对于使用“动态阈值”的警报规则，请检查是否配置了高级设置，因为“冲突的数量”可能会筛选警报，而“忽略之前的数据”会影响阈值的计算方式 

       > [!NOTE] 
       > 动态阈值在变为活动状态之前至少需要 3 天和 30 个指标示例。

2. **已触发但没有通知** - 复查[触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，看是否可以找到触发的警报。 如果可以在列表中看到该警报，但其部分操作或通知存在问题，请在[此处](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)了解更多信息。

3. **已处于活动状态** - 检查你预计会收到警报的指标时序是否已存在触发的警报。 指标警报是有状态的，即，一旦在特定的指标时序中触发某个警报，就不会触发该时序中的其他警报，直到相应的问题不再出现。 此设计选择减少了干扰。 当连续三次评估不满足警报条件时，警报会自动解决。

4. **使用的维度**-如果已经[为某个指标](./alerts-metric-overview.md#using-dimensions)选择了某些维度值，则该警报规则将监视由维度值组合定义的每个 "指标时序" () 用于阈值泄露。 如果还要监视聚合指标时序（不选择任何维度），请在该指标上配置附加警报规则而不选择维度。

5. **聚合和时间粒度**-如果要使用[度量值图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)可视化指标，请确保：
    * 指标图表中选择的“聚合”与警报规则中的“聚合类型”相同 
    * 所选的“时间粒度”与警报规则中的“聚合粒度(周期)”相同，且未设置为“自动” 

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>指标警报在不应当触发时触发

如果你认为指标警报不应当触发但却触发，则可通过以下步骤来解决问题。

1. 查看[触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，找到触发的警报，然后单击以查看其详细信息。 查看“为何会触发此警报?”下提供的信息，以了解触发警报时的指标图表、“指标值”和“阈值”  。

    > [!NOTE] 
    > 如果使用的是动态阈值条件类型，并认为使用的阈值不正确，请使用哭脸图标提供反馈。 此反馈会影响机器学习算法研究，有助于改进未来的检测。

2. 如果已为某个指标选择了多个维度值，则当维度值组合定义的**任何**指标时间序列 () 违反阈值时，将触发该警报。 有关在指标警报中使用维度的详细信息， [请参阅](./alerts-metric-overview.md#using-dimensions)。

3. 检查警报规则配置以确保它已正确配置：
    - 检查是否按预期配置了“聚合类型”、“聚合粒度(周期)”和“阈值”或“敏感度”   
    - 对于使用“动态阈值”的警报规则，请检查是否配置了高级设置，因为“冲突的数量”可能会筛选警报，而“忽略之前的数据”会影响阈值的计算方式 

   > [!NOTE]
   > 动态阈值在变为活动状态之前至少需要 3 天和 30 个指标示例。

4. 如果要使用[度量值图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)直观呈现指标，请确保：
    - 指标图表中选择的“聚合”与警报规则中的“聚合类型”相同 
    - 所选的“时间粒度”与警报规则中的“聚合粒度(周期)”相同，且未设置为“自动” 

5. 如果在触发该警报时已存在对同一条件进行监视的已触发警报（尚未解决），则请检查是否已将警报规则的已配置 autoMitigate 属性设置为“false”（此属性只能通过 REST/PowerShell/CLI 进行配置，因此请检查用来部署警报规则的脚本）。 在这种情况下，警报规则不会自动解析触发的警报，并且在再次触发警报之前不需要解析触发的警报。


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>找不到用于对虚拟机来宾指标发出警报的指标

若要在虚拟机的来宾操作系统度量值上发出警报 (例如：内存、磁盘空间) ，请确保已安装了将此数据收集到 Azure Monitor 指标所需的代理：
- [对于 Windows VM](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [对于 Linux VM](./collect-custom-metrics-linux-telegraf.md)

有关从虚拟机的来宾操作系统收集数据的详细信息，请参阅[此处](../insights/monitor-vm-azure.md#guest-operating-system)。
    
> [!NOTE] 
> 如果将来宾指标配置为发送到 Log Analytics 工作区中，则这些指标将显示在 Log Analytics 工作区资源下，并且只会在创建用于监视数据的警报规则后才开始显示数据。 为此，按照步骤[配置日志的指标警报](./alerts-metric-logs.md#configuring-metric-alert-for-logs)。

## <a name="cant-find-the-metric-to-alert-on"></a>找不到警报所针对的指标

如果希望针对特定指标发出警报，但看不到该资源的任何指标，请[检查指标警报是否支持该资源类型](./alerts-metric-near-real-time.md)。
如果可以看到该资源的某些指标，但找不到某个特定的指标，请[检查该指标是否可用](./metrics-supported.md)。如果可用，请参阅指标说明，了解它是否仅适用于该资源的特定版本。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>找不到警报所针对的指标维度

如果你希望对[指标的特定维度值](./alerts-metric-overview.md#using-dimensions)发出警报，但找不到这些值，请注意以下事项：

1. 维度值可能需要几分钟时间才能显示在“维度值”列表下
1. 显示的维度值基于在过去三天内收集到的指标数据
1. 如果未发出此维度值，请单击“+”符号以添加自定义值
1. 如果要对某个维度的所有可能值（包括将来的值）发出警报，请选中“选择 *”复选框

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>在已删除资源上仍然会定义指标警报规则 

删除 Azure 资源时，不会自动删除关联的指标预警规则。 若要删除与已删除的资源关联的警报规则，请执行以下操作：

1. 打开在其中定义了删除的资源的资源组
1. 在显示资源的列表中，选中“显示隐藏的类型”复选框
1. 按类型 == microsoft.insights/metricalerts 筛选列表
1. 选择相关的警报规则，并选择“删除”

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>使指标警报在每次满足条件时都出现

默认情况下，指标警报是有状态的，因此，如果给定的时序已存在触发的警报，则不会触发其他警报。 如果希望将特定指标警报规则设为无状态，并在每次评估符合警报条件时收到警报，请以编程方式（例如，通过[资源管理器](./alerts-metric-create-templates.md)、[PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1)、[REST](/rest/api/monitor/metricalerts/createorupdate)、[CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)）创建警报规则，并将 autoMitigate 属性设为“False”。

> [!NOTE] 
> 将指标警报规则设为无状态会妨碍已触发警报的解决，因此即使在不再满足条件后，触发的警报也会在 30 天的保留期内保持已触发状态。

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>定义尚未发出的自定义指标的警报规则

创建指标警报规则时，将根据[指标定义 API](/rest/api/monitor/metricdefinitions/list)验证指标名称，以确保其存在。 在某些情况下，即使在发出之前，也要根据自定义指标创建警报规则。 例如，使用 ARM 模板创建 (时) 将发出自定义指标的 Application Insights 资源，以及用于监视该指标的警报规则。

若要避免在尝试验证自定义指标的定义时部署失败，可以在预警规则的 "条件" 部分中使用*skipMetricValidation*参数，这将导致跳过指标验证。 请参阅下面的示例，了解如何将此参数用于 ARM 模板 (有关创建指标警报规则的完整 ARM 模板示例，请参阅[此处]( ./alerts-metric-create-templates.md)) 。

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                    "name" : "condition1",
                        "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                        "dimensions":[],
                        "operator": "GreaterThan",
                        "threshold" : 10,
                        "timeAggregation": "Average",
                    "skipMetricValidation": true
        }
              ]
        }
```

## <a name="metric-alert-rules-quota-too-small"></a>指标警报规则配额太小

每个订阅允许的指标警报规则数目受制于[配额限制](../service-limits.md)。

如果已达到配额限制，以下步骤可帮助解决此问题：
1. 尝试删除或禁用不再使用的指标警报规则。

2. 切换到使用监视多个资源的指标预警规则。 通过此功能，一个警报规则可以监视多个资源，且只会将一个警报规则计入配额。 要详细了解此功能和支持的资源类型，请参阅[此处](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

3. 如果需要提高配额限制，请创建支持请求，并提供以下信息：

    - 需要提高配额限制的订阅 ID
    - 增大配额的资源类型：“指标警报”或“指标警报(经典)” 
    - 请求的配额限制

## <a name="check-total-number-of-metric-alert-rules"></a>检查指标警报规则的总数

若要检查指标警报规则的当前使用情况，请执行以下步骤。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 打开“警报”屏幕，然后单击“管理预警规则” 
2. 使用“订阅”下拉列表控件筛选到相关订阅
3. 请勿筛选到特定的资源组、资源类型或资源
4. 在“信号类型”下拉列表控件中，选择“指标” 
5. 验证“状态”下拉列表控件是否设置为“已启用” 
6. "警报规则" 列表上方显示的指标警报规则总数

### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [按订阅列出](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>使用资源管理器模板、REST API、PowerShell 或 Azure CLI 管理警报规则

如果使用资源管理器模板、REST API、PowerShell 或 Azure 命令行接口 (CLI) 来创建、更新、检索或删除指标警报时遇到问题，则可以通过以下步骤来帮助解决此问题。

### <a name="resource-manager-templates"></a>Resource Manager 模板

- 查看[常见 Azure 部署错误](../../azure-resource-manager/templates/common-deployment-errors.md)列表并相应地进行故障排除
- 请参阅[指标警报 Azure 资源管理器模板示例](./alerts-metric-create-templates.md)，以确保正确传递所有参数

### <a name="rest-api"></a>REST API

查看[REST API 指南](/rest/api/monitor/metricalerts/)，验证是否已正确传递所有参数

### <a name="powershell"></a>PowerShell

请确保使用正确的 PowerShell cmdlet 来查找指标警报：

- [Az.Monitor 模块](/powershell/module/az.monitor/?view=azps-3.6.1) 中提供了用于指标警报的 PowerShell cmdlet
- 请确保对新（非经典）指标警报（例如 [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)）使用以“V2”结尾的 cmdlet

### <a name="azure-cli"></a>Azure CLI

请确保对指标警报使用正确的 CLI 命令：

- 适用于指标警报的 CLI 命令以 `az monitor metrics alert` 开头。 查看 [Azure CLI 参考](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)来了解语法。
- 你可以查看[展示了如何使用指标警报 CLI 的示例](./alerts-metric.md#with-azure-cli)
- 若要针对自定义指标发出警报，请确保使用相关指标命名空间为指标名称加上前缀：NAMESPACE.METRIC

### <a name="general"></a>常规

- 如果收到 `Metric not found` 错误：

   - 对于平台指标：请确保使用 " [Azure Monitor 支持的指标" 页](./metrics-supported.md)中的**指标**名称，而不是**指标显示名称**

   - 对于自定义指标：请确保已发出指标 (你无法) 中创建不存在的自定义指标的警报规则，并提供自定义指标的命名空间 (参阅[此处](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)的 ARM 模板示例) 

- 如果要创建[针对日志的指标警报](./alerts-metric-logs.md)，请确保包含适当的依赖项。 参阅[示例模板](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)。

- 如果要创建包含多个条件的警报规则，请注意以下限制：

   - 在每个条件内，只能为每个维度选择一个值
   - 不能使用“\*”作为维度值
   - 当以不同条件配置的指标支持相同维度时，则必须以相同方式为所有这些指标显式设置配置的维度值（请参阅[此处](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)的资源管理器模板示例）


## <a name="no-permissions-to-create-metric-alert-rules"></a>无权创建指标警报规则

若要创建指标警报规则，你需要有以下权限：

- 对警报规则的目标资源的读取权限
- 对在其中创建警报规则的资源组的写入权限（如果是从 Azure 门户中创建警报规则，则会在目标资源所在的资源组中创建警报规则）
- 对关联到警报规则的任何操作组的读取权限（如果适用）


## <a name="naming-restrictions-for-metric-alert-rules"></a>指标警报规则的命名限制

对于指标警报规则名称，请考虑以下限制：

- 指标警报规则名称在创建后无法更改（重命名）
- 指标警报规则名称在资源组中必须唯一
- 指标警报规则名称不能包含以下字符：* # & + : < > ? @ % { } \ / 
- 指标警报规则名称不能以以下字符结尾：.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>在具有多个条件的指标警报规则中使用维度时的限制

指标警报支持根据多维指标发出警报，并支持定义多个条件（每个警报规则最多可定义 5 个条件）。

在包含多个条件的警报规则中使用维度时，请考虑以下约束：
- 在每个条件中，只能为每个维度选择一个值。
- 不能使用选项“选择所有当前值和未来值”（选择 \*）。
- 如果在不同条件中配置的指标支持同一维度，则必须以相同方式为所有这些指标（在相关条件中）显式设置配置的维度值。
例如：
    - 请考虑在存储帐户上定义的一个指标警报规则，该警报规则监视两个条件：
        * **事务**总数 > 5
        * 平均 **SuccessE2ELatency** > 250 毫秒
    - 我想更新第一个条件，并且仅监视 **ApiName** 维度等于“GetBlob”的事务
    - 由于“事务数”和 **SuccessE2ELatency** 指标都支持 **ApiName** 维度，所以我需要更新这两个条件，并将它们的 **ApiName** 维度都指定为“GetBlob”值。


## <a name="next-steps"></a>后续步骤

- 有关警报和通知的常规故障排除信息，请参阅[排查 Azure Monitor 警报中的问题](alerts-troubleshoot.md)。