---
title: 在 Azure Monitor 中使用适用于 IIS 的 blob 存储和适用于事件的表存储 | Microsoft Docs
description: Azure Monitor 可以读取将诊断写入到表存储的 Azure 服务的日志或写入到 Blob 存储的 IIS 日志。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 095fd0b534c0dffaf80d2464fb9734f295335b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317172"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>将 Azure 诊断扩展中的数据收集到 Azure Monitor 日志
Azure 诊断扩展是 [Azure Monitor 中的代理](agents-overview.md)，可从 Azure 计算资源（包括虚拟机）的来宾操作系统中收集监视数据。 本文介绍了如何将诊断扩展收集的数据从 Azure 存储收集到 Azure Monitor 日志。

> [!NOTE]
> Azure Monitor 中的 Log Analytics 代理通常是用于将来宾操作系统中的数据收集到 Azure Monitor 日志的首选方法。 请参阅 [Azure Monitor 代理概述](agents-overview.md)来详细比较各个代理。

## <a name="supported-data-types"></a>支持的数据类型
Azure 诊断扩展将数据存储在 Azure 存储帐户中。 要使 Azure Monitor 日志收集此数据，它必须位于以下位置：

| 日志类型 | 资源类型 | 位置 |
| --- | --- | --- |
| IIS 日志 |虚拟机 <br> Web 角色 <br> 辅助角色 |wad-iis-logfiles（Blob 存储） |
| Syslog |虚拟机 |LinuxsyslogVer2v0（表存储） |
| Service Fabric 操作事件 |Service Fabric 节点 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 节点 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 节点 |WADServiceFabricReliableServiceEventTable |
| Windows 事件日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADWindowsEventLogsTable（表存储） |
| Windows ETW 日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADETWEventTable（表存储） |

## <a name="data-types-not-supported"></a>不支持的数据类型

- 来宾操作系统中的性能数据
- Azure 网站中的 IIS 日志


## <a name="enable-azure-diagnostics-extension"></a>启用 Azure 诊断扩展
请参阅[安装并配置 Windows Azure 诊断扩展 (WAD)](diagnostics-extension-windows-install.md) 或[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)，了解有关安装和配置诊断扩展的详细信息。 这将允许你指定存储帐户并配置要转发到 Azure Monitor 日志的数据集合。


## <a name="collect-logs-from-azure-storage"></a>从 Azure 存储收集日志
请使用以下过程启用从 Azure 存储帐户收集诊断扩展数据：

1. 在 Azure 门户中，转到 **Log Analytics 工作区**并选择你的工作区。
1. 在菜单的“工作区数据源”部分中单击“存储帐户日志”。
2. 单击“添加”  。
3. 选择包含要收集的数据的**存储帐户**。
4. 选择要收集的**数据类型**。
5. “源”的值将基于数据类型自动填充。
6. 单击“确定”以保存配置。 
7. 针对其他数据类型重复操作。

在大约 30 分钟过后，能够在 Log Analytics 工作区中看到存储帐户中的数据。 在应用了配置后，只能看到写入到存储中的数据。 工作区不会从存储帐户中读取预先存在的数据。

> [!NOTE]
> 该门户不会验证源是否存在于存储帐户中，也不会验证是否正在写入新数据。



## <a name="next-steps"></a>后续步骤

* 针对支持的 Azure 服务[收集 Azure 服务的日志和指标](./resource-logs.md#send-to-log-analytics-workspace)。
* [启用解决方案](../insights/solutions.md)深入分析数据。
* [使用搜索查询](../log-query/log-query-overview.md)分析数据。

