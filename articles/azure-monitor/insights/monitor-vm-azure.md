---
title: 使用 Azure Monitor 监视 Azure 虚拟机
description: 介绍如何使用 Azure Monitor 从 Azure 虚拟机中收集和分析监视数据。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 85c4807d5bf71078e3cfb26bbc27e9eecc10c041
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90029455"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure 虚拟机
本文介绍如何使用 Azure Monitor 从 Azure 虚拟机收集和分析监视数据以维护其运行状况。 可以使用 Azure Monitor 监视虚拟机的可用性和性能，就像监视任何[其他 Azure 资源](monitor-azure-resource.md)一样，但是它们与其他资源不同，因为你还需要监视客户操作和系统以及在其中运行的工作负载。 

> [!NOTE]
> 本文全面概述了用于监视 Azure Monitor 虚拟机的概念和选项。 若要快速开始监视虚拟机而不关注基础概念，请参阅[快速入门：使用 Azure Monitor 监视 Azure 虚拟机](../learn/quick-monitor-azure-vm.md)。


## <a name="differences-from-other-azure-resources"></a>与其他 Azure 资源的差异
[使用 Azure Monitor 监视 Azure 资源](monitor-azure-resource.md)介绍 Azure 资源生成的监视数据，以及如何使用 Azure Monitor 的功能在此数据的基础上进行分析和发出警报。 可以从 Azure 虚拟机收集并处理相同的监视数据，但存在以下差异：

-  会为虚拟机自动收集[平台指标](../platform/data-platform-metrics.md)，但仅适用于[虚拟主机](#monitoring-data)。 需要一个代理来收集来宾操作系统的性能数据。 
- 虚拟机不会生成[资源日志](../platform/platform-logs-overview.md)，后者提供对 Azure 资源中执行的操作的见解。 使用代理从来宾操作系统收集日志数据。
- 可以为虚拟机创建[诊断设置](../platform/diagnostic-settings.md)，以将平台指标发送到其他目标（例如存储和事件中心），但无法在 Azure 门户中配置这些诊断设置。 

## <a name="monitoring-data"></a>监视数据
Azure 中的虚拟机生成 [日志](../platform/data-platform-logs.md) 和 [度量值](../platform/data-platform-metrics.md) ，如下图所示。

![概述](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>虚拟主机
Azure 中的虚拟机为虚拟主机生成以下与其他 Azure 资源相同的数据，如[监视数据](monitor-azure-resource.md#monitoring-data)中所述。

- [平台指标](../platform/data-platform-metrics.md) - 定期自动收集的数值，并在特定时间描述资源的某个方面。 平台指标是为虚拟主机收集的，但你需要使用诊断扩展来收集来宾操作系统的指标。
- [活动日志](../platform/platform-logs-overview.md) - 提供对外部（管理平台）订阅中针对各个 Azure 资源操作的见解。 对于虚拟机，这包括启动时间和任何配置更改等信息。


### <a name="guest-operating-system"></a>来宾操作系统
若要从虚拟机的来宾操作系统中收集数据，需要在每台虚拟机上本地运行的代理，并将数据发送到 Azure Monitor。 Azure Monitor 可以有多个代理，每个代理收集不同的数据并将数据写入不同位置。 在 [Azure Monitor 代理概述](../platform/agents-overview.md)中获取不同代理的详细比较。 

- [Log Analytics 代理](../platform/agents-overview.md#log-analytics-agent) - 适用于 Azure、其他云环境和本地中的虚拟机。 将数据收集到 Azure Monitor 日志。 支持用于 VM 的 Azure Monitor 和监视解决方案。 这是用于 System Center Operations Manager 的同一代理。
- [Dependency Agent](../platform/agents-overview.md#dependency-agent) - 收集有关虚拟机上运行的进程及其依赖项的数据。 依靠 Log Analytics 代理将数据传输到 Azure 中，并支持用于 VM 的 Azure Monitor、服务映射和 Wire Data 2.0 解决方案。
- [Azure 诊断扩展](../platform/agents-overview.md#azure-diagnostics-extension) - 仅适用于 Azure Monitor 虚拟机。 可以将数据收集到多个位置，但主要用于将来宾性能数据收集到 Windows 虚拟机的 Azure Monitor 指标中。
- [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md) - 从 Linux VM 将性能数据收集到 Azure Monitor 指标。


## <a name="configuration-requirements"></a>配置要求
若要启用 Azure Monitor 的所有功能来监视虚拟机，需要从虚拟主机和来宾操作系统收集监视数据到 [Azure Monitor 指标](../platform/data-platform-logs.md)和 [Azure Monitor 日志](../platform/data-platform-logs.md)。 下表列出了启用此收集时必须执行的配置。 可选择执行部分步骤，具体取决于你的特定要求。

| 配置步骤 | 完成的操作 | 启用的功能 |
|:---|:---|:---|
| 无配置 | - 向指标收集的主机平台指标。<br>- 收集的活动日志。 | - 主机的指标资源管理器。<br>- 主机的指标警报。<br>- 活动日志警报。 |
| [启用用于 VM 的 Azure Monitor](#enable-azure-monitor-for-vms) | - 已安装 Log Analytics 代理。<br>- 已安装 Dependency Agent。<br>- 向日志收集的来宾性能数据。<br>- 向日志收集的进程和依赖项详细信息。 | - 来宾性能数据的性能图表和工作簿。<br>- 来宾性能数据的日志查询。<br>- 来宾性能数据的日志警报。<br>- 依赖项映射。 |
| [安装诊断扩展和 telegraf 代理](#enable-diagnostics-extension-and-telegraf-agent) | - 向指标收集的来宾性能数据。 | - 来宾的指标资源管理器。<br>- 来宾的指标警报。  |
| [配置 Log Analytics 工作区](#configure-log-analytics-workspace) | - 从来宾收集的事件。 | - 来宾事件的日志查询。<br>- 来宾事件的日志警报。 |
| [为虚拟机创建诊断设置](#collect-platform-metrics-and-activity-log) | - 向日志收集的平台指标。<br>- 向日志收集的活动日志。 | -记录主机指标的查询。<br>- 主机指标的日志警报。<br>- 活动日志的日志查询。

后续部分将介绍其中每个配置步骤。

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor
[用于 VM 的 Azure Monitor](vminsights-overview.md) 是 Azure Monitor 中用于监视虚拟机主要工具的[见解](insights-overview.md)。 与标准 Azure Monitor 功能相比，它提供了以下附加价值。

- 简化 Log Analytics 代理和 Dependency Agent 的载入，以启用对虚拟机来宾操作系统和工作负载的监视。 
- 预定义的趋势性能图表和工作簿，可用于分析虚拟机来宾操作系统中的核心性能指标。
- 提供了依赖项映射，用于显示在每个虚拟机上运行的进程，以及与其他计算机和外部源关联的组件。

![用于 VM 的 Azure Monitor 性能视图](media/monitor-vm-azure/vminsights-01.png)

![用于 VM 的 Azure Monitor 地图视图](media/monitor-vm-azure/vminsights-02.png)


在 Azure 门户的“虚拟机”菜单中，从“见解”选项启用用于 VM 的 Azure Monitor。 有关详细信息和其他配置方法，请参阅[启用用于 VM 的 Azure Monitor 概述](vminsights-enable-overview.md)。

![启用用于 VM 的 Azure Monitor](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>配置 Log Analytics 工作区
用于 VM 的 Azure Monitor 使用的 Log Analytics 代理会将数据发送到 [Log Analytics 工作区](../platform/data-platform-logs.md)。 可以通过配置 Log Analytics 工作区，启用从代理中收集其他性能数据、事件及其他监视数据。 只需配置一次，因为任何连接到工作区的代理都将自动下载配置并立即开始收集定义的数据。 

通过从“入门”中选择“工作区配置”，可以直接从用于 VM 的 Azure Monitor 访问工作区配置。 单击工作区名称以打开菜单。

![工作区配置](media/monitor-vm-azure/workspace-configuration.png)

从工作区菜单中依次选择“高级设置”、“数据”来配置数据源。 对于 Windows 代理，选择“Windows 事件日志”并添加常见事件日志，如“系统”和“应用程序”。 对于 Linux 代理，请选择“Syslog”并添加常见功能，如“字距调整”和“守护程序”。 有关可用数据源的列表和配置这些数据源的详细信息，请参阅 [Azure Monitor 中的代理数据源](../platform/agent-data-sources.md)。 

![配置事件](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 可以配置要从工作区配置中收集的性能计数器，但这对于用于 VM 的 Azure Monitor 收集的性能计数器而言可能是多余的。 用于 VM 的 Azure Monitor 以每分钟一次的频率收集最常见的计数器集。 如果你想要收集用于 VM 的 Azure Monitor 尚未收集的计数器，或者如果你有使用性能数据的现有查询，则只需配置工作区要收集的性能计数器。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>启用诊断扩展和 Telegraf 代理
用于 VM 的 Azure Monitor 基于将数据发送到 Log Analytics 工作区的 Log Analytics 代理。 这支持 Azure Monitor 的多项功能，例如[日志查询](../log-query/log-query-overview.md)、[日志警报](../platform/alerts-log.md)和[工作簿](../platform/workbooks-overview.md)。 [诊断扩展](../platform/diagnostics-extension-overview.md)将 Windows 虚拟机来宾操作系统中的性能数据收集到 Azure 存储，并选择性地将性能数据发送到 [Azure Monitor 指标](../platform/data-platform-metrics.md)。 对于 Linux 虚拟机，需要 [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)才能将数据发送到 Azure 指标。  这将启用 Azure Monitor 的其他功能，如[指标资源管理器](../platform/metrics-getting-started.md)和[指标警报](../platform/alerts-metric.md)。 还可以将诊断扩展配置为使用 Azure 事件中心在 Azure Monitor 之外发送事件和性能数据。

在 VM 菜单的“诊断设置”选项中，为 Azure 门户中的单个 Windows 虚拟机安装诊断扩展。 选择该选项可在“接收器”选项卡中启用“Azure Monitor”。若要为多个虚拟机启用模板或命令行中的扩展，请参阅[安装和配置](../platform/diagnostics-extension-overview.md#installation-and-configuration)。 与 Log Analytics 代理不同，要收集的数据在每个虚拟机上的扩展配置中定义。

![诊断设置](media/monitor-vm-azure/diagnostic-setting.png)

有关在 Linux 虚拟机上配置 Telegraf 代理的详细信息，请参阅[安装和配置 Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf)。 “诊断设置”菜单选项适用于 Linux，但它只允许你将数据发送到 Azure 存储。

### <a name="collect-platform-metrics-and-activity-log"></a>收集平台指标和活动日志
你可以查看为 Azure 门户中的每个虚拟主机收集的平台指标和活动日志。 将此数据收集到与用于 VM 的 Azure Monitor 相同的 Log Analytics 工作区中，以便与为虚拟机收集的其他监视数据一起进行分析。 此收集配置了[诊断设置](../platform/diagnostic-settings.md)。 使用[订阅的诊断设置](../platform/diagnostic-settings.md#create-in-azure-portal)收集活动日志。

使用虚拟机的诊断设置收集平台指标。 与其他 Azure 资源不同，你无法在 Azure 门户中为虚拟机创建诊断设置，但必须使用[其他方法](../platform/diagnostic-settings.md#create-using-powershell)。 以下示例演示如何使用 PowerShell 和 CLI 收集虚拟机指标。

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>在 Azure 门户中监视 
为虚拟机配置收集监视数据后，可以在 Azure 门户中使用多个选项进行访问：

- 使用“Azure Monitor”菜单来访问所有受监视资源中的数据。 
- 使用用于 VM 的 Azure Monitor 来大规模监视虚拟机集。
- 从 Azure 门户的菜单中分析单个虚拟机的数据。 下表列出了用于监视虚拟机菜单的不同选项。

![在 Azure 门户中监视](media/monitor-vm-azure/monitor-menu.png)

| 菜单选项 | 说明 |
|:---|:---|
| 概述 | 显示虚拟主机的[平台指标](../platform/data-platform-metrics.md)。 单击图形可在[指标资源管理器](../platform/metrics-getting-started.md)中处理此数据。 |
| 活动日志 | 为当前虚拟机筛选的[活动日志](../platform/activity-log.md#view-the-activity-log)条目。 |
| 洞察力 | 打开[用于 VM 的 Azure Monitor](./vminsights-overview.md)，其中包含当前选定的虚拟机映射。 |
| 警报 | 查看当前虚拟机[警报](../platform/alerts-overview.md)。  |
| 指标 | 打开[指标资源管理器](../platform/metrics-getting-started.md)，并将“范围”设置为“当前虚拟机”。 |
| 诊断设置 | 为当前虚拟机启用和配置[诊断扩展](../platform/diagnostics-extension-overview.md)。 |
| 顾问建议 | [Azure 顾问](../../advisor/index.yml)就当前虚拟机所提供的建议。 |
| 日志 | 打开 [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics)，并将[范围](../log-query/scope.md)设置为“当前虚拟机”。 |
| 连接监视器 | 打开[网络观察程序连接监视器](../../network-watcher/connection-monitor-preview.md)，以监视当前虚拟机和其他虚拟机之间的连接。 |


## <a name="analyzing-metric-data"></a>分析指标数据
可以通过在虚拟机的菜单中打开“指标”，使用指标资源管理器分析虚拟机指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../platform/metrics-getting-started.md)。 

虚拟机有三个用于指标的命名空间：

| 命名空间 | 说明 | 要求 |
|:---|:---|:---|
| 虚拟主机 | 为所有 Azure 虚拟机自动收集的主机指标。 有关详细指标列表，请参见 [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines)。 | 自动收集，无需任何配置。 |
| 来宾(经典) | 一组有限的来宾操作系统和应用程序性能数据。 在指标资源管理器中可用，但不能用于其他 Azure Monitor 功能（如指标警报）。  | 已安装[诊断扩展](../platform/diagnostics-extension-overview.md)。 从 Azure 存储读取数据。  |
| 虚拟机来宾 | 使用指标的所有 Azure Monitor 功能都可以使用来宾操作系统和应用程序性能数据。 | 对于 Windows，启用与 Azure Monitor 接收器一起安装的[已安装诊断扩展](../platform/diagnostics-extension-overview.md)。 对于 Linux，则启用[已安装的 Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)。 |

![Azure 门户中的指标资源管理器](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>分析日志数据
Azure 虚拟机将收集以下数据到 Azure Monitor 日志。 

用于 VM 的 Azure Monitor 允许收集一组预先确定的性能计数器，这些计数器写入到“InsightsMetrics”表中。 这是[适用于容器的 Azure Monitor](container-insights-overview.md) 所使用的同一个表。 

| 数据源 | 要求 | 表 |
|:---|:---|:---|
| 用于 VM 的 Azure Monitor | 在每个虚拟机上启用。 | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>有关详细信息，请参阅[如何从用于 VM 的 Azure Monitor 查询日志](vminsights-log-search.md)。 |
| 活动日志 | 订阅的诊断设置。 | AzureActivity |
| 主机指标 | 虚拟机的诊断设置。 | AzureMetrics |
| 来宾操作系统中的数据源 | 启用 Log Analytics 代理并配置数据源。 | 请参阅文档了解每个数据源。 |


> [!NOTE]
> Log Analytics 代理收集的性能数据会写入“Perf”表中，而用于 VM 的 Azure Monitor 会将其收集到“InsightsMetrics”表中。 这是相同的数据，但表具有不同的结构。 如果现有查询基于“Perf”，则需要重新编写才能使用“InsightsMetrics”。


## <a name="alerts"></a>警报
Azure Monitor 中的[警报](../platform/alerts-overview.md)会在监视数据中发现重要条件时主动通知你，并且可能会启动一个操作，如启动逻辑应用或调用 Webhook。 警报规则定义用于确定何时应创建警报的逻辑。 Azure Monitor 收集警报规则使用的数据，但需要创建规则以定义 Azure 订阅中的警报条件。

以下各节描述了警报规则的类型，以及关于应何时使用各规则的建议。 此建议基于警报规则类型的功能和成本。 有关警报的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。


### <a name="activity-log-alert-rules"></a>活动日志警报规则
在活动日志中创建符合特定条件的条目时，将触发[活动日志警报规则](../platform/alerts-activity-log.md)。 它们不会产生任何费用，因此，如果所需的逻辑在活动日志中，它们应是你的首选。 

活动日志警报的目标资源可以是特定的虚拟机、资源组中的所有虚拟机或订阅中的所有虚拟机。

例如，如果要在一台关键虚拟机停止时创建警报，可以选择“关闭虚拟机”作为“信号名称”。

![活动日志警报](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>指标警报规则
指标值超过阈值时，将触发[指标警报规则](../platform/alerts-metric.md)。 你可以定义特定阈值，或允许 Azure Monitor 基于历史数据动态确定阈值。  请尽可能对指标数据使用指标警报，因为它们的成本较低，并且比日志警报规则响应更快。 它们也是有状态的，这意味着它们将在指标下降到低于阈值时自行解析。

活动日志警报的目标资源可以是特定的虚拟机，或资源组中的所有虚拟机。

例如，若要在虚拟机的处理器超过特定值时创建警报，请使用“百分比 CPU”作为信号类型来创建指标警报规则。 设置特定阈值或允许 Azure Monitor 设置动态阈值。 

![指标警报](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>日志警报
当计划的日志查询的结果与特定条件匹配时，将触发[日志警报规则](../platform/alerts-log.md)。 日志查询警报是最昂贵且响应率最低的警报规则，但它们有权访问最不同的数据，并且可以执行其他警报规则无法执行的复杂逻辑。 

日志查询的目标资源是 Log Analytics 工作区。 筛选查询中的特定计算机。

例如，若要创建一个警报来检查特定资源组中的任何虚拟机是否处于脱机状态，请使用以下查询，该查询将为过去十分钟内丢失检测信号的每台计算机返回一条记录。 如果至少有一台计算机丢失检测信号，则使用阈值 1。

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![丢失的检测信号的日志警报](media/monitor-vm-azure/log-alert-01.png)

如果订阅中的任何 Windows 虚拟机上出现过多次失败登录，要创建警报，请使用以下查询，该查询将为过去一小时的每个登录失败事件返回一条记录。 使用一个设置为允许的失败登录次数的阈值。 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![失败登录的日志警报](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager 提供对虚拟机上的工作负荷的精细监视。 请参阅[云监视指南](/azure/cloud-adoption-framework/manage/monitor/)，以比较监视平台和不同策略的实现。

如果你有打算继续使用的现有 Operations Manager 环境，则可将其与 Azure Monitor 集成以提供其他功能。 Azure Monitor 使用的 Log Analytics 代理与用于 Operations Manager 的代理相同，因此你已监视虚拟机将数据发送到这两个。 你仍需要将代理添加到用于 VM 的 Azure Monitor 并配置工作区以按上面所指定的方式收集额外数据，但虚拟机可以继续在 Operations Manager 环境中运行其现有管理包，而无需修改。

增强现有 Operations Manager 功能的 Azure Monitor 功能包括：

- 使用 Log Analytics 以交互方式分析日志和性能数据。
- 使用日志警报来定义跨多个虚拟机的警报条件，并使用不能使用 Operations Manager 中的警报的长期趋势。   

有关将现有 Operations Manager 管理组连接到 Log Analytics 工作区的详细信息，请参阅 [连接 Operations Manager 到 Azure Monitor](../platform/om-agents.md) 。


## <a name="next-steps"></a>后续步骤

* [了解如何使用日志查询分析 Azure Monitor 日志中的数据。](../log-query/get-started-queries.md)
* [了解 Azure Monitor 中使用指标和日志的警报。](../platform/alerts-overview.md)

