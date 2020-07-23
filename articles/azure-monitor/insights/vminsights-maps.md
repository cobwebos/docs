---
title: 使用用于 VM 的 Azure Monitor 查看应用依赖项
description: 映射是用于 VM 的 Azure Monitor 的一项功能。 它可以自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文提供有关如何在各种方案中使用映射功能的详细信息。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80283848"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>使用用于 VM 的 Azure Monitor 的地图功能来了解应用程序组件
在用于 VM 的 Azure Monitor 中，可以查看在 Azure 或你的环境中运行的 Windows 和 Linux 虚拟机 (VM) 上发现的应用程序组件。 可通过两种方式观察 VM。 查看直接从 VM 建立的映射，或查看从 Azure Monitor 建立的映射，以查看各个 VM 组中的组件。 本文将帮助你了解这两种查看方法，以及如何使用映射功能。 

有关配置用于 VM 的 Azure Monitor 的信息，请参阅[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

## <a name="sign-in-to-azure"></a>登录 Azure
登录 [Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-the-map-experience"></a>映射体验简介
在深入了解映射体验之前，应该了解它如何显示和可视化信息。 无论是直接从 VM 还是从 Azure Monitor 选择映射功能，它都能提供一致的体验。 唯一的区别是，通过 Azure Monitor 使用时，一个映射会显示多层应用程序或群集的所有成员。

映射功能通过发现正在运行的进程来可视化 VM 依赖项，包括： 

- 服务器之间的有效网络连接。
- 入站和出站连接延迟。
- 指定的时间范围内与 TCP 连接的任何体系结构中的端口。  
 
展开 VM 可显示进程详细信息，并且仅显示与 VM 通信的进程。 客户端组显示连接到 VM 的前端客户端计数。 服务器端口组显示 VM 连接到的后端服务器计数。 展开服务器端口组，查看通过该端口连接的服务器的详细列表。  

选择 VM 时，右侧的“属性”窗格将显示 VM 的属性。**** 属性包括操作系统报告的系统信息、Azure VM 的属性，以及汇总了已发现的连接的圆环图。 

![“属性”窗格](./media/vminsights-maps/properties-pane-01.png)

在窗格的右侧，选择“日志事件”显示 VM 发送到 Azure Monitor 的数据列表。**** 此数据可用于查询。  选择任一记录类型会打开“日志”页，在其中可以看到该记录类型的结果。**** 此外，还会看到根据 VM 筛选的预配置查询。  

![“日志事件”窗格](./media/vminsights-maps/properties-pane-logs-01.png)

关闭“日志”页并返回“属性”窗格。******** 选择“警报”查看 VM 运行状况条件警报。**** 映射功能与 Azure 警报集成，可显示所选时间范围内所选服务器的警报。 对于当前的警报，服务器会显示一个图标，且“计算机警报”窗格会列出警报。**** 

![“警报”窗格](./media/vminsights-maps/properties-pane-alerts-01.png)

若要使映射功能显示相关警报，请创建应用到特定计算机的警报规则：

- 包含一个子句，以按计算机将警报分组（例如“by Computer interval 1 minute”****）。
- 使警报基于指标。

有关 Azure 警报和创建警报规则的详细信息，请参阅[Azure Monitor 中的统一警报](../../azure-monitor/platform/alerts-overview.md)。

右上角的“图例”选项描述映射中的符号和角色****。 要深入了解映射并将其四处移动，请使用右下角的缩放控件。 可以设置缩放级别，并使映射适合页面大小。  

## <a name="connection-metrics"></a>连接指标
“连接”窗格显示通过 TCP 端口 VM 中所选连接的标准指标****。 指标包括响应时间、每分钟的请求数、流量吞吐量和链接。  

![“连接”窗格中的网络连接图表](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失败的连接
映射会显示进程和计算机的失败连接。 红色虚线表示相应客户端系统无法访问某个进程或端口。 对于使用依赖项代理的系统，该代理将报告失败的连接尝试。 映射功能通过观察无法建立连接的 TCP 套接字来监视进程。 发生这种失败的原因可能是防火墙问题、客户端或服务器配置不当，或远程服务不可用。

![映射中的失败连接](./media/vminsights-maps/map-group-failed-connection-01.png)

了解失败的连接可帮助进行故障排除、验证迁移、分析安全性，以及了解服务的总体体系结构。 失败的连接有时是无害的，但它们通常表示出现了问题。 例如，当故障转移环境突然不可访问，或者在云迁移后两个应用层彼此无法通信时，则连接可能会失败。

### <a name="client-groups"></a>客户端组
在映射中，客户端组表示要连接到已映射计算机的客户端计算机。 单个客户端组表示单个进程或计算机的客户端。

![映射中的客户端组](./media/vminsights-maps/map-group-client-groups-01.png)

若要查看客户端组中的受监控客户端和系统的 IP 地址，请选择该组。 下面显示了组的内容。  

![映射中客户端组的 IP 地址列表](./media/vminsights-maps/map-group-client-group-iplist-01.png)

如果组包含受监视和未受监视的客户端，则你可以选择该组的圆环图的相应部分来筛选客户端。

### <a name="server-port-groups"></a>服务器端口组
服务器端口组表示从已映射的计算机建立了入站连接的服务器上的端口。 该组包含服务器端口，以及连接到该端口的服务器计数。 选择该组可查看各个服务器和连接。 

![映射中的服务器端口组](./media/vminsights-maps/map-group-server-port-groups-01.png)  

如果组包含受监视和未受监视的服务器，则你可以选择该组的圆环图的相应部分来筛选服务器。

## <a name="view-a-map-from-a-vm"></a>从 VM 查看映射 

若要直接从 VM 访问用于 VM 的 Azure Monitor：

1. 在 Azure 门户中，选择“虚拟机”。 
2. 在列表中选择一个 VM。 在 "**监视**" 部分，选择 "**见解**"。  
3. 选择“映射”选项卡****。

映射将发现在指定时间范围内已建立有效网络连接的正在运行的进程组和进程，以此将 VM 的依赖项可视化。  

默认情况下，映射显示最近 30 分钟。 若要查看过去发现的依赖项，可以查询最多一小时的历史时间范围。 若要运行查询，请使用左上角的“TimeRange”选择器。**** 例如，在发生某个事件期间，或者想要查看更改前的状态时，可以运行查询。  

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>从虚拟机规模集查看映射

若要直接从虚拟机规模集访问用于 VM 的 Azure Monitor：

1. 在 Azure 门户中选择“虚拟机规模集”。
2. 在列表中选择一个 VM。 然后在 "**监视**" 部分选择 "**见解**"。  
3. 选择“映射”选项卡****。

映射会将规模集中的所有实例可视化为组节点，并可视化该组的依赖项。 展开的节点将列出规模集中的实例。 每次可以滚动浏览其中的 10 个实例。 

若要加载特定实例的映射，请先在映射中选择该实例。 选择右侧的**省略号**按钮 (...)，然后选择“加载服务器映射”。**** 在显示的映射中，可以看到在指定的时间范围内建立了有效网络连接的进程组和进程。 

默认情况下，映射显示最近 30 分钟。 若要查看过去发现的依赖项，可以查询最多一小时的历史时间范围。 若要运行查询，请使用“TimeRange”选择器。**** 例如，在发生某个事件期间，或者想要查看更改前的状态时，可以运行查询。

![直接 VM 映射概述](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>也可以从虚拟机规模集的“实例”视图访问特定实例的映射。**** 在 "**设置**" 部分中，请参阅**实例**  >  **见解**。

## <a name="view-a-map-from-azure-monitor"></a>从 Azure Monitor 查看映射

在 Azure Monitor 中，映射功能将提供 VM 及其依赖项的全局视图。 若要在 Azure Monitor 中访问映射功能：

1. 在 Azure 门户中选择“监视”。 
2. 在 "**见解**" 部分，选择 "**虚拟机**"。
3. 选择“映射”选项卡****。

   ![多个 VM 的 Azure Monitor 概述映射](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

使用页面顶部的“工作区”选择器选择一个工作区。**** 如果你有多个 Log Analytics 工作区，请选择已在解决方案中启用的，并且 VM 会向其报告的工作区。 

“组”选择器将返回与所选工作区相关的计算机的订阅、资源组、[计算机组](../../azure-monitor/platform/computer-groups.md)以及虚拟机规模集****。 所做的选择只会应用到映射功能，而不会传播到性能或运行状况功能。

默认情况下，映射显示最近 30 分钟。 若要查看过去发现的依赖项，可以查询最多一小时的历史时间范围。 若要运行查询，请使用“TimeRange”选择器。**** 例如，在发生某个事件期间，或者想要查看更改前的状态时，可以运行查询。  

## <a name="next-steps"></a>后续步骤

若要标识瓶颈、检查性能和了解 VM 的总体利用率，请参阅[查看用于 VM 的 Azure Monitor 的性能状态](vminsights-performance.md)。 
