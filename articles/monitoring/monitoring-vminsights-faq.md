---
title: 用于 VM 的 Azure Monitor 常见问题解答 | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文将解答一些常见问题。
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
ms.date: 09/19/2018
ms.author: magoedte
ms.openlocfilehash: 308a447ff99cd11ad6a28df0bdb515764b0f546b
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063449"
---
# <a name="azure-monitor-for-vms-frequently-asked-questions"></a>用于 VM 的 Azure Monitor 常见问题解答
本 Microsoft 常见问题解答列出了 Microsoft Azure 中用于 VM 的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="can-i-onboard-to-an-existing-workspace"></a>是否可以载入到现有工作区？
如果虚拟机已连接到 Log Analytics 工作区，则在载入到用于 VM 的 Azure Monitor 时，可以继续使用该工作区，前提是该工作区位于[此处](monitoring-vminsights-onboard.md#prerequisites)所列的某个受支持区域中。

载入时，我们将配置该工作区的性能计数器，使得向该工作区报告数据的所有 VM 都会开始收集此信息，以便在用于 VM 的 Azure Monitor 中进行显示和分析。  因此，你会看到与所选工作区连接的所有 VM 发出的性能数据。  只会为指定载入的 VM 启用“运行状况”和“映射”功能。

有关要启用哪些性能计数器的详细信息，请参阅[载入](monitoring-vminsights-onboard.md)一文。

## <a name="can-i-onboard-to-a-new-workspace"></a>是否可以载入到新工作区？ 
如果 VM 当前未连接到现有的 Log Analytics 工作区，则你需要创建一个新工作区来存储数据。  如果通过 Azure 门户为用于 VM 的 Azure Monitor 配置了单个 Azure VM，则系统会自动创建新的默认工作区。

如果选择使用基于脚本的方法，请参阅[载入](monitoring-vminsights-onboard.md)一文中所述的步骤。 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果 VM 已开始向现有工作区报告，我该怎么做？
如果你已开始从虚拟机收集数据，则可能已将虚拟机配置为向现有的 Log Analytics 工作区报告数据。  只要该工作区在某个受支持区域中，就可以在该现有工作区中启用用于 VM 的 Azure Monitor。  如果使用的工作区不在受支持的区域中，则目前无法载入到用于 VM 的 Azure Monitor。  我们将力求支持更多的区域。

>[!NOTE]
>对于影响到向工作区报告的 VM 的工作区，我们将配置性能计数器，不管你是否选择将这些 VM 载入到用于 VM 的 Azure Monitor。 有关如何为工作区配置性能计数器的详细信息，请参阅我们的[文档](../log-analytics/log-analytics-data-sources-performance-counters.md)。 有关为用于 VM 的 Azure Monitor 配置的计数器的信息，请参阅[载入文档](monitoring-vminsights-onboard.md#performance-counters-enabled)。  

## <a name="why-did-my-vm-fail-to-onboard"></a>为何我的 VM 无法载入？
从 Azure 门户载入 Azure VM 时，将执行以下步骤：

* 创建默认的 Log Analytics 工作区（如果已选择该选项）。
* 为选定的工作区配置性能计数器。 如果此步骤失败，你会发现，某些性能图表和表不会显示所载入 VM 的数据。 可以运行[此处](monitoring-vminsights-onboard.md#enable-with-powershell)所述的 PowerShell 脚本来解决此问题。
* 使用 VM 扩展在 Azure VM 上安装 Log Analytics 代理（如果确定有必要）。  
* 使用某个扩展在 Azure VM 上安装用于 VM 的 Azure Monitor 映射依赖项代理（如果确定有必要）。  
* 根据需要配置支持“运行状况”功能的 Azure Monitor 组件，并将 VM 配置为报告运行状况数据。

在载入过程中，我们会检查上述每个步骤的状态，并在门户中返回通知状态。  配置工作区和安装代理通常需要 5 到 10 分钟。  在门户中查看监视和运行状况数据需要额外的 5 到 10 分钟时间。  

如果在启动载入后有消息指出 VM 需要载入，则 VM 最长需要花费 30 分钟来完成该过程。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我的 VM 性能图表中未显示某些数据，或未显示任何数据
如果在磁盘表或某些性能图表中未看到性能数据，则原因可能是未在工作区中配置性能计数器。 若要解决此问题，请运行以下 [PowerShell 脚本](monitoring-vminsights-onboard.md#enable-with-powershell)。

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>用于 VM 的 Azure Monitor 映射功能与服务映射有何不同？
用于 VM 的 Azure Monitor 映射功能基于服务映射，但存在以下差异：

* 可以从“VM”边栏选项卡，或者 Azure Monitor 中的“用于 VM 的 Azure Monitor”访问“映射”视图。
* “映射”中的连接现在可单击，所选连接的边侧面板中会显示连接指标数据的视图。
* 可以使用一个新 API 创建映射，以便更好地支持更复杂的映射。
* 受监视 VM 现在包含在客户端组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 受监视虚拟机现在包含在服务器端口组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 映射样式已更新，与 Application Insights 中的应用映射更一致。
* 边侧面板已更新，但尚不包含服务映射中支持的整套集成功能 -“更新管理”、“更改跟踪”、“安全性”和“服务台”。 
* 用于选择要映射的组和计算机的选项已更新，现在支持订阅、资源组、Azure 虚拟机规模集和云服务。
* 无法在用于 VM 的 Azure Monitor 映射功能中创建新的服务映射计算机组。  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>为何我的性能图表显示虚线？

这有几个原因。  如果数据集合中存在间隙，则我们会绘制虚线。  如果为启用的性能计数器修改了数据采样频率（默认设置为每隔 60 秒收集数据），并且为图表选择较窄的时间范围，而采样频率小于图表中使用的桶大小（例如，采样频率为每隔 10 分钟，图表上的每个桶为 5 分钟），则可能会在图表中看到虚线。  在这种情况下，选择查看更宽的时间范围应该可使图表线条显示为实线而不是虚线。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 是否支持组？
“性能”功能支持基于特定工作区中突出显示的资源的组，以及基于特定 Azure 虚拟机规模集和云服务的组。

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何查看聚合性能图表中驱动第 95 百分位线条的详细信息？
默认情况下，列表已经过排序，会显示所选指标的第 95 百分位值最大的 VM，但“可用内存”图表除外，其中显示第 5 百分位值最小的计算机。  单击图表会打开“前 N 项列表”视图，其中已选择相应的指标。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>“映射”功能如何处理不同 VNet 和子网中的重复 IP？
如果跨子网和 VNet 复制 VM 或 Azure 虚拟机规模集的 IP 范围，可能导致用于 VM 的 Azure Monitor 映射显示错误的信息。 这是一个已知问题，我们正在研究可改进此体验的选项。

## <a name="does-map-feature-support-ipv6"></a>“映射”功能是否支持 IPv6？
“映射”功能目前仅支持 IPv4，我们正在研究 IPv6 的支持。 我们还支持 IPv6 隧道内部的 IPv4。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>加载资源组或其他大型组的映射时，难以查看映射
尽管我们已对“映射”做出改进，使其能够处理大型和复杂的配置，但我们认识到，映射中可能包含大量以群集方式工作的节点和连接。  我们将持续致力增强支持以提高可伸缩性。   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>为何“性能”选项卡上的网络图表不同于 Azure VM“概述”页上的网络图表？

Azure VM 的概述页基于来宾 VM 中的活动的主机度量值显示图表。  Azure VM“概述”中的网络图表仅显示计费的网络流量。  它不包括 VNet 间的流量。  为用于 VM 的 Azure Monitor 显示的数据和图表基于来宾 VM 中的数据，网络图表显示入站和出站到该 VM 的所有 TCP/IP 流量，包括 VNet 间的流量。

## <a name="next-steps"></a>后续步骤
查看[载入用于 VM 的 Azure Monitor](monitoring-vminsights-onboard.md)，以了解启用虚拟机监视的要求和方法。
