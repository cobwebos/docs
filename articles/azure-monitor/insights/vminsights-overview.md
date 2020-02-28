---
title: 什么是用于 VM 的 Azure Monitor（预览版）？ | Microsoft Docs
description: 用于 VM 的 Azure Monitor 的概述，它监视 Azure Vm 的运行状况和性能，以及自动发现和映射应用程序组件及其依赖项。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 1dcce3ab9f975fcf5910c382df3489d5d4ed425a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670656"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>什么是用于 VM 的 Azure Monitor（预览版）？

用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 

它包括对监视本地或其他云提供程序中的 Vm 的性能和应用程序依赖关系的支持。 以下主要功能提供了深入了解：

- **预定义的趋势性能图表**：显示来宾 VM 操作系统中的核心性能指标。

- **依赖关系映射**：显示来自不同资源组和订阅的 VM 的相互连接组件。  

>[!NOTE]
>我们最近根据我们从公共预览版客户收到的反馈，宣布了对运行状况功能所[做的更改](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)。 考虑到我们将进行的更改数，我们将停止为新客户提供运行状况功能。 现有客户可继续使用运行状况功能。 有关更多详细信息，请参阅我们的[公开上市常见问题解答](vminsights-ga-release-faq.md)。  

与 Azure Monitor 日志集成提供了强大的聚合和筛选功能，并可随时分析数据趋势。 单独使用 Azure Monitor 或服务映射无法实现此类全面的工作负荷监视。  

你可以直接从虚拟机的单个 VM 中查看此数据，也可以使用 Azure Monitor 提供 Vm 的聚合视图，其中视图支持 Azure 资源上下文或工作区上下文模式。 有关详细信息，请参阅[访问模式概述](../platform/design-logs-deployment.md#access-mode)。

![Azure 门户中的虚拟机见解透视图](./media/vminsights-overview/vminsights-azmon-directvm.png)

适用于 VM 的 Azure Monitor 可以提供可预测的性能和重要应用程序的可用性。 它识别性能瓶颈和网络问题。 适用于 VM 的 Azure Monitor 还有助于理解某个问题是否与其他依赖关系相关。  

## <a name="data-usage"></a>数据使用情况

部署用于 VM 的 Azure Monitor 时，系统会引入 VM 收集的数据并将其存储在 Azure Monitor 中。 收集的性能和依赖关系数据存储在 Log Analytics 工作区中。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对用于 VM 的 Azure Monitor 进行计费：

- 引入和存储的数据。
- 创建的预警规则。
- 发送的通知。 

日志大小根据性能计数器的字符串长度而变化，并且可以增加逻辑磁盘数和分配给 VM 的网络适配器数。 如果你已有一个工作区并且正在收集这些计数器，则不会重复收费。 如果已在使用服务映射，则你看到的唯一变化是发送到 Azure Monitor 的额外连接数据。

## <a name="next-steps"></a>后续步骤

请参阅[部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md)，了解有助于监视虚拟机的要求和方法。
