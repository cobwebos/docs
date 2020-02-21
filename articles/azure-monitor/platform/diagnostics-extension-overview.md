---
title: Azure 诊断扩展概述
description: 使用 Azure 诊断在云服务、虚拟机和 Service Fabric 中进行调试、性能度量、监视和流量分析
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: d9db4b4c8e6d82f29d227b9f8afe528e000c651e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467987"
---
# <a name="azure-diagnostics-extension-overview"></a>Azure 诊断扩展概述
Azure 诊断扩展是[Azure Monitor 中](agents-overview.md)的一个代理，可从 Azure 计算资源（包括虚拟机）的来宾操作系统中收集监视数据。 本文概述了 Azure 诊断扩展，其中包括它支持的特定功能以及用于安装和配置的选项。 

> [!NOTE]
> Azure 诊断扩展是可用于从计算资源的来宾操作系统中收集监视数据的一个代理。 请参阅[Azure Monitor 代理概述](agents-overview.md)，了解有关不同代理的说明，以及有关如何根据需要选择适当的代理的指南。

## <a name="comparison-to-log-analytics-agent"></a>与 Log Analytics 代理比较
Azure Monitor 中的 Log Analytics 代理还可以用于从虚拟机的来宾操作系统中收集监视数据。 你可以根据自己的需求选择使用或两者。 有关 Azure Monitor 代理的详细比较，请参阅[Azure Monitor 代理概述](agents-overview.md)。 

需要考虑的主要区别是：

- Azure 诊断扩展只能与 Azure 虚拟机一起使用。 Log Analytics 代理可与 Azure、其他云和本地中的虚拟机一起使用。
- Azure 诊断扩展将数据发送到 Azure 存储、 [Azure Monitor 指标](data-platform-metrics.md)（仅限 Windows）和事件中心。 Log Analytics 代理将数据收集到[Azure Monitor 日志](data-platform-logs.md)。
- [解决方案](../monitor-reference.md#insights-and-core-solutions)、[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)和其他服务（例如[Azure 安全中心](/azure/security-center/)）需要 Log Analytics 代理。

## <a name="costs"></a>成本
Azure 诊断扩展不会产生费用，但可能会产生数据引入的费用。 检查要收集数据的目标[Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-collected"></a>收集的数据
下表列出了 Windows 和 Linux 诊断扩展可以收集的数据。

### <a name="windows-diagnostics-extension-wad"></a>Windows 诊断扩展（WAD）

| 数据源 | 说明 |
| --- | --- |
| Windows 事件日志   | 来自 Windows 事件日志的事件。 |
| 性能计数器 | 数值度量操作系统和工作负载的不同方面的性能。 |
| IIS 日志             | 在来宾操作系统上运行的 IIS 网站的使用情况信息。 |
| 应用程序日志     | 应用程序写入的跟踪消息。 |
| .NET EventSource 日志 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 类的代码编写事件 |
| [基于清单的 ETW 日志](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |由任何进程生成的 Windows 事件的事件跟踪。 |
| 故障转储（日志）   | 有关应用程序崩溃时进程状态的信息。 |
| 基于文件的日志    | 由应用程序或服务创建的日志。 |
| 代理诊断日志 | 有关 Azure 诊断本身的信息。 |


### <a name="linux-diagnostics-extension-lad"></a>Linux 诊断扩展（LAD）

| 数据源 | 说明 |
| --- | --- |
| Syslog | 发送到 Linux 事件日志记录系统的事件。   |
| 性能计数器  | 数值度量操作系统和工作负载的不同方面的性能。 |
| 日志文件 | 发送到基于文件的日志的条目。  |

## <a name="data-destinations"></a>数据目标
适用于 Windows 和 Linux 的 Azure 诊断扩展始终将数据收集到 Azure 存储帐户中。 请参阅[安装和配置 Windows Azure 诊断扩展（WAD）](diagnostics-extension-windows-install.md)和[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)，获取收集此数据的特定表和 blob 的列表。

配置一个或多个*数据接收器*，将数据发送到其他其他目标。 以下部分列出了适用于 Windows 和 Linux 诊断扩展的接收器。

### <a name="windows-diagnostics-extension-wad"></a>Windows 诊断扩展（WAD）

| 目标 | 说明 |
|:---|:---|
| Azure Monitor 度量值 | 收集性能数据以 Azure Monitor 指标。 请参阅[将来宾 OS 指标发送到 Azure Monitor 指标数据库](collect-custom-metrics-guestos-resource-manager-vm.md)。  |
| 事件中心 | 使用 Azure 事件中心在 Azure 外部发送数据。 请参阅[将数据流式传输 Azure 诊断到事件中心](diagnostics-extension-stream-event-hubs.md) |
| Azure 存储 Blob | 除了表外，还会将数据写入 Azure 存储中的 blob。 |
| Application Insights | 收集 VM 中运行的应用程序的数据，以 Application Insights 与其他应用程序监视集成。 请参阅[将诊断数据发送到 Application Insights](diagnostics-extension-to-application-insights.md)。 |

你还可以将存储中的 WAD 数据收集到 Log Analytics 工作区，以便使用 Azure Monitor 日志对其进行分析，尽管 Log Analytics 代理通常用于此功能。 它可以将数据直接发送到 Log Analytics 工作区，并支持提供附加功能的解决方案和见解。  请参阅[从 Azure 存储收集 azure 诊断日志](diagnostics-extension-logs.md)。 


### <a name="linux-diagnostics-extension-lad"></a>Linux 诊断扩展（LAD）
LAD 将数据写入 Azure 存储中的表。 它支持下表中的接收器。

| 目标 | 说明 |
|:---|:---|
| 事件中心 | 使用 Azure 事件中心在 Azure 外部发送数据。 |
| Azure 存储 Blob | 除了表外，还会将数据写入 Azure 存储中的 blob。 |
| Azure Monitor 度量值 | 除了 LAD 外，还安装 Telegraf 代理。 请参阅[使用 InfluxData Telegraf 代理收集 LINUX VM 的自定义指标](collect-custom-metrics-linux-telegraf.md)。


## <a name="installation-and-configuration"></a>安装和配置
诊断扩展在 Azure 中实现为[虚拟机扩展](/virtual-machines/extensions/overview)，因此，它支持使用资源管理器模板、POWERSHELL 和 CLI 的相同安装选项。 有关安装和维护虚拟机扩展的常规详细信息，请参阅适用[于 Windows 的虚拟机扩展和功能](/virtual-machines/extensions/features-windows)以及适用于[Linux 的虚拟机扩展和功能](/virtual-machines/extensions/features-linux)。

你还可以在虚拟机菜单的 "**监视**" 部分下的 "**诊断设置**" 下的 "Azure 门户中安装和配置 Windows 和 Linux 诊断扩展。

有关安装和配置适用于 Windows 和 Linux 的诊断扩展的详细信息，请参阅以下文章。

- [安装和配置 Windows Azure 诊断扩展（WAD）](diagnostics-extension-windows-install.md)
- [使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>其他文档

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure 云服务（经典） Web 角色和辅助角色
- [云服务监视简介](../../cloud-services/cloud-services-how-to-monitor.md)
- [在 Azure 云服务中启用 Azure 诊断](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Azure 云服务的 Application Insights](../app/cloudservices.md)<br>[使用 Azure 诊断跟踪云服务应用程序的流](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [在本地计算机开发安装过程中监视和诊断服务](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>后续步骤


* 了解如何[在 Azure 诊断中使用性能计数器](../../cloud-services/diagnostics-performance-counters.md)。
* 如果在开始诊断时或者在 Azure 存储表中查找数据时遇到问题，请参阅 [Azure 诊断故障排除](diagnostics-extension-troubleshooting.md)

