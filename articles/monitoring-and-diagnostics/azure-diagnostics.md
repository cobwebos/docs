---
title: Azure 诊断扩展概述
description: 使用 Azure 诊断在云服务、虚拟机和 Service Fabric 中进行调试、性能度量、监视和流量分析
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: a40917ec24ede1107f7d8ae7f5fb2f0f03d1094c
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278321"
---
# <a name="what-is-azure-diagnostics-extension"></a>什么是 Azure 诊断扩展
Azure 诊断扩展是 Azure 中可对部署的应用程序启用诊断数据收集的代理。 可以使用于自许多不同源的诊断扩展。 目前支持 Azure 云服务（经典）Web 和辅助角色、虚拟机、虚拟机规模集，以及 Service Fabric。 其他 Azure 服务具有不同的诊断方法。 请参阅 [Azure 中的监控概述](../azure-monitor/overview.md)。

## <a name="linux-agent"></a>Linux 代理
[Linux 版本的扩展](../virtual-machines/extensions/diagnostics-linux.md)适用于运行 Linux 的虚拟机。 收集的统计信息和行为因 Windows 版本而异。

## <a name="data-you-can-collect"></a>可以收集的数据
Azure 诊断扩展可收集以下类型的数据：

| 数据源 | Description |
| --- | --- |
| 性能计数器 |操作系统和自定义性能计数器 |
| 应用程序日志 |应用程序写入的跟踪消息 |
| Windows 事件日志 |发送到 Windows 事件日志记录系统的信息 |
| .NET 事件源 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 类的代码编写事件 |
| IIS Logs |有关 IIS 网站的信息 |
| 基于清单的 ETW |由任何进程生成的 Windows 事件的事件跟踪。(1) |
| 故障转储 |有关应用程序崩溃时进程状态的信息 |
| 自定义错误日志 |应用程序或服务创建的日志 |
| Azure Diagnostics基础结构日志 |有关诊断自身的信息 |

(1) 要获取 ETW 提供程序列表，在要收集信息的计算机的控制台窗口中运行 `c:\Windows\System32\logman.exe query providers`。

## <a name="data-storage"></a>数据存储
该扩展将其数据存储在你指定的 [Azure 存储帐户](azure-diagnostics-storage.md)中。

也可将其发送到 [Application Insights](../application-insights/app-insights-cloudservices.md)。 还可将其流式传输到[事件中心](../event-hubs/event-hubs-about.md)，然后就可将其发送到非 Azure 监控服务。

### <a name="azure-monitor"></a>Azure Monitor
你还可以将数据发送到 Azure Monitor。 此时，该接收器仅适用于性能计数器。 由此，可将在 VM、VMSS 或云服务上收集的性能计数器作为自定义指标发送到 Azure Monitor。 Azure Monitor 接收器支持：
* 通过 [Azure Monitor 指标 API](https://docs.microsoft.com/rest/api/monitor/) 检索发送到 Azure Monitor 的所有性能计数器。
* 通过 Azure Monitor 中新的[统一警报体验](monitoring-overview-unified-alerts.md)根据发送到 Azure Monitor 的所有性能计数器发出警报
* 将性能计数器中的通配符运算符视为指标上的“实例”维度。  例如，如果你收集了“LogicalDisk(\*)/DiskWrites/sec”计数器，则可以根据“实例”维度进行筛选和拆分，以基于 VM 上的每个逻辑磁盘（C:、D:等）的磁盘写入次数/秒进行绘图或发出警报

若要了解有关如何配置此接收器的详细信息，请参阅 [Azure 诊断架构文档](azure-diagnostics-schema-1dot3-and-later.md)。

## <a name="versioning-and-configuration-schema"></a>版本控制和配置架构
请参阅 [Azure 诊断版本历史记录和架构](azure-diagnostics-schema.md)。


## <a name="next-steps"></a>后续步骤
请选择要尝试在哪个服务上收集诊断数据，并使用以下文章来入门。 有关具体任务的参考，请使用一般的 Azure 诊断链接。

## <a name="cloud-services-using-azure-diagnostics"></a>使用 Azure 诊断的云服务
* 如果使用 Visual Studio，请参阅[使用 Visual Studio 跟踪云服务应用程序](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)帮助自己入门。 否则，请参阅
* [如何使用 Azure 诊断监视云服务](../cloud-services/cloud-services-how-to-monitor.md)
* [在云服务应用程序中设置 Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)

有关更高级主题，请参阅

* [将 Azure 诊断与适用于云服务的 Application Insights 配合使用](../application-insights/app-insights-cloudservices.md)
* [使用 Azure 诊断跟踪云服务应用程序的流](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [使用 PowerShell 在云服务上设置诊断](../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>虚拟机
* 如果使用 Visual Studio，请参阅[使用 Visual Studio 跟踪 Azure 虚拟机](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)帮助自己入门。 否则，请参阅
* [在 Azure 虚拟机上设置 Azure 诊断](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

有关更高级主题，请参阅

* [使用 PowerShell 在 Azure 虚拟机上设置诊断](../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Azure 资源管理器模板创建具有监视和诊断功能的 Windows 虚拟机](../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
请参阅[监视 Service Fabric 应用程序](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)帮助自己入门。 打开此文章后，可以使用左侧的导航树来查看其他许多 Service Fabric 诊断文章。

## <a name="general-articles"></a>一般文章
* 了解如何[在 Azure 诊断中使用性能计数器](../cloud-services/diagnostics-performance-counters.md)。
* 如果在开始诊断时或者在 Azure 存储表中查找数据时遇到问题，请参阅 [Azure 诊断故障排除](azure-diagnostics-troubleshooting.md)
