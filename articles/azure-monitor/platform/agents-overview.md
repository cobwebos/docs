---
title: Azure 监视代理概述 | Microsoft Docs
description: 本文提供可用于在 Azure 或混合环境中支持监视虚拟机的 Azure 代理的详细概述。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a9786c1f596a9f59e63886fa503bddac58ee7a8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325332"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor 代理概述

虚拟机和其他计算资源需要代理收集监视数据，以度量其来宾操作系统和工作负载的性能和可用性。 本文介绍 Azure Monitor 使用的代理，并帮助你确定需要满足特定环境的哪些要求。

> [!NOTE]
> 由于 Azure Monitor 和 Log Analytics 最近已合并到一起，Azure Monitor 目前有多个代理。 虽然它们的特性可能存在重叠，但每个特性都有独特的功能。 可能需要在虚拟机上有一个或多个代理，具体取决于要求。 

你可能有一组特定的要求，这些要求在为特定虚拟机设置单个代理的情况下无法完全满足。 例如，你可能想要使用需要 Azure 诊断扩展的指标警报，但同时又想要利用用于 VM 的 Azure Monitor 功能，该功能需要 Log Analytics 代理和依赖项代理。 在这样的情况下，可以使用多个代理。对于需要每个代理中的功能的客户，这很常见。

## <a name="summary-of-agents"></a>代理摘要

下表对适用于 Windows 和 Linux 的 Azure Monitor 代理进行了快速比较。 以下部分提供了每个代理的更多详细信息。 

### <a name="windows-agents"></a>Windows 代理

| | 诊断<br>扩展 (WAD) | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|
| **支持的环境** | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | 
| **代理要求**  | 无 | 无 | 需要 Log Analytics 代理 |
| **收集的数据** | 事件日志<br>ETW 事件<br>性能<br>基于文件的日志<br>IIS 日志<br>.NET 应用日志<br>故障转储<br>代理诊断日志 | 事件日志<br>性能<IIS logs><br>基于文件的日志<br>见解和解决方案<br>其他服务 | 进程详细信息和依赖项<br>网络连接指标 |
| **数据发送目标** | Azure 存储<br>Azure Monitor 指标<br>事件中心 | Azure Monitor 日志 | Azure Monitor 日志 |


### <a name="linux-agents"></a>Linux 代理

| | 诊断<br>扩展 (LAD) | Telegraf<br>代理 | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|:---|
| **支持的环境** | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 |
| **代理要求**  | 无 | 无 | 无 | 需要 Log Analytics 代理 |
| **收集的数据** | Syslog<br>性能 | 性能 | Syslog<br>性能| 进程详细信息和依赖项<br>网络连接指标 |
| **数据发送目标** | Azure 存储<br>事件中心 | Azure Monitor 指标 | Azure Monitor 日志 | Azure Monitor 日志 |

## <a name="log-analytics-agent"></a>Log Analytics 代理

[Log Analytics 代理](log-analytics-agent.md)从 Azure 中的虚拟机、其他云提供商和本地的来宾操作系统与工作负荷收集监视数据。 它将数据收集到 Log Analytics 工作区中。 Log Analytics 代理是 System Center Operations Manager 使用的代理，你可以通过多宿主代理计算机同时与管理组和 Azure Monitor 通信。 Azure Monitor 中的某些见解和解决方案也需要此代理。


> [!NOTE]
> 适用于 Windows 的 Log Analytics 代理通常称作 Microsoft Monitoring Agent (MMA)。 适用于 Linux 的 Log Analytics 代理通常称作 OMS 代理。



如果需要执行以下操作，请使用 Log Analytics 代理：

* 从 Azure 外部的虚拟机或物理机收集日志和性能数据。 
* 将数据发送到 Log Analytics 工作区，以利用 [Azure Monitor 日志](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支持的功能，例如[日志查询](../log-query/log-query-overview.md)。
* 使用[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)允许你大规模监视虚拟机，并监视其进程和其他资源和外部进程的依赖项。  
* 使用[Azure 安全中心](../../security-center/security-center-intro.md)或[azure Sentinel](../../sentinel/overview.md)管理虚拟机的安全性。
* 使用[Azure 自动化更新管理](../../automation/automation-update-management.md)、 [Azure 自动化状态配置](../../automation/automation-dsc-overview.md)或[azure 自动化更改跟踪和清点](../../automation/change-tracking.md)来提供 Azure vm 的全面管理
* 使用不同的[解决方案](../monitor-reference.md#insights-and-core-solutions)来监视特定服务或应用程序。

Log Analytics 代理的限制包括：

- 无法将数据发送到 Azure Monitor 指标、Azure 存储或 Azure 事件中心。

## <a name="azure-diagnostics-extension"></a>Azure 诊断扩展

[Azure 诊断扩展](diagnostics-extension-overview.md)从 Azure 虚拟机和其他计算资源的来宾操作系统和工作负载收集监视数据。 它主要将数据收集到 Azure 存储中，但也允许你通过定义数据接收器将数据同时发送到其他目标（例如 Azure Monitor 指标和 Azure 事件中心）。

如果需要执行以下操作，请使用 Azure 诊断扩展：

- 将数据发送到 Azure 存储进行存档，或使用 [Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之类的工具对其进行分析。
- 将数据发送到 [Azure Monitor 指标](data-platform-metrics.md)，以便使用[指标资源管理器](metrics-getting-started.md)对其进行分析，并利用准实时[指标警报](./alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)之类的功能（仅限 Windows）。
- 使用 [Azure 事件中心](diagnostics-extension-stream-event-hubs.md)将数据发送到第三方工具。
- 收集[启动诊断](../../virtual-machines/troubleshooting/boot-diagnostics.md)数据，调查 VM 启动问题。

Azure 诊断扩展的限制包括：

- 只能与 Azure 资源配合使用。
- 将数据发送到 Azure Monitor 日志的功能有限。

## <a name="telegraf-agent"></a>Telegraf 代理

[InfluxData Telegraf 代理](collect-custom-metrics-linux-telegraf.md)用于从 Linux 计算机收集性能数据以 Azure Monitor 指标。

如果需要执行以下操作，请使用 Telegraf 代理：

* 将数据发送到 [Azure Monitor 指标](data-platform-metrics.md)，以便使用[指标资源管理器](metrics-getting-started.md)对其进行分析，并利用准实时[指标警报](./alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)之类的功能（仅限 Linux）。 



## <a name="dependency-agent"></a>依赖关系代理

依赖关系代理用于收集在虚拟机上运行的进程的已发现数据，以及外部进程依赖项的已发现数据。 

如果需要执行以下操作，请使用 Dependency Agent：

* 使用地图功能[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)或[服务映射](../insights/service-map.md)解决方案。

使用 Dependency Agent 时，请注意以下事项：

- 依赖项代理要求在同一个虚拟机上安装 Log Analytics 代理。
- 在 Linux VM 上，必须先安装 Log Analytics 代理，然后再安装 Azure 诊断扩展。

## <a name="extensions-compared-to-agents"></a>相较于代理的扩展

[Windows](../../virtual-machines/extensions/oms-windows.md) 和 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics 扩展在 Azure 虚拟机上安装 Log Analytics 代理。 适用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure Monitor 依赖项扩展在 Azure 虚拟机上安装依赖关系代理。 它们是如上所述的代理，但你可以通过[虚拟机扩展](../../virtual-machines/extensions/overview.md)对它们进行管理。 应尽可能使用扩展来安装和管理代理。


## <a name="next-steps"></a>后续步骤

在以下站点获取每个代理的更多详细信息：

- [Log Analytics 代理概述](log-analytics-agent.md)
- [Azure 诊断扩展概述](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](collect-custom-metrics-linux-telegraf.md)

