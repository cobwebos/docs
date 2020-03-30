---
title: 对 Azure 监视器中的事件使用 IIS 和表存储的 blob 存储 |微软文档
description: Azure Monitor 可以读取将诊断写入到表存储的 Azure 服务的日志或写入到 Blob 存储的 IIS 日志。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672389"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>从 Azure 诊断扩展到 Azure 监视器日志收集数据
Azure 诊断扩展是[Azure 监视器中的代理](agents-overview.md)，用于从 Azure 计算资源（包括虚拟机）的来宾操作系统收集监视数据。 本文介绍如何收集诊断扩展从 Azure 存储到 Azure 监视器日志收集的数据。

> [!NOTE]
> Azure 监视器中的日志分析代理通常是将数据从来宾操作系统收集到 Azure 监视器日志的首选方法。 有关代理的详细比较，请参阅[Azure 监视器代理的概述](agents-overview.md)。

## <a name="supported-data-types"></a>支持的数据类型
Azure 诊断扩展在 Azure 存储帐户中存储数据。 对于 Azure 监视器日志要收集此数据，它必须位于以下位置：

| 日志类型 | 资源类型 | 位置 |
| --- | --- | --- |
| IIS 日志 |虚拟机 <br> Web 角色 <br> 辅助角色 |wad-iis-logfiles（Blob 存储） |
| Syslog |虚拟机 |LinuxsyslogVer2v0（表存储） |
| Service Fabric 操作事件 |Service Fabric 节点 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 节点 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 节点 |WADServiceFabricReliableServiceEventTable |
| Windows 事件日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADWindowsEventLogsTable（表存储） |
| Windows ETW 日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADETWEventTable（表存储） |

## <a name="data-types-not-supported"></a>不支持数据类型

- 来自来宾操作系统的性能数据
- 来自 Azure 网站的 IIS 日志


## <a name="enable-azure-diagnostics-extension"></a>启用 Azure 诊断扩展
有关[安装和配置诊断扩展的详细信息，请参阅安装和配置 Windows Azure 诊断扩展 （WAD）](diagnostics-extension-windows-install.md)或使用[Linux 诊断扩展来监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md)。 这将让您指定存储帐户，并配置要转发到 Azure 监视器日志的数据的集合。


## <a name="collect-logs-from-azure-storage"></a>从 Azure 存储收集日志
使用以下过程启用从 Azure 存储帐户收集诊断扩展数据：

1. 在 Azure 门户中，转到**日志分析工作区**并选择工作区。
1. 单击菜单的"**工作区数据源"** 部分中的 **"存储帐户日志**"。
2. 单击"**添加**"。
3. 选择包含要收集的数据的**存储帐户**。
4. 选择要收集的**数据类型**。
5. 根据数据类型自动填充源的值。
6. 单击“确定”**** 保存配置。
7. 对其他数据类型重复上述步骤。

在大约 30 分钟过后，能够在 Log Analytics 工作区中看到存储帐户中的数据。 在应用了配置后，只能看到写入到存储中的数据。 工作区不会从存储帐户中读取预先存在的数据。

> [!NOTE]
> 门户不验证源是否存在于存储帐户中或是否正在写入新数据。



## <a name="next-steps"></a>后续步骤

* 针对支持的 Azure 服务[收集 Azure 服务的日志和指标](collect-azure-metrics-logs.md)。
* [启用解决方案](../../azure-monitor/insights/solutions.md)深入分析数据。
* [使用搜索查询](../../azure-monitor/log-query/log-query-overview.md)分析数据。
