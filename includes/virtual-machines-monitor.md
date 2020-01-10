---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752412"
---
随着在 Azure 中托管的 Vm 的巨大增长，确定影响应用程序和基础结构服务所支持的性能和运行状况问题非常重要。 默认情况下，基本监视按指标类型 "CPU 使用率"、"磁盘利用率"、"内存使用率" 和主机虚拟机监控程序收集的网络流量一起提供。 可以使用[扩展](../articles/virtual-machines/windows/extensions-features.md)来收集其他指标和日志数据，以在来宾操作系统中为 vm 配置诊断。

若要检测和帮助诊断在 VM 内运行的来宾操作系统、基于 .NET 或 Java web 应用程序组件的性能和运行状况问题，Azure Monitor 通过综合功能提供集中式监视，如用于 VM 的 Azure Monitor和 Application Insights。

## <a name="diagnostics-and-metrics"></a>诊断和指标 

可以在 Azure 门户、Azure CLI、Azure PowerShell 和编程应用程序编程接口 (API) 中使用[指标](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)来设置和监视[诊断数据](https://docs.microsoft.com/cli/azure/vm/diagnostics)收集。 例如，可以：

- **观察 VM 的基本指标。** Azure 门户的“概述”屏幕上显示的基本指标包括 CPU 使用率、网络使用情况、总磁盘字节数以及每秒的磁盘操作数。

- **使用 Azure 门户启用并查看启动诊断数据收集。** 将自己的映像加载到 Azure 或者启动某个平台映像时，可能会因为许多原因而导致 VM 进入无法启动状态。 创建 VM 时，针对“设置”屏幕的“监视”部分下的“启动诊断”单击“已启用”，即可轻松启用启动诊断。

    VM 启动时，启动诊断代理将捕获启动输出并将其存储在 Azure 存储中。 此数据可以用于排查 VM 启动问题。 从命令行工具创建 VM 时，不会自动启用启动诊断。 在启用启动诊断之前，需要创建一个存储帐户来存储启动日志。 如果在 Azure 门户中启用启动诊断，则会自动创建一个存储帐户。

    如果未在创建 VM 时启用启动诊断，可在以后随时使用 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 或 [Azure 资源管理器模板](../articles/virtual-machines/windows/extensions-diagnostics-template.md)启用它。

- **启用来宾 OS 诊断数据收集。** 创建 VM 时，可以在“设置”屏幕上启用来宾 OS 诊断。 如果确实启用了诊断数据收集，[用于 Linux 的 IaaSDiagnostics 扩展](../articles/virtual-machines/linux/diagnostic-extension.md)或[用于 Windows 的 IaaSDiagnostics 扩展](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)将添加到 VM，使你可以收集更多的磁盘、CPU 和内存数据。

    使用收集的诊断数据，可以为 VM 配置自动缩放。 你还可以配置[Azure Monitor 日志](../articles/azure-monitor/platform/data-platform-logs.md)以存储数据并设置警报，以便在性能不正常时通知你。

## <a name="alerts"></a>警报

可以根据特定的性能指标创建[警报](../articles/azure-monitor/platform/alerts-overview.md)。 举例来说，可以根据以下问题生成警报，平均 CPU 使用率超过特定的阈值，或者可用磁盘空间低于特定的空间量。 可以使用[Azure 资源管理器模板](../articles/azure-monitor/platform/alerts-metric-create-templates.md)或[Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)在[Azure 门户](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)中配置警报。

## <a name="azure-service-health"></a>Azure 服务运行状况

[Azure 服务运行状况](../articles/service-health/service-health-overview.md)会在 Azure 服务问题影响你时提供个性化的指导和支持，并且会帮助你为即将到来的计划内维护做好准备。 Azure 服务运行状况使用具有针对性和灵活性的通知提醒你和你的团队。

## <a name="azure-resource-health"></a>Azure 资源运行状况

[Azure 资源运行状况](../articles/service-health/resource-health-overview.md)有助于在 Azure 问题影响资源时进行诊断和获取支持。 它会告知资源当前和过去的运行状况并帮助减少问题。 资源运行状况可在需要关于 Azure 服务问题的帮助时提供技术支持。

## <a name="azure-activity-log"></a>Azure 活动日志

[Azure 活动日志](../articles/azure-monitor/platform/platform-logs-overview.md) 是一种方便用户深入了解 Azure 中发生的订阅级别事件的订阅日志。 该日志包括从 Azure 资源管理器操作数据到服务运行状况事件更新的一系列数据。 可以在 Azure 门户中单击“活动日志”查看 VM 的日志。

可以对活动日志执行的部分操作包括：

- [根据活动日志事件创建警报](../articles/azure-monitor/platform/platform-logs-overview.md)。
- 将[其流式传输到事件中心](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md)，以供第三方服务或自定义分析解决方案（例如 Power BI）引入。
- 使用[Power BI 内容包](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)在 Power BI 中进行分析。
- [将活动日志保存到存储帐户](../articles/azure-monitor/platform/archive-activity-log.md)进行存档或手动检查。 可以使用“日志配置文件”指定保留时间（天）。

还可以通过使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 或[监视 REST API](https://docs.microsoft.com/rest/api/monitor/) 访问活动日志数据。

[Azure 资源日志](../articles/azure-monitor/platform/platform-logs-overview.md)是 VM 发出的日志，提供有关其操作的丰富、频繁的数据。 资源日志不同于活动日志，通过提供有关在 VM 中执行的操作的见解。

可以对诊断日志执行的部分操作包括：

- 将诊断日志保存到[存储帐户](../articles/azure-monitor/platform/archive-diagnostic-logs.md)进行审核或手动检查。 可以使用“资源诊断设置”指定保留时间（天）。
- [将它们流式传输到事件中心](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md)，以供第三方服务或自定义分析解决方案（例如 Power BI）引入。
- 使用 [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md) 对诊断日志进行分析。

## <a name="advanced-monitoring"></a>高级监视

若要查看 Azure VM 和虚拟机规模集支持的应用程序或服务，识别 VM 中运行的来宾操作系统或工作负荷的问题，以了解它是否会影响应用程序的可用性或性能，或者是否存在应用程序问题，同时启用[用于 VM 的 Azure Monitor](../articles/azure-monitor/insights/vminsights-overview.md)和[Application Insights](../articles/azure-monitor/app/app-insights-overview.md)。

通过分析 Windows 和 Linux Vm 的性能和运行状况（包括不同的进程以及其他资源和外部进程的相互关联依赖关系），用于 VM 的 Azure Monitor 大规模监视 Azure 虚拟机（VM）发现. 它包括多个趋势性能图表，可帮助调查问题并评估 Vm 的容量。 依赖关系图显示监视和未监视的计算机、进程和这些计算机之间的失败和活动的网络连接，并显示具有标准网络连接指标的趋势图表。 与 Application Insights 相结合，你可以监视应用程序并捕获遥测数据（如 HTTP 请求、异常等），以便你可以将 Vm 与应用程序之间的问题关联起来。 配置[Azure Monitor 警报](../articles/azure-monitor/platform/alerts-overview.md)，以根据通过监视用于 VM 的 Azure Monitor 收集的数据中检测到的重要条件发出警报。

## <a name="next-steps"></a>后续步骤

- 逐步完成[使用 Azure PowerShell 监视 Windows 虚拟机](../articles/virtual-machines/windows/tutorial-monitoring.md)或[使用 Azure CLI 监视 Linux 虚拟机](../articles/virtual-machines/linux/tutorial-monitoring.md)中的步骤。

- 了解更多有关[监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)的最佳做法的信息。
