---
title: Azure 监视代理概述 | Microsoft Docs
description: 本文提供可用于在 Azure 或混合环境中支持监视虚拟机的 Azure 代理的详细概述。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249082"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor 代理概述

虚拟机和其他计算资源需要代理收集监视数据，以衡量其来宾操作系统和工作负载的性能和可用性。 本文介绍 Azure Monitor 使用的代理，并帮助你确定需要满足特定环境的要求。

> [!NOTE]
> Azure Monitor 当前有多个代理，因为最近合并了 Azure Monitor 和 Log Analytics。 虽然它们的功能可能存在重叠，但每个功能都有独特的功能。 根据你的要求，你可能需要虚拟机上的一个或多个代理。 

可能有一组特定的要求，这些要求对于特定虚拟机的单个代理无法完全满足。 例如，你可能想要使用需要 Azure 诊断扩展的指标警报，但同时想要利用需要 Log Analytics 代理和依赖项代理的用于 VM 的 Azure Monitor 功能。 在这种情况下，可以使用多个代理，这是需要每个代理中的功能的客户的常见方案。

## <a name="summary-of-agents"></a>代理摘要

下表提供了适用于 Windows 和 Linux 的 Azure Monitor 代理的快速比较。 下一节提供了有关每个的详细信息。 

### <a name="windows-agents"></a>Windows 代理

| | 诊断<br>扩展（WAD） | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|
| 支持的环境 | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | 
| 代理要求  | 无 | 无 | 需要 Log Analytics 代理 |
| 收集的数据 | 事件日志<br>ETW 事件<br>性能<br>基于文件的日志<br>IIS 日志<br>.NET 应用日志<br>故障转储<br>代理诊断日志 | 事件日志<br>性能<IIS logs><br>基于文件的日志<br>见解和解决方案<br>其他服务 | 进程详细信息和依赖项<br>网络连接指标 |
| 数据发送到 | Azure 存储<br>Azure Monitor 度量值<br>事件中心 | Azure Monitor 日志 | Azure Monitor 日志 |


### <a name="linux-agents"></a>Linux 代理

| | 诊断<br>扩展（LAD） | Telegraf<br>代理 | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|:---|
| 支持的环境 | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 |
| 代理要求  | 无 | 无 | 无 | 需要 Log Analytics 代理 |
| 收集的数据 | Syslog<br>性能 | 性能 | Syslog<br>性能| 进程详细信息和依赖项<br>网络连接指标 |
| 数据发送到 | Azure 存储<br>事件中心 | Azure Monitor 度量值 | Azure Monitor 日志 | Azure Monitor 日志 |

## <a name="log-analytics-agent"></a>Log Analytics 代理

[Log Analytics 代理](log-analytics-agent.md)在 Azure、其他云提供程序和本地中收集来宾操作系统和虚拟机工作负荷中的监视数据。 它将数据收集到 Log Analytics 工作区中。 Log Analytics 代理是 System Center Operations Manager 使用的同一代理，你可以多宿主代理计算机与管理组进行通信，同时 Azure Monitor。 Azure Monitor 中的某些见解和解决方案也需要此代理。


> [!NOTE]
> 适用于 Windows 的 Log Analytics 代理通常称为 Microsoft Monitoring Agent （MMA）。 适用于 Linux 的 Log Analytics 代理通常称为 OMS 代理。



如果需要，请使用 Log Analytics 代理：

* 从 Azure 外部的虚拟或物理计算机收集日志和性能数据。 
* 向 Log Analytics 工作区发送数据，以利用[Azure Monitor 日志](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支持的功能，如[日志查询](../log-query/log-query-overview.md)。
* 使用[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)允许你大规模监视虚拟机，并监视其进程和其他资源和外部进程的依赖项。  
* 使用[Azure 安全中心](../../security-center/security-center-intro.md)或[azure Sentinel](../../sentinel/overview.md)管理虚拟机的安全性。
* 使用[Azure 自动化更新管理](../../automation/automation-update-management.md)、 [Azure 自动化状态配置](../../automation/automation-dsc-overview.md)或[azure 自动化更改跟踪和清点](../../automation/change-tracking.md)来提供 Azure vm 的全面管理
* 使用不同的[解决方案](../monitor-reference.md#insights-and-core-solutions)来监视特定服务或应用程序。

Log Analytics 代理的限制包括：

- 无法将数据发送到 Azure Monitor 指标、Azure 存储或 Azure 事件中心。

## <a name="azure-diagnostics-extension"></a>Azure 诊断扩展

[Azure 诊断扩展](diagnostics-extension-overview.md)收集来宾操作系统中的监视数据以及 Azure 虚拟机和其他计算资源的工作负荷。 它主要收集 Azure 存储中的数据，还允许你定义数据接收器，同时将数据发送到其他目标（例如 Azure Monitor 度量值和 Azure 事件中心）。

如果需要，请使用 Azure 诊断扩展：

- 将数据发送到 Azure 存储以进行存档，或者将数据与[Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之类的工具进行分析。
- 使用[指标资源管理器](metrics-getting-started.md)将数据发送到[Azure Monitor 度量值](data-platform-metrics.md)，并利用准实时[指标警报](../../azure-monitor/platform/alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)等功能（仅限 Windows）。
- 使用[Azure 事件中心](diagnostics-extension-stream-event-hubs.md)将数据发送到第三方工具。
- 收集[启动诊断](../../virtual-machines/troubleshooting/boot-diagnostics.md)以调查 VM 启动问题。

Azure 诊断扩展的限制包括：

- 只能与 Azure 资源一起使用。
- 将数据发送到 Azure Monitor 日志的功能有限。

## <a name="telegraf-agent"></a>Telegraf 代理

[InfluxData Telegraf 代理](collect-custom-metrics-linux-telegraf.md)用于从 Linux 计算机收集性能数据以 Azure Monitor 指标。

如果需要执行以下操作，请使用 Telegraf 代理：

* 使用[指标资源管理器](metrics-getting-started.md)将数据发送到[Azure Monitor 度量值](data-platform-metrics.md)，并利用准实时[指标警报](../../azure-monitor/platform/alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)等功能（仅适用于 Linux）。 



## <a name="dependency-agent"></a>依赖关系代理

依赖关系代理收集有关在虚拟机上运行的进程和外部进程依赖关系的发现数据。 

如果需要，请使用依赖关系代理：

* 使用地图功能[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)或[服务映射](../insights/service-map.md)解决方案。

使用依赖关系代理时，请注意以下事项：

- 依赖关系代理要求在同一虚拟机上安装 Log Analytics 代理。
- 在 Linux Vm 上，必须先安装 Log Analytics 代理，然后才能安装 Azure 诊断扩展。

## <a name="extensions-compared-to-agents"></a>与代理比较的扩展

适用于[Windows](../../virtual-machines/extensions/oms-windows.md)和[Linux](../../virtual-machines/extensions/oms-linux.md)的 Log Analytics 扩展在 Azure 虚拟机上安装 Log Analytics 代理。 适用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure Monitor 依赖项扩展在 Azure 虚拟机上安装依赖关系代理。 这两个代理是上述相同的代理，但允许你通过[虚拟机扩展](../../virtual-machines/extensions/overview.md)对其进行管理。 应尽可能使用扩展安装和管理代理。


## <a name="next-steps"></a>后续步骤

在以下各项中获取有关每个代理的详细信息：

- [Log Analytics 代理概述](log-analytics-agent.md)
- [Azure 诊断扩展概述](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](collect-custom-metrics-linux-telegraf.md)
