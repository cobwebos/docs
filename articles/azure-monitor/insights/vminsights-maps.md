---
title: 如何使用用于 VM 的 Azure Monitor（预览版）查看应用依赖项 | Microsoft Docs
description: 映射是 Vm 的 Azure Monitor 的一项功能。 它会自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文提供有关如何在各种方案中使用的映射功能的详细信息。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206765"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>为 Vm （预览版） 使用 Azure Monitor 的映射功能来了解应用程序组件
在 Azure 监视器中的 Vm，可以查看发现的应用程序组件，在 Windows 和 Linux 虚拟机 (Vm) 在 Azure 或你的环境中运行。 您可以观察两种方法中的虚拟机。 查看映射直接从 VM 或查看从 Azure Monitor 来查看组件间的 Vm 组的映射。 本文将帮助你了解这两个查看方法以及如何使用地图功能。 

有关配置用于 VM 的 Azure Monitor 的信息，请参阅[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

## <a name="sign-in-to-azure"></a>登录 Azure
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-the-map-experience"></a>地图体验简介
在开始编写地图体验之前, 应该了解如何显示和直观显示信息。 无论是直接从 VM 或 Azure Monitor，请选择映射功能，映射功能提供了一致的体验。 唯一的区别是通过 Azure Monitor，一个代码图显示的多层应用程序或群集的所有成员。

地图功能通过发现正在运行的进程具有直观显示 VM 依赖项： 

- 活动的网络服务器之间的连接。
- 入站和出站连接延迟。
- 跨任何 TCP 连接的体系结构指定的时间范围内的端口。  
 
展开以显示进程详细信息和与 VM 通信的进程的 VM。 客户端组显示连接到 VM 的前端客户端的计数。 服务器端口组显示 VM 连接到后端服务器的计数。 展开要查看详细的列表，通过该端口连接的服务器的服务器端口组。  

当你选择的 VM，**属性**右侧窗格中的显示 VM 的属性。 属性包括系统信息报告的操作系统、 Azure VM 和圆环图，其中总结了已发现的连接的属性。 

![属性窗格](./media/vminsights-maps/properties-pane-01.png)

在窗格的右侧，选择**日志事件**以显示 VM 已发送到 Azure Monitor 的数据的列表。 此数据是可用于查询。  选择要打开的任何记录类型**日志**页上，将出现该记录类型的结果。 您还看到筛选针对 VM 的预配置的查询。  

![日志事件窗格](./media/vminsights-maps/properties-pane-logs-01.png)

关闭**日志**页上，又回到**属性**窗格。 选择**警报**以查看 VM 运行状况条件的警报。 地图功能与 Azure 警报，可显示所选的时间范围内所选服务器的警报。 服务器将显示当前警报的图标和**机警报**窗格会列出警报。 

![警报窗格](./media/vminsights-maps/properties-pane-alerts-01.png)

若要使显示相关警报的映射功能，创建适用于特定计算机的警报规则：

- 按计算机包括组警报子句 (例如，**由 Computer interval 1minute**)。
- 基本指标警报。

有关 Azure 警报和创建警报规则的详细信息，请参阅[统一 Azure Monitor 中的警报](../../azure-monitor/platform/alerts-overview.md)。

在右上角**图例**选项描述在代码图上的符号和角色。 要深入了解在您的映射和以移动该工具栏，在右下角中使用缩放控件。 可以设置的缩放级别，并适合映射到页的大小。  

## <a name="connection-metrics"></a>连接指标
**连接**窗格显示所选连接从虚拟机的标准指标通过 TCP 端口。 指标包括响应时间、每分钟的请求数、流量吞吐量和链接。  

![在连接窗格上的网络连接图表](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失败的连接
映射将显示有关进程和计算机的连接失败。 红色虚线指示客户端系统无法访问进程或端口。 对于使用依赖关系代理的系统，该代理报告的失败的连接尝试。 地图功能通过观察无法建立连接的 TCP 套接字监视进程。 此失败可能是因为防火墙，客户端或服务器或不可用的远程服务中的配置错误。

![在地图上失败的连接](./media/vminsights-maps/map-group-failed-connection-01.png)

了解失败的连接可以帮助您进行故障排除、 验证迁移、 分析安全性，并了解该服务的整体体系结构。 失败的连接有时无害，但它们通常指向问题。 例如，连接可能会失败或故障转移环境突然变得无法访问时两个应用程序层能云迁移后与彼此通信时。

### <a name="client-groups"></a>客户端组
在映射中，客户端组表示连接到映射的计算机的客户端计算机。 单个客户端组表示单个进程或计算机的客户端。

![在地图上一个客户端组](./media/vminsights-maps/map-group-client-groups-01.png)

若要查看受监视的客户端和客户端组中的系统的 IP 地址，选择的组。 下面显示的组的内容。  

![在地图上的 IP 地址的客户端组的列表](./media/vminsights-maps/map-group-client-group-iplist-01.png)

如果组包括监视和未受监视的客户端，则可以选择组的圆环图图表以筛选客户端的相应部分。

### <a name="server-port-groups"></a>服务器端口组
服务器端口组表示具有的服务器上的端口的入站连接从映射的计算机。 组包含服务器端口，以及到该端口已连接的服务器数的计数。 选择要查看各个服务器和连接的组。 

![在地图上为服务器端口组](./media/vminsights-maps/map-group-server-port-groups-01.png)  

如果组中包含的监视和未受监视服务器，则可以选择组的圆环图以筛选在服务器的相应部分。

## <a name="view-a-map-from-a-vm"></a>查看从 VM 的映射 

若要直接从 VM 的 vm 访问 Azure 监视器：

1. 在 Azure 门户中，选择“虚拟机”  。 
2. 从列表中，选择 VM。 在中**监视**部分中，选择**Insights （预览版）** 。  
3. 选择“映射”选项卡  。

地图可视化 VM 的依赖项通过发现正在运行进程组和指定的时间范围内有活动网络连接的进程。  

默认情况下，映射显示最近 30 分钟。 如果想要查看依赖关系在过去，您可以查询历史时间范围的最多一小时。 若要运行查询，请使用**TimeRange**中左上角选择器。 例如，发生事件期间，或者若要查看更改前的状态，则可以运行查询。  

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>查看从虚拟机规模集的映射

若要直接从虚拟机规模集的 Vm 访问 Azure 监视器：

1. 在 Azure 门户中，选择**虚拟机规模集**。
2. 从列表中，选择 VM。 然后在**监视**部分中，选择**Insights （预览版）** 。  
3. 选择“映射”选项卡  。

代码图直观显示规模集作为组的依赖项的组节点中的所有实例。 展开的节点中列出规模集中的实例。 您可以一次滚动浏览这些实例 10。 

若要加载特定实例的映射，首先选择该实例在地图上。 然后选择**省略号**右侧的按钮 （...），然后选择**加载服务器映射**。 在映射中显示，您将看到进程组和指定的时间范围内具有活动的网络连接的进程。 

默认情况下，映射显示最近 30 分钟。 如果想要查看依赖关系在过去，您可以查询历史时间范围的最多一小时。 若要运行查询，请使用**TimeRange**选择器。 例如，发生事件期间，或者若要查看更改前的状态，则可以运行查询。

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>您还可以访问一个特定实例的映射**实例**为虚拟机规模集的视图。 在中**设置**部分中，转到**实例** > **Insights （预览版）** 。

## <a name="view-a-map-from-azure-monitor"></a>查看从 Azure Monitor 的映射
在 Azure 监视器中，映射功能提供了你的 Vm 和它们的依赖项的全局视图。 若要访问 Azure Monitor 中的映射功能：

1. 在 Azure 门户中选择“监视”。  
2. 在中**Insights**部分中，选择**虚拟机 （预览版）** 。
3. 选择“映射”选项卡  。

   ![多个 Vm 的 azure 监视器摘要图](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

通过选择一个工作区**工作区**在页面顶部的选择器。 如果有多个 Log Analytics 工作区，选择工作区的启用了该解决方案，并且已向其报告的 Vm。 

**组**选择器返回订阅、 资源组[计算机组](../../azure-monitor/platform/computer-groups.md)，和的计算机与所选工作区相关的虚拟机规模集。 你的选择仅适用于映射功能，不会延续到性能或运行状况。

默认情况下，映射显示最近 30 分钟。 如果想要查看依赖关系在过去，您可以查询历史时间范围的最多一小时。 若要运行查询，请使用**TimeRange**选择器。 例如，发生事件期间，或者若要查看更改前的状态，则可以运行查询。  

## <a name="next-steps"></a>后续步骤
- 若要了解如何使用运行状况的功能，请参阅[查看 Azure VM 运行状况](vminsights-health.md)。 
- 若要标识瓶颈，检查性能，并了解 Vm 的整体利用率，请参阅[Vm 的 Azure Monitor 来查看性能状态](vminsights-performance.md)。 
