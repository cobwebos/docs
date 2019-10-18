---
title: 如何使用用于 VM 的 Azure Monitor（预览版）查看应用依赖项 | Microsoft Docs
description: Map 是用于 VM 的 Azure Monitor 的一项功能。 它会自动发现 Windows 和 Linux 系统上的应用程序组件，并映射服务之间的通信。 本文提供了有关如何在各种情况下使用地图功能的详细信息。
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
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 456ed0a48db015d3c95827942a576e6916095131
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515437"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>使用用于 VM 的 Azure Monitor （预览版）的地图功能了解应用程序组件
在用于 VM 的 Azure Monitor 中，可以查看在 Azure 或环境中运行的 Windows 和 Linux 虚拟机（Vm）上发现的应用程序组件。 可以通过两种方式观察 Vm。 直接从 VM 查看地图，或从 Azure Monitor 查看地图，查看跨 Vm 组的组件。 本文将帮助你了解这两种查看方法，以及如何使用地图功能。 

有关配置适用于 VM 的 Azure Monitor 的信息，请参阅[启用适用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

## <a name="sign-in-to-azure"></a>登录 Azure
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-the-map-experience"></a>地图体验简介
在深入探讨地图体验之前，您应该了解它如何呈现和可视化信息。 无论是直接从 VM 选择地图功能还是从 Azure Monitor 中选择地图功能，地图功能都可以提供一致的体验。 唯一的区别是，从 Azure Monitor 中，一个映射显示多层应用程序或群集的所有成员。

映射功能通过发现正在运行的进程来直观显示 VM 依赖项： 

- 服务器之间的活动网络连接。
- 入站和出站连接延迟。
- 在指定时间范围内跨任何 TCP 连接的体系结构的端口。  
 
展开 VM 以显示进程详细信息，并且仅显示与 VM 通信的进程。 客户端组显示连接到 VM 的前端客户端的计数。 服务器端口组显示 VM 连接到的后端服务器的计数。 展开服务器端口组，查看通过该端口连接的服务器的详细列表。  

选择 VM 时，右侧的 "**属性**" 窗格将显示 vm 的属性。 属性包括操作系统报告的系统信息、Azure VM 的属性，以及汇总发现的连接的圆环图。 

!["属性" 窗格](./media/vminsights-maps/properties-pane-01.png)

在窗格的右侧，选择 "**日志事件**" 以显示 VM 发送到 Azure Monitor 的数据列表。 此数据可用于查询。  选择 "任何记录类型" 打开 "**日志**" 页，您可以在其中看到该记录类型的结果。 还会看到根据 VM 筛选的预配置查询。  

!["日志事件" 窗格](./media/vminsights-maps/properties-pane-logs-01.png)

关闭 "**日志**" 页并返回到 "**属性**" 窗格。 在其中，选择 "**警报**" 以查看 VM 运行状况-条件警报。 地图功能与 Azure 警报集成，以显示所选时间范围内所选服务器的警报。 服务器显示当前警报的图标，"**计算机警报**" 窗格会列出警报。 

!["警报" 窗格](./media/vminsights-maps/properties-pane-alerts-01.png)

若要使地图功能显示相关警报，请创建适用于特定计算机的警报规则：

- 包括子句以便按计算机对警报进行分组（例如，**按计算机时间间隔1分钟**）。
- 基于指标的警报。

有关 Azure 警报和创建警报规则的详细信息，请参阅[Azure Monitor 中的统一警报](../../azure-monitor/platform/alerts-overview.md)。

在右上角，**图例**选项描述了地图上的符号和角色。 若要深入了解地图并四处移动，请使用右下角的缩放控件。 可以设置缩放级别，并将地图调整到页面大小。  

## <a name="connection-metrics"></a>连接指标
"**连接**" 窗格显示通过 TCP 端口从 VM 选择的连接的标准指标。 指标包括响应时间、每分钟的请求数、流量吞吐量和链接。  

!["连接" 窗格中的网络连接图](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失败的连接
该地图显示进程和计算机的失败连接。 红色虚线表示客户端系统无法访问进程或端口。 对于使用依赖关系代理的系统，代理会报告失败的连接尝试。 映射功能通过观察无法建立连接的 TCP 套接字来监视进程。 此失败的原因可能是防火墙、客户端或服务器中的配置错误或远程服务不可用。

![地图上的连接失败](./media/vminsights-maps/map-group-failed-connection-01.png)

了解失败的连接可帮助进行故障排除，验证迁移，分析安全性，并了解服务的总体体系结构。 失败的连接有时会无害，但通常会指出问题。 例如，如果故障转移环境突然变为不可访问，或两个应用程序层在云迁移后无法相互通信，则连接可能会失败。

### <a name="client-groups"></a>客户端组
在映射中，客户端组表示连接到映射的计算机的客户端计算机。 单个客户端组表示单个进程或计算机的客户端。

![地图上的客户端组](./media/vminsights-maps/map-group-client-groups-01.png)

若要查看客户端组中系统的监视的客户端和 IP 地址，请选择该组。 组的内容如下所示。  

![客户组在映射上的 IP 地址列表](./media/vminsights-maps/map-group-client-group-iplist-01.png)

如果组包括监视和未监视的客户端，则可以选择组的圆环图的相应部分来筛选客户端。

### <a name="server-port-groups"></a>服务器端口组
服务器端口组表示服务器上的端口，这些端口具有来自映射计算机的入站连接。 该组包含服务器端口，以及连接到该端口的服务器的数目。 选择组以查看各个服务器和连接。 

![地图上的服务器端口组](./media/vminsights-maps/map-group-server-port-groups-01.png)  

如果组包括监视和未监视的服务器，您可以选择组的圆环图的相应部分来筛选服务器。

## <a name="view-a-map-from-a-vm"></a>查看 VM 的地图 

直接从 VM 访问用于 VM 的 Azure Monitor：

1. 在 Azure 门户中，选择“虚拟机”。 
2. 从列表中选择一个 VM。 在 "**监视**" 部分，选择 "**见解（预览版）** "。  
3. 选择“映射”选项卡。

该地图通过发现在指定时间范围内具有活动的网络连接的运行中进程组和进程，直观显示 VM 的依赖关系。  

默认情况下，映射显示最近 30 分钟。 如果要了解依赖关系在过去的情况，可以查询最多一小时的历史时间范围。 若要运行查询，请使用左上角的**TimeRange**选择器。 例如，你可以在事件期间运行查询，或在更改前查看状态。  

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>从虚拟机规模集查看地图

若要直接从虚拟机规模集访问用于 VM 的 Azure Monitor：

1. 在 Azure 门户中，选择 "**虚拟机规模集**"。
2. 从列表中选择一个 VM。 然后在 "**监视**" 部分，选择 "**见解（预览版）** "。  
3. 选择“映射”选项卡。

地图将规模集中的所有实例直观显示为组节点以及组的依赖项。 展开的节点会列出规模集中的实例。 你可以一次滚动这些实例10个。 

若要为特定实例加载映射，请先在映射上选择该实例。 然后选择右侧的**省略号**按钮（...），然后选择 "**加载服务器映射**"。 在显示的地图中，可以看到在指定时间范围内具有活动的网络连接的进程组和进程。 

默认情况下，映射显示最近 30 分钟。 如果要了解依赖关系在过去的情况，可以查询最多一小时的历史时间范围。 若要运行查询，请使用**TimeRange**选择器。 例如，你可以在事件期间运行查询，或在更改前查看状态。

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>你还可以从虚拟机规模集的**实例**视图访问特定实例的映射。 在 "**设置**" 部分中，参阅 "**实例** > **Insights （预览版）** "。

## <a name="view-a-map-from-azure-monitor"></a>从 Azure Monitor 查看地图

在 Azure Monitor 中，地图功能提供了 Vm 及其依赖项的全局视图。 若要访问中的映射功能 Azure Monitor：

1. 在 Azure 门户中选择“监视”。 
2. 在 "**见解**" 部分，选择 "**虚拟机（预览版）** "。
3. 选择“映射”选项卡。

   ![Azure Monitor 多个 Vm 的概述图](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

使用页面顶部的**工作区**选择器选择工作区。 如果有多个 Log Analytics 工作区，请选择通过解决方案启用的工作区，并为其提供向其报告的 Vm。 

**组**选择器将返回与所选工作区相关的订阅、资源组、[计算机组](../../azure-monitor/platform/computer-groups.md)和虚拟机规模集。 你的选择仅适用于地图功能，且不会执行性能或运行状况。

默认情况下，映射显示最近 30 分钟。 如果要了解依赖关系在过去的情况，可以查询最多一小时的历史时间范围。 若要运行查询，请使用**TimeRange**选择器。 例如，你可以在事件期间运行查询，或在更改前查看状态。  

## <a name="next-steps"></a>后续步骤

若要确定瓶颈、查看性能并了解 Vm 的总体利用率，请参阅[查看用于 VM 的 Azure Monitor 的性能状态](vminsights-performance.md)。 
