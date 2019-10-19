---
title: 什么是用于 VM 的 Azure Monitor（预览版）？ | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure Monitor 的一项功能，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文提供了概述。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 2364218a9ccbde1cbfc276e3354cb93e42e5ce0b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553750"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什么是用于 VM 的 Azure Monitor（预览版）？

用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 

它包括对监视本地或其他云提供程序中的 Vm 的性能和应用程序依赖关系的支持。 三个主要功能提供深入的见解：

- **运行 Windows 和 Linux 的 Azure vm 和虚拟机规模集的逻辑组件**针对预配置的运行状况标准进行度量，并在满足计算条件时向你发出警报。  

- **预定义的趋势性能图表**：显示来宾 VM 操作系统中的核心性能指标。

- **依赖关系映射**：显示来自不同资源组和订阅的 VM 的相互连接组件。  

这些功能分成三个透视图：

- 医疗业
- 性能
- 映射

>[!NOTE]
>我们最近[宣布了更改](https://azure.microsoft.com/updates/upcoming-changes-for-azure-monitor-for-vms-as-we-prepare-for-ga)，我们将根据我们从公共预览版客户收到的反馈来更改运行状况功能。 考虑到我们将进行的更改数，我们将停止为新客户提供运行状况功能。 现有客户可继续使用运行状况功能。 有关更多详细信息，请参阅我们的[公开上市常见问题解答](vminsights-ga-release-faq.md)。  

与 Azure Monitor 日志集成提供了强大的聚合和筛选功能，并可随时分析数据趋势。 单独使用 Azure Monitor 或服务映射无法实现此类全面的工作负荷监视。  

可直接从虚拟机在单个 VM 中查看此数据，也可使用 Azure Monitor 提供 VM 的聚合视图。 此视图基于每个功能的透视图：

- **运行状况**： vm 与资源组相关。
- **映射**和**性能**： vm 配置为向特定 Log Analytics 工作区报告。

![Azure 门户中的虚拟机见解透视图](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

适用于 VM 的 Azure Monitor 可以提供可预测的性能和重要应用程序的可用性。 它标识关键的操作系统事件、性能瓶颈和网络问题。 适用于 VM 的 Azure Monitor 还有助于理解某个问题是否与其他依赖关系相关。  

## <a name="data-usage"></a>数据使用情况

部署用于 VM 的 Azure Monitor 时，系统会引入 VM 收集的数据并将其存储在 Azure Monitor 中。 运行状况条件指标存储在时序数据库的 Azure Monitor 中，收集的性能和依赖关系数据存储在 Log Analytics 工作区中。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对用于 VM 的 Azure Monitor 进行计费：

- 引入和存储的数据。
- 受监视的运行状况条件指标时序的数量。
- 创建的预警规则。
- 发送的通知。 

日志大小根据性能计数器的字符串长度而变化，并且可以增加逻辑磁盘数和分配给 VM 的网络适配器数。 如果你已有一个工作区并且正在收集这些计数器，则不会重复收费。 如果已在使用服务映射，则你看到的唯一变化是发送到 Azure Monitor 的额外连接数据。

## <a name="next-steps"></a>后续步骤

请参阅[部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md)，了解有助于监视虚拟机的要求和方法。
