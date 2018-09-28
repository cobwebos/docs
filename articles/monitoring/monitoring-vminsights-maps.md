---
title: 如何使用适用于 VM 的 Azure Monitor 查看应用依赖性 | Microsoft Docs
description: 映射是适用于 VM 的 Azure Monitor 的功能，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文详细介绍如何在各种场景中使用映射。
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
ms.date: 09/17/2018
ms.author: magoedte
ms.openlocfilehash: 30f9a874853f4cb492d7a562d0d2439b73cf62dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989968"
---
# <a name="using-azure-monitor-for-vms-map-to-understand-application-components"></a>使用适用于 VM 的 Azure Monitor 映射了解应用程序组件
查看在 Azure 中运行的 Windows 和 Linux 虚拟机上发现的应用程序组件，并且可使用适用于 VM 的 Azure Monitor 通过两种方式观察环境，直接从虚拟机中或通过 VM 组在 Azure Monitor 中观察。 

可以通过本文了解这两个角度的不同体验，以及如何使用映射功能。 有关配置适用于 VM 的 Azure Monitor 的信息，请参阅[启用适用于 VM 的 Azure Monitor](monitoring-vminsights-onboard.md)。

## <a name="sign-in-to-azure"></a>登录 Azure
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="introduction-to-map-experience"></a>映射体验简介
在深入探讨如何针对单个虚拟机或 VM 组查看映射之前，我们必须提供此功能的相关简介，使读者了解信息的显示方式，以及要显示哪些可视化效果。  

无论是直接从 VM 或 Azure Monitor 中选择映射功能，都能提供一致的体验。  唯一的区别是通过 Azure Monitor，可在一个映射中看到多层应用程序或群集的所有成员。

映射通过使用服务器之间的有效网络连接、入站和出站连接延迟和指定时间范围内任何已连接 TCP 体系结构中的端口数发现正在运行的进程，从而直观显示 VM 依赖项。  展开 VM 将显示进程详细信息，并且仅显示与 VM 通信的进程。 使用客户端组指示连接到虚拟机的前端客户端计数。 服务器端口组上指示 VM 连接到的后端服务器的计数。 展开服务器端口组，查看通过该端口连接的服务器的详细列表。  

单机虚拟机时，右侧的“属性面板”窗格将展开，显示所选项的属性，如操作系统报告的系统信息、Azure VM 的属性和汇总已发现连接的圆环图。 

![计算机的系统属性](./media/monitoring-vminsights-maps/properties-pane-01.png)

在右侧的窗格中，单击“日志事件”图标切换窗格的焦点，显示已发送至 Log Analytics 并可供查询的表格列表（从 VM 中收集）。  单击列出的任一记录类型将打开“日志”页，查看该类型的结果，其中包含针对特定虚拟机筛选的预配置的查询。  

![“属性面板”窗格中的日志搜索列表](./media/monitoring-vminsights-maps/properties-pane-logs-01.png)

关闭“日志”*并返回“属性面板”窗格，然后选择“警报”，以查看根据运行状况条件针对 VM 引发的警报。 映射与 Azure 警报集成，可显示所选时间范围内所选服务器触发的警报。 如果当前有警报，服务器会显示一个图标，且“计算机警报”窗格会列出警报。 

![“属性面板”窗格中的计算机警报](./media/monitoring-vminsights-maps/properties-pane-alerts-01.png)

若要使映射功能显示相关警报，请创建针对特定计算机触发的警报规则。 创建相应的警报：
- 包含一个子句，以按计算机分组（例如“by Computer interval 1 minute”）。
- 根据指标度量值选择警报。

有关 Azure 警报和创建警报规则的详细信息，请参阅 [Azure Monitor 中的统一警报](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)

右上角的“图例”选项描述地图上的符号和角色。  若要放大以进一步查看地图并四处移动，可使用页面右下角的“缩放”控件设置缩放级别并使页面适应当前页的大小。  

## <a name="connection-metrics"></a>连接指标
“连接”窗格可显示通过 TCP 端口 VM 中所选连接的标准连接指标。 指标包括响应时间、每分钟的请求数、流量吞吐量和链接。  

![网络连接图表窗格示例](./media/monitoring-vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失败的连接
对于进程和计算机，失败的连接显示在映射中，并显示一条红色虚线，指示客户端系统无法访问进程或端口。 如果具有依赖项代理的任何系统正尝试失败的连接，将从该系统报告失败的连接。 映射通过观察无法建立连接的 TCP 套接字测量此过程。 出现失败连接可能是因为防火墙、客户端或服务器中的配置错误或远程服务不可用。

![映射上失败的连接示例](./media/monitoring-vminsights-maps/map-group-failed-connection-01.png)

了解失败的连接可帮助进行疑难解答、迁移验证、安全分析和了解服务的总体体系结构。 有时失败的连接无害，但它们通常直接指向某个问题，例如故障转移环境突然无法访问，或两个应用程序层在进行云迁移后无法彼此通信。

### <a name="client-groups"></a>客户端组
映射上的客户端组表示已连接到映射计算机的客户端计算机。 单个客户端组表示单个进程或计算机的客户端。

![映射上的客户端组示例](./media/monitoring-vminsights-maps/map-group-client-groups-01.png)

若要查看客户端组中的受监控客户端和系统的 IP 地址，请选择该组。 组下面列出了组的内容。  

![映射上的客户端组 IP 地址列表示例](./media/monitoring-vminsights-maps/map-group-client-group-iplist-01.png)

如果组包括受监视和未受监视的客户端，可以在组上选择圆环图的相应部分来筛选客户端。

### <a name="server-port-groups"></a>服务器端口组
服务器端口组表示具有源自映射计算机的入站连接的服务器上的服务器端口。 组中包含服务器端口，以及连接到该端口的服务器计数。 选择此组可查看列出的各个服务器和连接。 

![映射上的服务器端口组示例](./media/monitoring-vminsights-maps/map-group-server-port-groups-01.png)  

如果组包括受监视和未受监视的服务器，可以在组上选择圆环图的相应部分来筛选服务器。

## <a name="view-map-directly-from-a-virtual-machine"></a>直接从虚拟机查看映射 

若要直接从虚拟机访问适用于 VM 的 Azure Monitor，请执行以下步骤。

1. 在 Azure 门户中，选择“虚拟机”。 
2. 在列表中，选择 VM 并“监视”分区中选择“见解(预览)”。  
3. 选择“映射”选项卡。

映射可直观显示 VM 依赖项，即在指定时间范围内通过活动网络连接运行进程组和进程。  默认情况下，映射显示最近 30 分钟。  使用左上角的“时间范围”选择器，可查询历史时间范围（最多一小时），显示依赖项在过去（例如，发生事件期间或发生更改之前）的出现形式。  

![直接 VM 映射概述](./media/monitoring-vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>从 Azure Monitor 查看映射
通过 Azure Monitor，映射功能可提供虚拟机及其依赖项的全局视图。  若要从 Azure Monitor 访问映射功能，请执行以下步骤。 

1. 在 Azure 门户中，选择“监视”。 
2. 在“见解”分区中选择“虚拟机(预览)”。
3. 选择“映射”选项卡。

![Azure Monitor 多 VM 映射概述](./media/monitoring-vminsights-maps/map-multivm-azure-monitor-01.png)

如果你有多个 Log Analytics 工作区，在页面顶部的“工作区”选择器中，请选择与解决方案集成并已向其报告虚拟机的工作区。  然后，在“组”选择器中选择订阅或资源组，以查看指定时间段内，与该组匹配的 VM 集及其依赖项。  默认情况下，映射显示最近 30 分钟。  使用“时间范围”选择器，可查询历史时间范围（最多一小时），显示依赖项在过去（例如，发生事件期间或发生更改之前）的出现形式。   

## <a name="next-steps"></a>后续步骤
若要了解如何使用运行状况功能，请参阅[查看 Azure VM 运行状况](monitoring-vminsights-health.md)；或者，若要确定 VM 性能的瓶颈和整体使用率，请参阅[查看适用于 VM 的 Azure Monitor 的性能](monitoring-vminsights-performance.md)。 
