---
title: 什么是用于 VM 的 Azure Monitor？
description: VM 的 Azure 监视器概述，除了自动发现和映射应用程序组件及其依赖项外，还监视 Azure VM 的运行状况和性能。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480481"
---
# <a name="what-is-azure-monitor-for-vms"></a>什么是用于 VM 的 Azure Monitor？

用于 VM 的 Azure Monitor 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 它支持监视本地或其他云提供程序中托管的 VM 的性能和应用程序依赖项。 以下关键功能提供深入洞察：

- **预定义的趋势性能图表**：显示来宾 VM 操作系统的核心性能指标。

- **依赖项映射**：显示来自各种资源组和订阅的 VM 的互连组件。  

>[!NOTE]
>我们最近宣布[，](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)我们将根据从公共预览客户收到的反馈对"健康"功能进行更改。 鉴于我们将进行的数量变化，我们将停止为新客户提供健康功能。 现有客户可以继续使用运行状况功能。 有关详细信息，请参阅我们的[通用常见问题解答](vminsights-ga-release-faq.md)。  

与 Azure 监视器日志集成可提供强大的聚合和筛选，使 VM 的 Azure 监视器能够分析随时间变化的数据趋势。 可以直接从虚拟机在单个 VM 中查看此数据，也可以使用 Azure 监视器提供 VM 的聚合视图，其中视图支持 Azure 资源上下文或工作区上下文模式。 有关详细信息，请参阅[访问模式概述](../platform/design-logs-deployment.md#access-mode)。

![Azure 门户中的虚拟机见解透视图](media/vminsights-overview/vminsights-azmon-directvm.png)

适用于 VM 的 Azure Monitor 可以提供可预测的性能和重要应用程序的可用性。 它可识别性能瓶颈和网络问题，还可以帮助您了解问题是否与其他依赖关系相关。  

## <a name="data-usage"></a>数据使用情况

部署用于 VM 的 Azure Monitor 时，系统会引入 VM 收集的数据并将其存储在 Azure Monitor 中。 收集的性能和依赖项数据存储在日志分析工作区中。 根据在 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，将针对以下内容对用于 VM 的 Azure Monitor 进行计费：

- 引入和存储的数据。
- 创建的预警规则。
- 发送的通知。 

日志大小因性能计数器的字符串长度而异，并且可能会随分配给 VM 的逻辑磁盘和网络适配器的数量而增大。 如果你已有一个工作区并且正在收集这些计数器，则不会重复收费。 如果您已在使用服务映射，您将看到的唯一更改是发送到 Azure 监视器的其他连接数据。

## <a name="next-steps"></a>后续步骤

请参阅[部署用于 VM 的 Azure Monitor](vminsights-enable-overview.md)，了解有助于监视虚拟机的要求和方法。
