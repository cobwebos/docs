---
title: Azure 监视代理概述 | Microsoft Docs
description: 本文提供可用于在 Azure 或混合环境中支持监视虚拟机的 Azure 代理的详细概述。
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150027"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Azure Monitor 代理概述 
计算资源（如虚拟机）生成数据来监视其性能和可用性，就像[其他云资源](../insights/monitor-azure-resource.md)。 不过，计算资源还有需要监视的来宾操作系统和工作负荷。 从资源内部收集此监视数据需要代理。 本文介绍 Azure Monitor 使用的代理，并帮助你确定需要满足特定环境的要求。

## <a name="summary-of-agents"></a>代理摘要

> [!NOTE]
> Azure Monitor 当前有多个代理，因为最近合并了 Azure Monitor 和 Log Analytics。 这两个代理共享某些功能，而其他功能对于特定代理是唯一的。 根据你的要求，你可能需要一个或两个代理。 

Azure Monitor 具有三个代理，每个代理都提供特定功能。 根据你的要求，你可以在虚拟机和其他计算资源上安装一个或多个代理。

* [Azure 诊断扩展](#azure-diagnostic-extension)
* [Log Analytics 代理](#log-analytics-agent)
* [依赖关系代理](#dependency-agent)

下表提供了不同代理的快速比较。 有关每个的详细信息，请参阅本文的其余部分。

| | Azure 诊断扩展 | Log Analytics 代理 | 依赖关系代理 |
|:---|:---|:---|:---|
| 支持的环境 | Azure | Azure<br>其他云<br>本地 | Azure<br>其他云<br>本地 |
| 操作系统 | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| 代理依赖项  | 无 | 无 | 需要 Log Analytics 代理 |
| 收集的数据 | 事件日志<br>ETW 事件<br>Syslog<br>性能<br>IIS 日志<br>.NET 应用程序跟踪输出日志<br>故障转储 | 事件日志<br>Syslog<br>性能<br>IIS 日志<br>自定义日志<br>解决方案中的数据 | 进程详细信息和依赖项<br>网络连接指标 |
| 数据发送到 | Azure 存储空间<br>Azure Monitor 指标<br>事件中心 | Azure Monitor 日志 | Azure Monitor 日志 |



## <a name="azure-diagnostic-extension"></a>Azure 诊断扩展
[Azure 诊断扩展](../../azure-monitor/platform/diagnostics-extension-overview.md)收集来宾操作系统中的监视数据和 Azure 计算资源的工作负荷。 它主要收集 Azure 存储中的数据。 可以将 Azure Monitor 配置为将数据从存储复制到 Log Analytics 工作区。 你还可以将来宾性能数据收集到 Azure Monitor 度量值。

Azure 诊断扩展通常称为 Windows Azure 诊断（WAD）或 Linux Azure 诊断（LAD）扩展。


### <a name="scenarios-supported"></a>支持的方案

Azure 诊断扩展支持的方案包括以下各项：

* 从 Azure 计算资源收集日志和性能数据。
* 将来宾操作系统中的日志和性能数据存档到 Azure 存储。
* 使用工具（如[Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)）查看存储中的监视数据。
* 收集指标数据库中的性能数据，以利用[Azure Monitor 指标](data-platform-metrics.md)支持的功能，例如准实时[指标警报](../../azure-monitor/platform/alerts-metric-overview.md)和[自动缩放](autoscale-overview.md)。 
* 将监视数据从[存储收集到 Log Analytics 工作区](azure-storage-iis-table.md)，以利用[Azure Monitor 日志](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支持的功能，如[日志查询](../log-query/log-query-overview.md)。
* 使用[Azure 事件中心](diagnostics-extension-stream-event-hubs.md)将监视数据发送到第三方工具。
* 使用[启动诊断](../../virtual-machines/troubleshooting/boot-diagnostics.md)调查 VM 启动问题。
* 将数据从 VM 中运行的应用程序复制[到 Application Insights](diagnostics-extension-to-application-insights.md) ，以便与其他应用程序监视集成。

## <a name="log-analytics-agent"></a>Log Analytics 代理
[Log Analytics 代理](log-analytics-agent.md)在 Azure、其他云提供程序和本地中收集来宾操作系统和虚拟机工作负荷中的监视数据。 它将数据收集到 Log Analytics 工作区中。

Log Analytics 代理与 System Center Operations Manager 使用的代理相同，并且您多宿主代理计算机与管理组进行通信，同时 Azure Monitor。 Azure Monitor 中的某些解决方案也需要此代理。

适用于 Windows 的 Log Analytics 代理通常称为 Microsoft 管理代理（MMA）。 适用于 Linux 的 Log Analytics 代理通常称为 OMS 代理。


### <a name="scenarios-supported"></a>支持的方案

Log Analytics 代理支持的方案包括：

* 从 Azure 中的虚拟机、其他云提供程序和本地收集日志和性能数据。 
* 将监视数据收集到 Log Analytics 工作区，以利用[Azure Monitor 日志](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)支持的功能，如[日志查询](../log-query/log-query-overview.md)。
* 使用 Azure Monitor 监视解决方案，如[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)、 [Azure Monitor 容器](../insights/container-insights-overview.md)等。  
* 使用需要 Log Analytics 代理的[Azure Sentinel](../../sentinel/overview.md)来管理虚拟机的安全性。
* 使用需要 Log Analytics 代理的[Azure 安全中心](../../security-center/security-center-intro.md)来管理虚拟机的安全性。
* 使用[Azure 自动化](../../automation/automation-intro.md)的功能，通过其生命周期提供对 Azure vm 的全面管理。  需要 Log Analytics 代理的这些功能的示例包括：
  * 对操作系统更新进行 [Azure 自动化更新管理](../../automation/automation-update-management.md)。
  * 用于维护一致配置状态的[Azure 自动化状态配置](../../automation/automation-dsc-overview.md)。
  * 使用 [Azure 自动化更改跟踪和库存](../../automation/change-tracking.md)来跟踪配置更改。

## <a name="dependency-agent"></a>依赖关系代理
依赖关系代理收集有关在虚拟机上运行的进程和外部进程依赖关系的发现数据。 [服务映射](../insights/service-map.md)和 Map 功能[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)需要此代理。 依赖关系代理需要 Log Analytics 代理，并将数据写入 Azure Monitor 的 Log Analytics 工作区。


## <a name="using-multiple-agents"></a>使用多个代理
对于特定虚拟机，你可能有特定的要求，可以使用 Azure 诊断扩展或 Log Analytics 代理。 例如，你可能想要使用需要 Azure 诊断扩展的指标警报。 但你可能还希望使用需要依赖关系代理和 Log Analytics 代理的用于 VM 的 Azure Monitor 的映射功能。 在这种情况下，可以使用多个代理，这是需要每个代理中的功能的客户的常见方案。

### <a name="considerations"></a>注意事项

- 依赖关系代理要求在同一虚拟机上安装 Log Analytics 代理。
- 在 Linux Vm 上，必须先安装 Log Analytics 代理，然后才能安装 Azure 诊断扩展。


## <a name="next-steps"></a>后续步骤

- 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，了解将代理部署到 Auzre、数据中心或其他云环境中托管的计算机所要满足的要求和支持的方法。

