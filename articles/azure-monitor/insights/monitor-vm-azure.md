---
title: 使用 Azure 监视器监视 Azure 虚拟机
description: 介绍如何使用 Azure 监视器从 Azure 中的虚拟机收集和分析监视数据。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283933"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>使用 Azure 监视器监视 Azure 虚拟机
本文介绍如何使用 Azure 监视器收集和分析 Azure 虚拟机的监视数据以维护其运行状况。 虚拟机可以像[任何其他 Azure 资源](monitor-azure-resource.md)一样使用 Azure 监视器监视可用性和性能，但它们与其他资源是唯一的，因为您还需要监视来宾操作系统和系统以及其中运行的工作负载。 

> [!NOTE]
> 本文全面概述了在 Azure 监视器中监视虚拟机的概念和选项。 要快速监视虚拟机而不关注基本概念，请参阅[快速入门：使用 Azure 监视器监视 Azure 虚拟机](../learn/quick-monitor-azure-vm.md)。


## <a name="differences-from-other-azure-resources"></a>与其他 Azure 资源的差异
[使用 Azure 监视器监视 Azure 资源](monitor-azure-resource.md)将介绍 Azure 资源生成的监视数据，以及如何使用 Azure 监视器的功能来分析和警报此数据。 您可以从 Azure 虚拟机收集相同的监视数据并处理具有以下差异的数据：

- [平台指标](../platform/data-platform-metrics.md)自动为虚拟机收集，但仅适用于[虚拟机主机](#monitoring-data)。 您需要一个代理来从来宾操作系统收集性能数据。 
- 虚拟机不生成[资源日志](../platform/platform-logs-overview.md)，这些日志提供对在 Azure 资源中执行的操作的见解。 您可以使用代理从来宾操作系统收集日志数据。
- 可以为虚拟机创建[诊断设置](../platform/diagnostic-settings.md)，将平台指标发送到其他目标（如存储和事件中心），但不能在 Azure 门户中配置这些诊断设置。 

## <a name="monitoring-data"></a>监视数据
Azure 中的虚拟机生成[日志](../platform/data-platform-logs.md)和[指标](../platform/data-platform-metrics.md)，如下所示。

![概述](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>虚拟主机
Azure 中的虚拟机为虚拟机主机生成以下数据，与[监视数据](monitor-azure-resource.md#monitoring-data)中描述的其他 Azure 资源相同。

- [平台指标](../platform/data-platform-metrics.md)- 定期自动收集的数字值，并描述特定时间资源的某些方面。 为虚拟机主机收集平台指标，但您需要诊断扩展来收集来宾操作系统的指标。
- [活动日志](../platform/platform-logs-overview.md)- 从外部（管理平面）提供对订阅中每个 Azure 资源的操作的见解。 对于虚拟机，这包括启动时间以及任何配置更改等信息。


### <a name="guest-operating-system"></a>来宾操作系统
要从虚拟机的来宾操作系统收集数据，需要代理，该代理在每个虚拟机上本地运行并将数据发送到 Azure 监视器。 Azure 监视器可以使用多个代理，每个代理都收集不同的数据并将数据写入不同的位置。 在[Azure 监视器代理的概述](../platform/agents-overview.md)中详细比较不同的代理。 

- [日志分析代理](../platform/agents-overview.md#log-analytics-agent)- 可用于 Azure、其他云环境和本地中的虚拟机。 将数据收集到 Azure 监视器日志。 支持针对 VM 的 Azure 监视器和监视解决方案。 这是用于系统中心操作管理器的相同代理。
- [依赖项代理](../platform/agents-overview.md#dependency-agent)- 收集有关在虚拟机上运行的进程及其依赖项的数据。 依赖日志分析代理将数据传输到 Azure，并支持针对 VM 的 Azure 监视器、服务映射和有线数据 2.0 解决方案。
- [Azure 诊断扩展](../platform/agents-overview.md#azure-diagnostics-extension)- 仅适用于 Azure 监视器虚拟机。 可以将数据收集到多个位置，但主要用于将来宾性能数据收集到 Windows 虚拟机的 Azure 监视器指标中。
- [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)- 从 Linux VM 收集性能数据到 Azure 监视器指标中。


## <a name="configuration-requirements"></a>配置要求
要启用 Azure 监视器的所有功能以监视虚拟机，需要从虚拟机主机和来宾操作系统将监视数据收集到[Azure 监视器指标](../platform/data-platform-logs.md)和[Azure 监视器日志](../platform/data-platform-logs.md)。 下表列出了启用此集合必须执行的配置。 您可以选择不执行所有这些步骤，具体取决于您的特定要求。

| 配置步骤 | 已完成的操作 | 启用功能 |
|:---|:---|:---|
| 无配置 | - 收集到指标的主机平台指标。<br>- 收集活动日志。 | - 主机的指标资源管理器。<br>- 主机的指标警报。<br>- 活动日志警报。 |
| [启用用于 VM 的 Azure Monitor](#enable-azure-monitor-for-vms) | - 已安装日志分析代理。<br>- 已安装依赖项代理。<br>- 将来宾性能数据收集到日志。<br>- 收集到日志的进程和依赖项详细信息。 | - 客户绩效数据的性能图表和工作簿。<br>- 记录来宾性能数据的查询。<br>- 记录来宾性能数据的警报。<br>- 依赖项映射。 |
| [安装诊断扩展和远程广播代理](#enable-diagnostics-extension-and-telegraf-agent) | - 收集到指标的来宾性能数据。 | - 供客人使用指标的资源管理器。<br>- 针对来宾的指标警报。  |
| [配置日志分析工作区](#configure-log-analytics-workspace) | - 从客人那里收集的活动。 | - 记录来宾事件的查询。<br>- 记录来宾事件的警报。 |
| [为虚拟机创建诊断设置](#collect-platform-metrics-and-activity-log) | - 收集到日志的平台指标。<br>- 收集到日志的活动日志。 | - 主机指标的 Loq 查询。<br>- 记录主机指标的警报。<br>- 记录活动日志的查询。

以下各节将介绍每个配置步骤。

### <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor
[VM 的 Azure 监视器](vminsights-overview.md)是 Azure 监视器中的[见解](insights-overview.md)，是监视 Azure 监视器中虚拟机的主要工具。 它提供以下附加值，而不是标准 Azure 监视器功能。

- 简化日志分析代理和依赖项代理的载入，以便监视虚拟机来宾操作系统和工作负载。 
- 预定义的趋势性能图表和工作簿，允许您从虚拟机的来宾操作系统分析核心性能指标。
- 依赖项映射，显示在每个虚拟机上运行的进程，以及与其他计算机和外部源的互连组件。

![用于 VM 的 Azure Monitor](media/monitor-vm-azure/vminsights-01.png)

![用于 VM 的 Azure Monitor](media/monitor-vm-azure/vminsights-02.png)


从 Azure 门户的虚拟机菜单中的 **"见解"** 选项为 VM 启用 Azure 监视器。 有关详细信息和其他配置方法，请参阅[启用 VM 的 Azure 监视器概述](vminsights-enable-overview.md)。

![启用用于 VM 的 Azure Monitor](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>配置日志分析工作区
Azure 监视器用于 VM 的日志分析代理将数据发送到[日志分析工作区](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)。 您可以通过配置日志分析工作区，从代理中收集其他性能数据、事件和其他监视数据。 它只需要配置一次，因为任何连接到工作区的代理都将自动下载配置，并立即开始收集定义的数据。 

您可以通过从 **"开始"** 中选择**工作区配置**，直接从 AZURE 监视器访问工作区的配置。 单击工作区名称以打开其菜单。

![工作区配置](media/monitor-vm-azure/workspace-configuration.png)

从工作区菜单中选择 **"高级设置****"，然后选择"数据**"以配置数据源。 对于 Windows 代理，选择**Windows 事件日志**并添加常见事件日志，如*系统和**应用程序*。 对于 Linux 代理，选择**Syslog**并添加通用设施，如*kern*和*守护进程*。 有关可用数据源的列表以及有关配置数据源的详细信息，请参阅[Azure 监视器中的代理数据源](../platform/agent-data-sources.md)。 

![配置事件](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 您可以将性能计数器配置为从工作区配置中收集，但对于 Azure 监视器为 VM 收集的性能计数器，这可能是多余的。 VM 的 Azure 监视器以每分钟一次的频率收集最常见的计数器集。 仅当要收集 Azure 监视器尚未收集的 VM 尚未收集的计数器或使用性能数据的现有查询时，才配置工作区要收集的性能计数器。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>启用诊断扩展和远程格拉夫代理
VM 的 Azure 监视器基于日志分析代理，该代理将数据收集到日志分析工作区中。 这支持[Azure 监视器的多个功能](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)，如[日志查询](../log-query/log-query-overview.md)、[日志警报](../platform/alerts-log.md)和[工作簿](../platform/workbooks-overview.md)。 [诊断扩展](../platform/diagnostics-extension-overview.md)从 Windows 虚拟机的来宾操作系统收集性能数据到 Azure 存储，并可以选择将性能数据发送到[Azure 监视器指标](../platform/data-platform-metrics.md)。 对于 Linux 虚拟机，[需要 Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)将数据发送到 Azure 指标。  这支持 Azure 监视器的其他功能，如[指标资源管理器](../platform/metrics-getting-started.md)和[指标警报](../platform/alerts-metric.md)。 还可以将诊断扩展配置为使用 Azure 事件中心在 Azure 监视器之外发送事件和性能数据。

从 VM 菜单中的 **"诊断"设置**选项在 Azure 门户中安装单个 Windows 虚拟机的诊断扩展。 在 **"接收器"** 选项卡中选择启用**Azure 监视器**的选项。要从多个虚拟机的模板或命令行启用扩展，请参阅[安装和配置](../platform/diagnostics-extension-overview.md#installation-and-configuration)。 与 Log Analytics 代理不同，要收集的数据在每个虚拟机上的扩展的配置中定义。

![诊断设置](media/monitor-vm-azure/diagnostic-setting.png)

有关在 Linux 虚拟机上配置 Telegraf 代理的详细信息，请参阅[安装和配置 Telegraf。](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) **诊断设置**菜单选项可用于 Linux，但它仅允许您将数据发送到 Azure 存储。

### <a name="collect-platform-metrics-and-activity-log"></a>收集平台指标和活动日志
您可以查看为 Azure 门户中的每个虚拟机主机收集的平台指标和活动日志。 将此数据收集到与 VM Azure 监视器相同的日志分析工作区中，以便使用为虚拟机收集的其他监视数据进行分析。 此集合配置了[诊断设置](../platform/diagnostic-settings.md)。 使用[订阅的诊断设置](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)收集活动日志。

使用虚拟机的诊断设置收集平台指标。 与其他 Azure 资源不同，您不能在 Azure 门户中为虚拟机创建诊断设置，但必须使用[另一种方法](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)。 以下示例演示如何使用 PowerShell 和 CLI 收集虚拟机的指标。

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
为虚拟机配置监视数据收集后，在 Azure 门户中具有多个访问选项：

- 使用**Azure 监视器**菜单访问来自所有受监视资源的数据。 
- 使用 VM 的 Azure 监视器大规模监视虚拟机集。
- 从 Azure 门户中的菜单分析单个虚拟机的数据。 下表列出了监视虚拟机菜单的不同选项。

![在 Azure 门户中监视](media/monitor-vm-azure/monitor-menu.png)

| 菜单选项 | 描述 |
|:---|:---|
| 概述 | 显示虚拟机主机[的平台指标](../platform/data-platform-metrics.md)。 单击图形以在[指标资源管理器](../platform/metrics-getting-started.md)中使用此数据。 |
| 活动日志 | 为当前虚拟机筛选[的活动日志](../platform/activity-log-view.md)条目。 |
| 洞察力 | 打开[Azure 监视器，显示](../insights/vminsights-overview.md)已选择的当前虚拟机的映射。 |
| 警报 | 查看当前虚拟机的[警报](../platform/alerts-overview.md)。  |
| 指标 | 打开[指标资源管理器](../platform/metrics-getting-started.md)，范围设置为当前虚拟机。 |
| 诊断设置 | 为当前虚拟机启用和配置[诊断扩展](../platform/diagnostics-extension-overview.md)。 |
| 顾问建议 | [Azure 顾问](/azure/advisor/)对当前虚拟机的建议。 |
| 日志 | 打开[日志分析](../log-query/log-query-overview.md#what-is-log-analytics)，将[范围](../log-query/scope.md)设置为当前虚拟机。 |
| 连接监视器 | 打开[网络观察程序连接监视器](../../network-watcher/connection-monitor-preview.md)，以监视当前虚拟机和其他虚拟机之间的连接。 |


## <a name="analyzing-metric-data"></a>分析指标数据
您可以通过从虚拟机菜单中打开**指标**来分析使用指标资源管理器的指标。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../platform/metrics-getting-started.md)。 

虚拟机使用两个命名空间用于指标：

| 命名空间 | 描述 |
|:---|:---|
| 虚拟机主机 | 自动收集所有 Azure 虚拟机的主机指标。 微软的详细指标列表[。](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
| 虚拟机来宾 | 从虚拟机收集的来宾操作系统指标，并安装诊断扩展并配置为发送到 Azure 监视器接收器。 |

![指标](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>分析日志数据
Azure 虚拟机将收集以下数据到 Azure 监视器日志。 

VM 的 Azure 监视器支持收集写入*InsightsMetrics*表的预定性能计数器集。 这是[Azure 监视器用于容器的相同](container-insights-overview.md)表。 

| 数据源 | 要求 | 表 |
|:---|:---|:---|
| 用于 VM 的 Azure Monitor | 在每个虚拟机上启用。 | InsightsMetrics<br>VMBundPort<br>虚拟机<br>VMConnection<br>VMProcess<br>有关详细信息[，请参阅如何从 Azure 监视器查询日志以了解 VM。](vminsights-log-search.md) |
| 活动日志 | 订阅的诊断设置。 | AzureActivity |
| 主机指标 | 虚拟机的诊断设置。 | AzureMetrics |
| 来自来宾操作系统的数据源 | 启用日志分析代理并配置数据源。 | 请参阅每个数据源的文档。 |


> [!NOTE]
> 日志分析代理收集的性能数据写入*Perf*表，而 VM 的 Azure 监视器将将其收集到*见解指标*表。 这是相同的数据，但表具有不同的结构。 如果您有基于*Perf*的现有查询，则需要重写该查询才能使用*InsightsMetrics*。


## <a name="alerts"></a>警报
Azure 监视器中的[警报](../platform/alerts-overview.md)会在监视数据中找到重要条件时主动通知您，并可能启动操作，如启动逻辑应用或调用 Webhook。 警报规则定义用于确定何时应创建警报的逻辑。 Azure 监视器收集警报规则使用的数据，但需要创建规则以在 Azure 订阅中定义警报条件。

以下各节介绍警报规则的类型，以及何时应使用每个规则的建议。 此建议基于警报规则类型的功能和成本。 有关警报的详细信息定价，请参阅[Azure 监视器定价](https://azure.microsoft.com/pricing/details/monitor/)。


### <a name="activity-log-alert-rules"></a>活动日志警报规则
在活动日志中创建匹配特定条件的条目时，[活动日志警报规则](../platform/alerts-activity-log.md)将触发。 它们没有成本，因此，如果您所需的逻辑位于活动日志中，它们应该是您的第一选择。 

活动日志警报的目标资源可以是特定虚拟机、资源组中的所有虚拟机或订阅中的所有虚拟机。

例如，如果关键虚拟机停止，通过为信号名称选择 *"关闭虚拟机"* 来创建警报。

![活动日志警报](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>指标警报规则
当指标值超过阈值时，[指标警报规则](../platform/alerts-metric.md)将触发。 可以定义特定的阈值，也可以允许 Azure 监视器根据历史数据动态确定阈值。  尽可能将指标警报与指标数据一起使用，因为它们的成本更低，并且比日志警报规则响应更快。 它们也是有状态的，这意味着当指标低于阈值时，它们会自行解决。

活动日志警报的目标资源可以是特定虚拟机或资源组中的所有虚拟机。

例如，要在虚拟机的处理器超过特定值时创建警报，请使用*百分比 CPU*作为信号类型创建指标警报规则。 设置特定的阈值或允许 Azure 监视器设置动态阈值。 

![指标警报](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>日志警报
当计划日志查询的结果符合特定条件时，[日志警报规则](../platform/alerts-log.md)将触发。 日志查询警报是警报规则中成本最大、响应速度最低的警报，但它们可以访问最多样化的数据，并且可以执行其他警报规则无法执行的复杂逻辑。 

日志查询的目标资源是日志分析工作区。 筛选查询中特定计算机。

例如，要创建检查特定资源组中的任何虚拟机是否脱机的警报，请使用以下查询，该查询返回最近十分钟内丢失检测信号的每台计算机的记录。 使用阈值 1，如果至少有一台计算机的心跳未命中，则该阈值触发。

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![日志警报](media/monitor-vm-azure/log-alert-01.png)

如果要在订阅中的任何 Windows 虚拟机上发生过多失败登录，请创建警报，请使用以下查询，该查询返回过去一小时内每个失败登录事件的记录。 使用设置为允许的失败登录数的阈值。 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![日志警报](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
系统中心操作管理器 （SCOM） 提供虚拟机上工作负载的精细监视。 有关监控平台和不同实施策略的比较，请参阅[云监控指南](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)。

如果现有 SCOM 环境要继续使用，则可以将其与 Azure 监视器集成以提供其他功能。 Azure 监视器使用的日志分析代理与 SCOM 使用的日志分析代理相同，因此已监视虚拟机将数据发送到两者。 您仍然需要将代理添加到 VM 的 Azure 监视器，并将工作区配置为按照上述指定收集其他数据，但虚拟机可以在 SCOM 环境中继续运行其现有管理包，而无需修改。

增强现有 SCOM 功能的 Azure 监视器功能包括：

- 使用日志分析以交互式分析日志和性能数据。
- 使用日志警报定义跨多个虚拟机的警报条件，并使用 SCOM 中的警报无法使用的长期趋势。   

有关将现有 SCOM 管理组连接到日志分析工作区的详细信息，请参阅[将操作管理器连接到 Azure 监视器](../platform/om-agents.md)。


## <a name="next-steps"></a>后续步骤

* [了解如何使用日志查询分析 Azure 监视器日志中的数据。](../log-query/get-started-queries.md)
* [使用 Azure 监视器中的指标和日志了解警报。](../platform/alerts-overview.md)

