---
title: 监视具有 Azure Monitor 的 Azure 虚拟机
description: 介绍如何使用 Azure Monitor 从 Azure 中的虚拟机收集和分析监视数据。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283933"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>监视具有 Azure Monitor 的 Azure 虚拟机
本文介绍如何使用 Azure Monitor 从 Azure 虚拟机收集和分析监视数据以维护其运行状况。 可以监视虚拟机的可用性和性能与任何[其他 Azure 资源](monitor-azure-resource.md)的 Azure Monitor 一样，但它们与其他资源是唯一的，因为你还需要监视来宾操作和系统以及在其中运行的工作负荷。 

> [!NOTE]
> 本文提供用于监视 Azure Monitor 中虚拟机的概念和选项的完整概述。 若要快速开始监视虚拟机而不关注基础概念，请参阅[快速入门：使用 Azure Monitor 监视 Azure 虚拟机](../learn/quick-monitor-azure-vm.md)。


## <a name="differences-from-other-azure-resources"></a>与其他 Azure 资源的差异
[使用 Azure Monitor 监视 azure 资源](monitor-azure-resource.md)介绍 azure 资源生成的监视数据，以及如何使用 Azure Monitor 的功能来分析和警报此数据。 你可以从 Azure 虚拟机收集并处理相同的监视数据，并具有以下差异：

- [平台指标](../platform/data-platform-metrics.md)是针对虚拟机自动收集的，但仅用于[虚拟机主机](#monitoring-data)。 需要一个代理来收集来宾操作系统的性能数据。 
- 虚拟机不会生成[资源日志](../platform/platform-logs-overview.md)，以便深入了解在 Azure 资源中执行的操作。 使用代理从来宾操作系统收集日志数据。
- 你可以为虚拟机创建[诊断设置](../platform/diagnostic-settings.md)，以便将平台指标发送到其他目标（例如存储和事件中心），但无法在 Azure 门户中配置这些诊断设置。 

## <a name="monitoring-data"></a>监视数据
Azure 中 Azure 中的虚拟机生成如下图所示的[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)。

![概述](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>虚拟主机
Azure 中的虚拟机为虚拟机主机生成以下数据，与[监视数据](monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同。

- [平台指标](../platform/data-platform-metrics.md)-自动收集的数值，在特定时间自动收集并描述资源的某个方面。 为虚拟机主机收集平台指标，但需要诊断扩展来收集来宾操作系统的指标。
- [活动日志](../platform/platform-logs-overview.md)-提供对外部（管理平面）订阅中每个 Azure 资源的操作的见解。 对于虚拟机，这包括启动时的信息以及任何配置更改。


### <a name="guest-operating-system"></a>来宾操作系统
若要从虚拟机的来宾操作系统中收集数据，需要一个在每个虚拟机上本地运行的代理，并将数据发送到 Azure Monitor。 每个代理都可用于 Azure Monitor，每个代理收集不同的数据并将数据写入不同位置。 在[Azure Monitor 代理概述](../platform/agents-overview.md)获取不同代理的详细比较。 

- [Log Analytics 代理](../platform/agents-overview.md#log-analytics-agent)-适用于 Azure、其他云环境和本地中的虚拟机。 将数据收集到 Azure Monitor 日志。 支持用于 VM 的 Azure Monitor 和监视解决方案。 这是用于 System Center Operations Manager 的同一代理。
- [依赖关系代理](../platform/agents-overview.md#dependency-agent)-收集有关虚拟机上运行的进程及其依赖项的数据。 依靠 Log Analytics 代理将数据传输到 Azure 中，并支持用于 VM 的 Azure Monitor、服务映射和 Wire Data 2.0 解决方案。
- [Azure 诊断扩展](../platform/agents-overview.md#azure-diagnostics-extension)-仅适用于 Azure Monitor 虚拟机。 可以将数据收集到多个位置，但主要用于将来宾性能数据收集到 Windows 虚拟机 Azure Monitor 度量值中。
- [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) -从 Linux vm 将性能数据收集到 Azure Monitor 指标中。


## <a name="configuration-requirements"></a>配置要求
若要启用 Azure Monitor 的所有功能来监视虚拟机，需要收集从虚拟机主机和来宾操作系统到[Azure Monitor 指标](../platform/data-platform-logs.md)和[Azure Monitor 日志](../platform/data-platform-logs.md)的监视数据。 下表列出了启用此集合时必须执行的配置。 你可以选择不执行所有这些步骤，具体取决于你的特定要求。

| 配置步骤 | 操作已完成 | 已启用功能 |
|:---|:---|:---|
| 无配置 | -收集到指标的主机平台指标。<br>-已收集活动日志。 | -主机的指标资源管理器。<br>-主机的指标警报。<br>-活动日志警报。 |
| [启用用于 VM 的 Azure Monitor](#enable-azure-monitor-for-vms) | -Log Analytics 已安装代理。<br>-已安装依赖关系代理。<br>-收集到日志的来宾性能数据。<br>-收集到日志的进程和依赖项详细信息。 | -性能图表和来宾性能数据的工作簿。<br>-对来宾性能数据的日志查询。<br>-针对来宾性能数据的日志警报。<br>-依赖关系映射。 |
| [安装诊断扩展和 telegraf 代理](#enable-diagnostics-extension-and-telegraf-agent) | -收集到指标的来宾性能数据。 | -来宾的指标资源管理器。<br>-来宾的指标警报。  |
| [配置 Log Analytics 工作区](#configure-log-analytics-workspace) | -从来宾收集的事件。 | -记录来宾事件的查询。<br>-为来宾事件记录警报。 |
| [为虚拟机创建诊断设置](#collect-platform-metrics-and-activity-log) | -收集到日志的平台指标。<br>-收集到日志的活动日志。 | -主机指标的 Loq 查询。<br>-主机指标的日志警报。<br>-对活动日志的日志查询。

以下各部分介绍了其中的每个配置步骤。

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor
[用于 VM 的 Azure Monitor](vminsights-overview.md)是用于监视 Azure Monitor 中虚拟机的主要工具 Azure Monitor 的[见解](insights-overview.md)。 它为标准 Azure Monitor 功能提供了以下附加价值。

- 简化 Log Analytics 代理和依赖项代理的载入，以启用虚拟机来宾操作系统和工作负载的监视。 
- 预定义的趋势性能图表和工作簿，可用于分析虚拟机的来宾操作系统中的核心性能指标。
- 用于显示在每个虚拟机上运行的进程以及与其他计算机和外部源关联的组件的依赖关系映射。

![用于 VM 的 Azure Monitor](media/monitor-vm-azure/vminsights-01.png)

![用于 VM 的 Azure Monitor](media/monitor-vm-azure/vminsights-02.png)


从 Azure 门户的 "虚拟机" 菜单中的 " **Insights** " 选项启用用于 VM 的 Azure Monitor。 请参阅[Enable 用于 VM 的 Azure Monitor 概述](vminsights-enable-overview.md)获取详细信息和其他配置方法。

![启用用于 VM 的 Azure Monitor](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>配置 Log Analytics 工作区
用于 VM 的 Azure Monitor 使用的 Log Analytics 代理将数据发送到[Log Analytics 工作区](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)。 可以通过配置 "Log Analytics" 工作区，从代理中启用其他性能数据、事件和其他监视数据的收集。 只需配置一次，因为任何连接到工作区的代理都将自动下载配置并立即开始收集定义的数据。 

通过从 "**入门**" 中选择 "**工作区配置**"，可以直接从用于 VM 的 Azure Monitor 访问工作区的配置。 单击工作区名称以打开其菜单。

![工作区配置](media/monitor-vm-azure/workspace-configuration.png)

从 "工作区" 菜单中选择 "**高级设置**"，然后选择 "**数据**" 以配置数据源。 对于 Windows 代理，请选择 " **Windows 事件日志**"，并添加常见事件日志，例如*系统*和*应用程序*。 对于 Linux 代理，请选择**Syslog**并添加常用设施，如*字偶间距*和*守护*程序。 有关可用数据源的列表和配置这些数据源的详细信息，请参阅[中的代理数据源 Azure Monitor](../platform/agent-data-sources.md) 。 

![配置事件](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 你可以配置要从工作区配置收集的性能计数器，但这可能对用于 VM 的 Azure Monitor 收集的性能计数器是冗余的。 用于 VM 的 Azure Monitor 收集每分钟一次最常见的计数器集。 如果你想要收集用于 VM 的 Azure Monitor 收集的计数器，或者如果你有使用性能数据的现有查询，则只需配置工作区要收集的性能计数器。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>启用诊断扩展和 Telegraf 代理
用于 VM 的 Azure Monitor 基于将数据收集到 Log Analytics 工作区的 Log Analytics 代理。 这支持[多种 Azure Monitor 功能](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)，如[日志查询](../log-query/log-query-overview.md)、[日志警报](../platform/alerts-log.md)和[工作簿](../platform/workbooks-overview.md)。 [诊断扩展](../platform/diagnostics-extension-overview.md)会将 Windows 虚拟机的来宾操作系统中的性能数据收集到 Azure 存储，并根据需要将性能数据发送到[Azure Monitor 指标](../platform/data-platform-metrics.md)。 对于 Linux 虚拟机，需要[Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)才能将数据发送到 Azure 指标。  这将启用 "[指标资源管理器](../platform/metrics-getting-started.md)" 和 "[指标警报](../platform/alerts-metric.md)" 等 Azure Monitor 的其他功能。 你还可以将诊断扩展配置为使用 Azure 事件中心在 Azure Monitor 之外发送事件和性能数据。

在 "VM" 菜单的 "**诊断设置**" 选项中，为 Azure 门户中的单个 Windows 虚拟机安装诊断扩展。 选择用于在 "**接收器**" 选项卡中启用**Azure Monitor**的选项。若要为多个虚拟机启用模板或命令行中的扩展，请参阅[安装和配置](../platform/diagnostics-extension-overview.md#installation-and-configuration)。 与 Log Analytics 代理不同，要收集的数据在每个虚拟机上的扩展的配置中定义。

![诊断设置](media/monitor-vm-azure/diagnostic-setting.png)

有关在 Linux 虚拟机上配置 Telegraf 代理的详细信息，请参阅[安装和配置 Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) 。 **诊断设置**菜单选项适用于 Linux，但它只允许你将数据发送到 Azure 存储。

### <a name="collect-platform-metrics-and-activity-log"></a>收集平台指标和活动日志
你可以查看为 Azure 门户中的每个虚拟主机收集的平台指标和活动日志。 将此数据收集到与用于 VM 的 Azure Monitor 相同的 Log Analytics 工作区中，以便通过为虚拟机收集的其他监视数据对其进行分析。 此集合是使用[诊断设置](../platform/diagnostic-settings.md)配置的。 使用[订阅的诊断设置](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)收集活动日志。

使用虚拟机的诊断设置收集平台指标。 与其他 Azure 资源不同，你无法在 Azure 门户中为虚拟机创建诊断设置，但必须使用[其他方法](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)。 以下示例演示如何使用 PowerShell 和 CLI 收集虚拟机的指标。

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>在 Azure 门户中监视 
为虚拟机配置监视数据收集后，可以在 Azure 门户中使用多个选项进行访问：

- 使用 " **Azure Monitor** " 菜单访问所有被监视资源中的数据。 
- 使用用于 VM 的 Azure Monitor 大规模监视虚拟机集。
- 从 Azure 门户中的菜单分析单个虚拟机的数据。 下表列出了用于监视虚拟机菜单的不同选项。

![在 Azure 门户中监视](media/monitor-vm-azure/monitor-menu.png)

| 菜单选项 | 说明 |
|:---|:---|
| 概述 | 显示虚拟主机的[平台指标](../platform/data-platform-metrics.md)。 单击图形可在[指标资源管理器](../platform/metrics-getting-started.md)中使用此数据。 |
| 活动日志 | 为当前虚拟机筛选的[活动日志](../platform/activity-log-view.md)条目。 |
| 洞察力 | 打开[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) ，其中包含当前所选虚拟机的地图。 |
| 警报 | 查看当前虚拟机的[警报](../platform/alerts-overview.md)。  |
| 指标 | 打开[指标资源管理器](../platform/metrics-getting-started.md)，并将作用域设置为当前的虚拟机。 |
| 诊断设置 | 为当前虚拟机启用和配置[诊断扩展](../platform/diagnostics-extension-overview.md)。 |
| 顾问建议 | 从[Azure Advisor](/azure/advisor/)获取当前虚拟机的建议。 |
| 日志 | 打开[Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) ，并将 "[范围](../log-query/scope.md)" 设置为 "当前虚拟机"。 |
| 连接监视器 | 打开 "[网络观察程序连接监视器](../../network-watcher/connection-monitor-preview.md)" 来监视当前虚拟机和其他虚拟机之间的连接。 |


## <a name="analyzing-metric-data"></a>分析指标数据
可以通过在虚拟机的菜单中打开**指标**来使用指标资源管理器分析虚拟机的指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../platform/metrics-getting-started.md)。 

虚拟机为指标使用了两个命名空间：

| 命名空间 | 说明 |
|:---|:---|
| 虚拟机主机 | 为所有 Azure 虚拟机自动收集的主机指标。 Microsoft 计算指标的详细列表。[计算/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines)。 |
| 虚拟机来宾 | 从安装了诊断扩展并配置为发送到 Azure Monitor 接收器的虚拟机收集的来宾操作系统指标。 |

![指标](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>分析日志数据
Azure 虚拟机将收集以下数据以 Azure Monitor 日志。 

用于 VM 的 Azure Monitor 允许收集一组预定义的性能计数器，这些计数器写入*InsightsMetrics*表。 这与[Azure Monitor 容器](container-insights-overview.md)使用的表相同。 

| 数据源 | 要求 | 表 |
|:---|:---|:---|
| 用于 VM 的 Azure Monitor | 启用每个虚拟机。 | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>有关详细信息，请参阅[如何从用于 VM 的 Azure Monitor 查询日志](vminsights-log-search.md)。 |
| 活动日志 | 订阅的诊断设置。 | AzureActivity |
| 主机度量值 | 虚拟机的诊断设置。 | AzureMetrics |
| 来宾操作系统中的数据源 | 启用 Log Analytics 代理并配置数据源。 | 请参阅每个数据源的文档。 |


> [!NOTE]
> Log Analytics 代理收集的性能数据将写入*Perf*表，用于 VM 的 Azure Monitor 会将其收集到*InsightsMetrics*表中。 这是相同的数据，但表具有不同的结构。 如果现有查询基于*Perf*，则需要重新编写才能使用*InsightsMetrics*。


## <a name="alerts"></a>警报
当在监视数据中发现重要情况并可能启动一个操作（如启动逻辑应用或调用 webhook）时，Azure Monitor 中的[警报](../platform/alerts-overview.md)会主动通知你。 警报规则定义用于确定何时应创建警报的逻辑。 Azure Monitor 收集警报规则使用的数据，但需要创建规则以定义 Azure 订阅中的警报条件。

以下各节描述了在应使用每个规则时的警报规则的类型和建议。 此建议基于预警规则类型的功能和成本。 有关警报的详细信息，请参阅[定价 Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)。


### <a name="activity-log-alert-rules"></a>活动日志警报规则
在活动日志中创建符合特定条件的条目时，将触发[活动日志警报规则](../platform/alerts-activity-log.md)。 它们不会产生任何费用，因此，如果所需的逻辑在活动日志中，则应该是第一个选择。 

活动日志警报的目标资源可以是特定的虚拟机、资源组中的所有虚拟机或订阅中的所有虚拟机。

例如，如果已停止关键虚拟机，请选择 "*关闭电源" 虚拟机*以获取信号名称，从而创建一个警报。

![活动日志警报](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>指标警报规则
指标值超过阈值时，将触发[指标警报规则](../platform/alerts-metric.md)。 您可以定义特定阈值，或允许 Azure Monitor 基于历史数据动态确定阈值。  请尽可能使用指标数据，因为它们的开销较低，并且比日志警报规则更快。 它们也是有状态的，当指标低于阈值时，它们会自行解决。

活动日志警报的目标资源可以是特定虚拟机，也可以是资源组中的所有虚拟机。

例如，若要在虚拟机的处理器超过特定值时创建警报，请使用*CPU 百分比*作为信号类型来创建指标警报规则。 设置特定阈值或允许 Azure Monitor 设置动态阈值。 

![指标警报](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>日志警报
当计划的日志查询的结果与特定的条件匹配时，将触发[日志警报规则](../platform/alerts-log.md)。 日志查询警报是警报规则最昂贵且最不响应的响应，但他们有权访问最不同的数据，并且可以执行其他警报规则无法执行的复杂逻辑。 

日志查询的目标资源是 Log Analytics 工作区。 筛选查询中的特定计算机。

例如，若要创建一个警报来检查特定资源组中的任何虚拟机是否处于脱机状态，请使用以下查询，该查询将为过去十分钟内丢失检测信号的每台计算机返回一条记录。 如果至少有一台计算机具有丢失的检测信号，则使用的阈值为1。

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![日志警报](media/monitor-vm-azure/log-alert-01.png)

若要在订阅中的任何 Windows 虚拟机上出现过多的失败登录数的情况下创建警报，请使用以下查询，该查询将为过去一小时的每个失败的登录事件返回一条记录。 使用设置为将允许的失败登录数的阈值。 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![日志警报](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager （SCOM）提供对虚拟机上的工作负荷的精细监视。 有关监视平台和实现的不同策略的比较，请参阅[云监视指南](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)。

如果你有打算继续使用的现有 SCOM 环境，则可将其与 Azure Monitor 集成，以提供其他功能。 Azure Monitor 使用的 Log Analytics 代理与 SCOM 使用的代理相同，因此，你已监视虚拟机将数据发送到这两个虚拟机。 你仍需要将代理添加到用于 VM 的 Azure Monitor 并配置工作区以按上面所指定的方式收集额外数据，但虚拟机可以继续在 SCOM 环境中运行其现有管理包，而无需修改。

增强现有 SCOM 功能的 Azure Monitor 功能包括：

- 使用 Log Analytics 以交互方式分析日志和性能数据。
- 使用日志警报来定义跨多个虚拟机的警报条件，并使用不能使用 SCOM 中的警报的长期趋势。   

有关将现有 SCOM 管理组连接到 Log Analytics 工作区的详细信息，请参阅[连接 Operations Manager 到 Azure Monitor](../platform/om-agents.md) 。


## <a name="next-steps"></a>后续步骤

* [了解如何使用日志查询分析 Azure Monitor 日志中的数据。](../log-query/get-started-queries.md)
* [了解有关在 Azure Monitor 中使用指标和日志的警报。](../platform/alerts-overview.md)

