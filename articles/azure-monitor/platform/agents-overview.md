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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249082"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure 监视器代理概述

虚拟机和其他计算资源需要代理收集监视数据，以测量其来宾操作系统和工作负载的性能和可用性。 本文介绍 Azure Monitor 使用的代理，并帮助你确定需要满足特定环境的哪些要求。

> [!NOTE]
> 由于 Azure Monitor 和 Log Analytics 最近已合并到一起，Azure Monitor 目前有多个代理。 虽然它们的功能可能有重叠，但每个功能都有独特的功能。 根据您的要求，您可能需要虚拟机上的一个或多个代理。 

您可能有一组特定的要求，这些要求不能由特定虚拟机的单个代理完全满足。 例如，您可能希望使用需要 Azure 诊断扩展的指标警报，但还希望利用 Azure 监视器的功能来执行需要日志分析代理和依赖项代理的 VM 功能。 在这种情况下，您可以使用多个代理，对于需要每个代理的功能的客户来说，这是一个常见方案。

## <a name="summary-of-agents"></a>代理摘要

下表提供了 Windows 和 Linux 的 Azure 监视器代理的快速比较。 有关每个部分的进一步详细信息，请参阅以下部分。 

### <a name="windows-agents"></a>Windows 代理

| | 诊断<br>扩展 （WAD） | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|
| 支持的环境 | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | 
| 代理要求  | 无 | 无 | 需要 Log Analytics 代理 |
| 收集的数据 | 事件日志<br>ETW 事件<br>性能<br>基于文件的日志<br>IIS 日志<br>.NET 应用日志<br>故障转储<br>代理诊断日志 | 事件日志<br>性能<IIS logs><br>基于文件的日志<br>见解和解决方案<br>其他服务 | 进程详细信息和依赖项<br>网络连接指标 |
| 数据发送目标 | Azure 存储<br>Azure Monitor 指标<br>事件中心 | Azure Monitor 日志 | Azure Monitor 日志 |


### <a name="linux-agents"></a>Linux 代理

| | 诊断<br>扩展（LAD） | 泰拉夫<br>代理 | Log Analytics<br>代理 | 依赖项<br>代理 |
|:---|:---|:---|:---|:---|
| 支持的环境 | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 |
| 代理要求  | 无 | 无 | 无 | 需要 Log Analytics 代理 |
| 收集的数据 | Syslog<br>性能 | 性能 | Syslog<br>性能| 进程详细信息和依赖项<br>网络连接指标 |
| 数据发送目标 | Azure 存储<br>事件中心 | Azure Monitor 指标 | Azure Monitor 日志 | Azure Monitor 日志 |

## <a name="log-analytics-agent"></a>Log Analytics 代理

[Log Analytics 代理](log-analytics-agent.md)从 Azure 中的虚拟机、其他云提供商和本地的来宾操作系统与工作负荷收集监视数据。 它将数据收集到 Log Analytics 工作区中。 日志分析代理是系统中心操作管理器使用的代理，您可以多家庭代理计算机与管理组和 Azure 监视器同时通信。 Azure 监视器中的某些见解和解决方案也需要此代理。


> [!NOTE]
> Windows 的日志分析代理通常称为 Microsoft 监视代理 （MMA）。 适用于 Linux 的 Log Analytics 代理通常称作 OMS 代理。



如果需要以下情况，请使用日志分析代理：

* 从 Azure 外部的虚拟或物理计算机收集日志和性能数据。 
* 将数据发送到日志分析工作区，以利用[Azure 监视器日志](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支持的功能，如[日志查询](../log-query/log-query-overview.md)。
* 对 VM 使用[Azure 监视器](../insights/vminsights-overview.md)，它允许您大规模监视虚拟机，并监视虚拟机对其他资源和外部进程的依赖关系。  
* 使用[Azure 安全中心](../../security-center/security-center-intro.md)或[Azure 哨兵](../../sentinel/overview.md)管理虚拟机的安全性。
* 使用[Azure 自动化更新管理](../../automation/automation-update-management.md)[、Azure 自动化状态配置](../../automation/automation-dsc-overview.md)或[Azure 自动化更改跟踪和清单](../../automation/change-tracking.md)来全面管理 Azure VM
* 使用不同的[解决方案](../monitor-reference.md#insights-and-core-solutions)来监视特定的服务或应用程序。

日志分析代理的限制包括：

- 无法将数据发送到 Azure 监视器指标、Azure 存储或 Azure 事件中心。

## <a name="azure-diagnostics-extension"></a>Azure 诊断扩展

[Azure 诊断扩展](diagnostics-extension-overview.md)从 Azure 虚拟机和其他计算资源的来宾操作系统和工作负荷收集监视数据。 它主要将数据收集到 Azure 存储中，但也允许您定义数据接收器，以便将数据也发送到其他目标，如 Azure 监视器指标和 Azure 事件中心。

如果需要：使用 Azure 诊断扩展：

- 将数据发送到 Azure 存储以进行存档，或使用[Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)等工具对其进行分析。
- 将数据发送到[Azure 监视器指标](data-platform-metrics.md)，以便使用[指标资源管理器](metrics-getting-started.md)进行分析，并利用近实时[指标警报](../../azure-monitor/platform/alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)（仅限 Windows）等功能。
- 使用[Azure 事件中心](diagnostics-extension-stream-event-hubs.md)将数据发送到第三方工具。
- 收集[启动诊断](../../virtual-machines/troubleshooting/boot-diagnostics.md)以调查 VM 启动问题。

Azure 诊断扩展的限制包括：

- 只能与 Azure 资源一起使用。
- 将数据发送到 Azure 监视器日志的能力有限。

## <a name="telegraf-agent"></a>泰拉夫特工

[Influx 数据 Telegraf 代理](collect-custom-metrics-linux-telegraf.md)用于从 Linux 计算机到 Azure 监视器指标收集性能数据。

如果您需要：

* 将数据发送到[Azure 监视器指标](data-platform-metrics.md)，以便使用[指标资源管理器](metrics-getting-started.md)进行分析，并利用近实时[指标警报](../../azure-monitor/platform/alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)（仅限 Linux）等功能。 



## <a name="dependency-agent"></a>依赖关系代理

依赖项代理收集有关在虚拟机上运行的进程和外部进程依赖项的已发现数据。 

如果需要以下任务，请使用依赖项代理：

* 使用 VM 的"地图"功能[Azure 监视器](../insights/vminsights-overview.md)[或服务映射](../insights/service-map.md)解决方案。

使用依赖项代理时请考虑以下事项：

- 依赖项代理要求在同一个虚拟机上安装 Log Analytics 代理。
- 在 Linux VM 上，必须先安装 Log Analytics 代理，然后再安装 Azure 诊断扩展。

## <a name="extensions-compared-to-agents"></a>与代理相比的扩展

[Windows](../../virtual-machines/extensions/oms-windows.md)和[Linux](../../virtual-machines/extensions/oms-linux.md)的日志分析扩展在 Azure 虚拟机上安装日志分析代理。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure 监视器依赖项扩展在 Azure 虚拟机上安装依赖项代理。 这些代理与上述代理相同，但允许您通过[虚拟机扩展](../../virtual-machines/extensions/overview.md)来管理它们。 应尽可能使用扩展来安装和管理代理。


## <a name="next-steps"></a>后续步骤

获取有关每个代理的更多详细信息，请了解以下内容：

- [日志分析代理概述](log-analytics-agent.md)
- [Azure 诊断扩展概述](diagnostics-extension-overview.md)
- [使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](collect-custom-metrics-linux-telegraf.md)
