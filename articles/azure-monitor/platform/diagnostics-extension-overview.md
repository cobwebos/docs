---
title: Azure 诊断扩展概述
description: 使用 Azure 诊断在云服务、虚拟机和 Service Fabric 中进行调试、性能度量、监视和流量分析
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 5dcdfba6e8dd00c8ba09e5e98293a30d19e51c99
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635951"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 诊断扩展概述
Azure 诊断扩展是 [Azure Monitor 中的一个代理](agents-overview.md)，可从 Azure 计算资源（包括虚拟机）的来宾操作系统收集监视数据。 本文概述了 Azure 诊断扩展，其中包括它支持的特定功能以及用于安装和配置的选项。 

> [!NOTE]
> Azure 诊断扩展是可用于从计算资源的来宾操作系统收集监视数据的代理之一。 有关不同代理的说明以及选择适合需求的代理的指导，请参阅 [Azure Monitor 代理](agents-overview.md)概述。

## <a name="primary-scenarios"></a>主要方案
诊断扩展解决的主要方案是：

- 将来宾指标收集到 Azure Monitor 指标中。
- 将来宾日志和指标发送到 Azure 存储以进行存档。
- 将来宾日志和指标发送到 Azure 事件中心，以在 Azure 外部发送。


## <a name="comparison-to-log-analytics-agent"></a>与 Log Analytics 代理比较
Azure Monitor 中的 Log Analytics 代理也可用于从虚拟机的来宾操作系统收集监视数据。 你可以根据需求选择使用其中一种或两者。 如需查看 Azure Monitor 代理的详细比较，请参阅 [Azure Monitor 代理概述](agents-overview.md)。 

需要考虑的主要区别是：

- 仅可通过 Azure 虚拟计算机来使用 Azure 诊断扩展。 可通过 Azure、其他云和本地中的虚拟机来使用 Log Analytics 代理。
- Azure 诊断扩展将数据发送到 Azure 存储、[Azure Monitor 指标](data-platform-metrics.md)（仅限 Windows）和事件中心。 Log Analytics 代理将数据收集到 [Azure Monitor 日志](data-platform-logs.md)。
- [解决方案](../monitor-reference.md#insights-and-core-solutions)、[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) 和其他服务（如 [Azure 安全中心](/azure/security-center/)）需要 Log Analytics 代理。

## <a name="costs"></a>成本
Azure 诊断扩展不收取任何费用，但引入的数据可能产生费用。 查看用于收集数据的目标的 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-collected"></a>收集的数据
下表列出了 Windows 和 Linux 诊断扩展可以收集的数据。

### <a name="windows-diagnostics-extension-wad"></a>Windows 诊断扩展 (WAD)

| 数据源 | 说明 |
| --- | --- |
| Windows 事件日志   | 来自 Windows 事件日志的事件。 |
| 性能计数器 | 测量数值操作系统和工作负载各方面性能的数值。 |
| IIS 日志             | 在来宾操作系统上运行的 IIS 网站的使用情况信息。 |
| 应用程序日志     | 应用程序写入的跟踪消息。 |
| .NET EventSource 日志 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 类的代码编写事件 |
| [基于清单的 ETW 日志](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |由任何进程生成的 Windows 事件的事件跟踪。 |
| 故障转储（日志）   | 有关应用程序崩溃时的进程状态的信息。 |
| 基于文件的日志    | 应用程序或服务创建的日志。 |
| 代理诊断日志 | 有关 Azure 诊断自身的信息。 |


### <a name="linux-diagnostics-extension-lad"></a>Linux 诊断扩展 (LAD)

| 数据源 | 说明 |
| --- | --- |
| Syslog | 发送到 Linux 事件日志记录系统的事件。   |
| 性能计数器  | 测量数值操作系统和工作负载各方面性能的数值。 |
| 日志文件 | 发送到基于文件的日志的条目。  |

## <a name="data-destinations"></a>数据目标
适用于 Windows 和 Linux 的 Azure 诊断扩展始终将数据收集到 Azure 存储帐户中。 请参阅[安装并配置 Microsoft Azure 诊断扩展 (WAD)](diagnostics-extension-windows-install.md) 和[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)，获取收集此数据的特定表和 Blob 的列表。

配置一个或多个数据接收器以便将数据发送到其他目标。 以下部分列出了适用于 Windows 和 Linux 诊断扩展的接收器。

### <a name="windows-diagnostics-extension-wad"></a>Windows 诊断扩展 (WAD)

| 目标 | 说明 |
|:---|:---|
| Azure Monitor 指标 | 将性能数据收集到 Azure Monitor 指标。 请参阅[将来宾 OS 指标发送到 Azure Monitor 指标数据库](collect-custom-metrics-guestos-resource-manager-vm.md)。  |
| 事件中心 | 使用 Azure 事件中心在 Azure 外部发送数据。 请参阅[将 Azure 诊断数据流式传输到事件中心](diagnostics-extension-stream-event-hubs.md) |
| Azure 存储 Blob | 除表外，还将数据写入 Azure 存储中的 Blob。 |
| Application Insights | 将 VM 中运行的应用程序的数据收集到 Application Insights，以与其他应用程序监视集成。 请参阅[将诊断数据发送到 Application Insights](diagnostics-extension-to-application-insights.md)。 |

尽管 Log Analytics 代理通常用于此功能，但你也可以将存储中的 WAD 数据收集到 Log Analytics 工作区中，以使用 Azure Monitor 日志对其进行分析。 它可以将数据直接发送到 Log Analytics 工作区，并支持提供其他功能的解决方案和见解。  请参阅[从 Azure 存储中收集 Azure 诊断日志](diagnostics-extension-logs.md)。 


### <a name="linux-diagnostics-extension-lad"></a>Linux 诊断扩展 (LAD)
LAD 将数据写入 Azure 存储中的表。 它支持下表中的接收器。

| 目标 | 说明 |
|:---|:---|
| 事件中心 | 使用 Azure 事件中心在 Azure 外部发送数据。 |
| Azure 存储 Blob | 除表外，还将数据写入 Azure 存储中的 Blob。 |
| Azure Monitor 指标 | 除了 LAD 外，还安装 Telegraf 代理。 请参阅[使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](collect-custom-metrics-linux-telegraf.md)。


## <a name="installation-and-configuration"></a>安装和配置
诊断扩展作为 Azure 中的[虚拟机扩展](../../virtual-machines/extensions/overview.md)实现，因此，它支持相同的、使用资源管理器模板、PowerShell 和 CLI 的安装选项。 要详细了解如何安装和维护虚拟机扩展，请参阅[适用于 Windows 的虚拟机扩展和功能](../../virtual-machines/extensions/features-windows.md)和[适用于 Linux 的虚拟机扩展和功能](../../virtual-machines/extensions/features-linux.md)。

你还可在 Azure 门户的虚拟机菜单中，在“监视”部分的“诊断设置”下安装和配置 Windows 和 Linux 诊断扩展 。

要了解如何安装和配置适用于 Windows 和 Linux 的诊断扩展，请参阅以下文章。

- [安装并配置 Microsoft Azure 诊断扩展 (WAD)](diagnostics-extension-windows-install.md)
- [使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>其他文档

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure 云服务（经典）Web 和辅助角色
- [云服务监视简介](../../cloud-services/cloud-services-how-to-monitor.md)
- [在 Azure 云服务中启用 Azure 诊断](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [适用于 Azure 云服务的 Application Insights](../app/cloudservices.md)<br>[使用 Azure 诊断跟踪云服务应用程序的流](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [在本地计算机开发安装过程中监视和诊断服务](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>后续步骤


* 了解如何[在 Azure 诊断中使用性能计数器](../../cloud-services/diagnostics-performance-counters.md)。
* 如果在开始诊断时或者在 Azure 存储表中查找数据时遇到问题，请参阅 [Azure 诊断故障排除](diagnostics-extension-troubleshooting.md)

