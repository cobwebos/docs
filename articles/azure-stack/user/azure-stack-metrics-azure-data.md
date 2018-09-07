---
title: 在 Azure Stack 上的 azure 监视器 |Microsoft Docs
description: 了解有关 Azure Stack 上的 Azure 监视器的信息。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: b0cf2d7856a78bbe2aa531c6e872168e8e33b06a
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021805"
---
# <a name="azure-monitor-on-azure-stack"></a>在 Azure Stack 上的 azure 监视器

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文概述 Azure Stack 中的 Azure Monitor 服务。 它讨论 Azure Monitor 的操作以及如何在 Azure Stack 上使用 Azure Monitor 的其他信息。 

有关的介绍，概述，以及如何开始使用 Azure Monitor，请参阅全局 Azure 文章[开始使用 Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)。

![Azure Stack 监视器边栏选项卡](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure 监视器是一款平台服务，提供了用于监视 Azure 资源的单一源。 通过 Azure Monitor，可直观显示、查询、路由和存档来自 Azure 内部资源的指标和日志并对其采取其他操作。 通过使用 Azure Stack 管理员门户中，监视器 PowerShell Cmdlet、 跨平台 CLI 或 Azure Monitor REST Api，可以使用此数据。 Azure Stack 支持的特定连接，请参阅[如何使用 Azure Stack 中的监视数据](azure-stack-metrics-monitor.md)

> [!Note]  
指标和诊断日志不可用于 Azure Stack 开发工具包。

## <a name="prerequisites"></a>必备组件

注册**Microsoft.insights**上你的订阅产品/服务资源提供程序设置的资源提供程序。 你可以验证资源提供程序现已推出产品/服务与你的订阅相关联：

1. 打开 Azure Stack 管理员门户。
2. 选择**提供了**。
3. 选择与订阅关联的产品/服务。
4. 选择**资源提供程序**下**设置。** 
5. 查找**Microsoft.Insights**列表中，并验证状态是否**已注册。**。

## <a name="overview"></a>概述

在 Azure 上的 Azure Monitor，如 Azure Stack 上的 Azure Monitor 为大多数服务提供基本级别基础结构指标和日志。

## <a name="azure-monitor-sources-compute-subset"></a>Azure monitor 源： 计算子集

![Azure monitor 源-计算子集](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

**Microsoft.Compute**中 Azure Stack 资源提供程序包括：
 - 虚拟机 
 - 虚拟机规模集

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>应用程序-诊断日志、 应用程序日志和指标

应用程序可以运行 VM 的 OS 中运行**Microsoft.Compute**资源提供程序。 这些应用程序和虚拟机发出其自己的日志和指标集。 Azure Monitor 依赖于 Azure 诊断扩展（Windows 或 Linux）来收集大多数应用程序级指标和日志。 

度量值的类型包括：
 - 性能计数器
 - 应用程序日志
 - Windows 事件日志
 - .NET 事件源
 - IIS 日志
 - 基于清单的 ETW
 - 故障转储
 - 客户错误日志

> [!Note]  
> 不支持 Azure Stack 上的 Linux 诊断扩展。

### <a name="host-and-guest-vm-metrics"></a>宿主和来宾 VM 指标

前面列出的计算资源都具有专用的宿主 VM 和来宾操作系统。 宿主 VM 和来宾 OS 是根 VM 和来宾 VM 中的 HYPER-V 虚拟机监控程序的等效项。 可为 VM 的主机和来宾操作系统收集指标。 此外，你可以收集来宾 OS 诊断日志。 Azure Stack 上的主机和来宾 VM 指标可回收的度量值的列表位于[支持在 Azure Stack 上的 Azure Monitor 指标](azure-stack-metrics-supported.md)。 

### <a name="activity-log"></a>活动日志

有关你的计算资源由 Azure Stack 基础结构所示，可以搜索活动日志的信息。 日志包含多种信息，例如创建或销毁资源的时间。 Azure Stack 上的活动日志是与 Azure 一致。 有关详细信息，请参阅的说明[在 Azure 上的活动日志概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)。 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor 源： 所有其他内容

![Azure monitor 源-所有其他内容](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>资源-指标和诊断日志

可回收的指标和诊断日志因资源类型。 对于 Azure Stack 上的每个资源可回收的度量值的列表是可在支持的指标。 有关详细信息，请参阅[支持在 Azure Stack 上的 Azure Monitor 指标](azure-stack-metrics-supported.md)。

### <a name="activity-log"></a>活动日志

活动日志是相同的计算资源。 

### <a name="uses-for-monitoring-data"></a>用于监视数据

**存储和存档**  

某些监视数据已存储并且在设定的时间段内在 Azure Monitor 中可用。 
 - 指标存储 90 天。 
 - 活动日志条目存储 90 天。 
 - 不存储诊断日志。
 - 存档到存储帐户进行更长时间保留数据。

**查询**  

可以使用 Azure Monitor REST API、 跨平台命令行接口 (CLI) 命令、 PowerShell cmdlet 或.NET SDK 访问系统或 Azure 存储中的数据。 

**可视化效果**

以图形和图表形式将监视数据可视化可以帮助你更快地查明趋势，其速度远非单纯查看数据可比。 

可视化方法包括：
 - 使用 Azure Stack 用户和管理员门户
 - 将数据路由到 Microsoft Power BI
 - 通过实时数据流将数据路由到第三方可视化工具，或者让第三方可视化工具从 Azure 存储中读取存档。

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>访问 Azure 的方法监视 Azure Stack 上

一般情况下，可以使用下述方法之一操作数据的跟踪、路由和检索。 并非所有方法都适用于所有操作或数据类型。

 - [Azure Stack 门户](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-use-portal)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [跨平台命令行 Interface(CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Stack 上一文中的监视数据消耗选项[监视 Azure Stack 中的数据的使用](azure-stack-metrics-monitor.md)。
