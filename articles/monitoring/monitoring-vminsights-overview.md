---
title: 什么是用于 VM 的 Azure Monitor？ | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure Monitor 的一项功能，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文提供了概述。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 79f507c342f5a13c4d3784cf312f0bf8aeffa3e3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957240"
---
## <a name="what-is-azure-monitor-for-vms"></a>什么是用于 VM 的 Azure Monitor？

用于 VM 的 Azure Monitor 分析 Windows 和 Linux VM 的性能与运行状况，包括其不同的进程以及与其他资源和外部进程之间的相互依赖关系，可以大规模监视 Azure 虚拟机 (VM)。 该解决方案支持监视本地或其他云提供程序中托管的 VM 的性能和应用程序依赖项。  它包括了用来提供此深度见解的三个主要功能：

* 当满足评估的条件时，将根据一组预先配置的运行状况条件和警报对运行 Windows 和 Linux 操作系统的 Azure VM 进行度量。  
* 在预先定义的趋势性能图表中收集和呈现来自来宾 VM 操作系统的处理器、内存、磁盘和网络适配器的核心性能指标。
* 依赖关系映射，显示发现的来自多个资源组和订阅的与该 VM 互连的组件。  

这些功能组织为三个视角：

* 运行状况
* 性能
* 映射

>[!NOTE]
>目前，对于 Azure 虚拟机，仅提供了运行状况功能。
>

与 Log Analytics 的集成提供了功能强大的聚合、筛选和随着时间推移执行数据趋势分析的能力。 单独使用 Azure Monitor、服务映射或 Log Analytics 无法实现全面的工作负荷监视。  

可以直接从虚拟机在单台 VM 的上下文中查看此数据，或者使用 Azure Monitor，它基于每项功能的以下视角提供了 VM 的聚合视图：

* 运行状况 - 与资源组相关的 VM
* 映射和性能 - 配置为向特定的 Log Analytics 工作区进行报告的 VM

![门户中的虚拟机见解视角](./media/monitoring-vminsights-overview/vminsights-azmon-directvm-01.png)

DevOps 可以通过查明严重的操作系统事件和性能瓶颈、网络问题来有效地使重要应用程序提供可预测的性能和可用性，并了解某个问题是否与其他依赖项有关。  

## <a name="data-usage"></a>数据使用情况 

一旦你载入了用于 VM 的 Azure Monitor，将会立即引入由 VM 收集的数据并将其存储在 Azure Monitor 中。  将根据在 Azure Monitor [定价页面](https://azure.microsoft.com/pricing/details/monitor/)上发布的定价，针对引入和保留的数据、监视的运行状况条件指标时序的数量、创建的警报规则、发送的通知对用于 VM 的 Azure Monitor 进行计费。

日志大小根据计数器的字符串长度变化，并且可能会随逻辑磁盘和网络适配器的数量而增大。  如果你已有一个工作区并且正在收集这些计数器，则不会重复收费。  如果已在使用服务映射，则你看到的唯一变化是发送到 Azure Monitor 的额外连接数据。

## <a name="next-steps"></a>后续步骤
[载入用于 VM 的 Azure Monitor](monitoring-vminsights-onboard.md) 以开始监视 Azure 虚拟机。
