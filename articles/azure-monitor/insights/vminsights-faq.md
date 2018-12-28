---
title: 用于 VM 的 Azure Monitor（预览版）常见问题解答 | Microsoft Docs
description: 用于 VM 的 Azure Monitor（预览版）是 Azure 中结合了 Azure VM 操作系统的运行状况和性能监视的解决方案。 它可自动发现应用程序组件以及与其他资源的依赖关系并映射它们之间的通信。 本文解答常见问题。
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
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184383"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>用于 VM 的 Azure Monitor（预览版）常见问题解答
本文解答有关用于 VM 的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问 [Azure 论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当问题经常被问到时，我们会将它们添加到本文中，以便可以轻松快捷地找到它们。

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>是否可以将 VM 部署到现有工作区？
如果虚拟机已连接到 Log Analytics 工作区，则在将它们部署到用于 VM 的 Azure Monitor 时，可以继续使用该工作区。 工作区必须位于[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md#prerequisites)“先决条件”部分所列的一个受支持区域中。

在部署过程中，我们会为工作区配置性能计数器。 此操作会使向工作区报告数据的 VM 开始收集信息以便在用于 VM 的 Azure Monitor 中进行显示和分析。 因此，你会看到与所选工作区连接的所有 VM 发出的性能数据。 只会为指定进行部署的 VM 启用“运行状况”和“映射”功能。

有关启用的性能计数器的详细信息，请参阅[用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md)。

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>是否可以将 VM 部署到新工作区？ 
如果 VM 当前未连接到现有的 Log Analytics 工作区，则你需要创建一个新工作区来存储数据。 可以通过在 Azure 门户中为用于 VM 的 Azure Monitor 配置单个 VM，来自动创建一个工作区。

如果选择使用基于脚本的方法，请参阅[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md)。 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果 VM 已开始向现有工作区报告，我可以怎么做？
如果你已开始从虚拟机收集数据，则可能已将虚拟机配置为向现有 Log Analytics 工作区报告数据。 只要该工作区在某个受支持区域中，就可以在该预先存在的工作区中启用用于 VM 的 Azure Monitor。 我们将力求支持更多的区域。

>[!NOTE]
>对于影响到向工作区报告的 VM 的工作区，我们将配置性能计数器，不管你是否选择将这些 VM 部署到用于 VM 的 Azure Monitor。 有关如何为工作区配置性能计数器的详细信息，请参阅 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)的“配置性能计数器”部分。 有关为用于 VM 的 Azure Monitor 配置的计数器的信息，请参阅[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md)。 

## <a name="why-did-my-vm-deployment-fail"></a>为何我的 VM 部署会失败？
从 Azure 门户部署 Azure VM 时，会发生以下事件：

* 创建默认的 Log Analytics 工作区（如果已选择该选项）。
* 为选定工作区配置性能计数器。 如果此步骤失败，则某些性能图表和表不会显示所部署 VM 的数据。 可以通过运行在[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md#enable-with-powershell)的“使用 PowerShell 进行启用”部分中介绍的 PowerShell 脚本来解决此问题。
* 使用 VM 扩展在 Azure VM 上安装 Log Analytics 代理（如果需要）。 
* 使用扩展在 Azure VM 上安装用于 VM 的 Azure Monitor 映射依赖项代理（如果需要）。 
* 根据需要配置支持“运行状况”功能的 Azure Monitor 组件，并将 VM 配置为报告运行状况数据。

在部署过程中，我们会检查前面每个步骤的状态，并在门户中向你返回通知状态。 配置工作区和安装代理通常需要 5 到 10 分钟。 在 Azure 门户中查看监视和运行状况数据需要额外的 5 到 10 分钟时间。 

如果在启动部署后有消息指出 VM 需要部署，则 VM 最长可以花费 30 分钟来完成该过程。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我的 VM 性能图表中未显示某些数据，或未显示任何数据
如果在磁盘表或某些性能图表中未显示性能数据，则可能未在工作区中配置性能计数器。 若要解决此问题，请运行在[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md#enable-with-powershell)的“使用 PowerShell 进行启用”部分中介绍的 PowerShell 脚本。

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>用于 VM 的 Azure Monitor 映射功能与服务映射有何不同？
用于 VM 的 Azure Monitor 映射功能基于服务映射，但存在以下差异：

* 可以从“VM”窗格，或者 Azure Monitor 下的“用于 VM 的 Azure Monitor”访问“映射”视图。
* “映射”中的连接现在可单击，并在侧面板中显示连接指标数据。
* 可使用一个新 API 创建映射，以便更好地支持更复杂的映射。
* 受监视 VM 现在处于客户端组节点中，圆环图显示受监视与不受监视虚拟机的比例。 展开组后，还可以筛选计算机列表。
* 受监视虚拟机现在处于服务器端口组节点中，圆环图显示受监视与不受监视计算机的比例。 展开组后，还可以筛选计算机列表。
* 映射样式已更新，与 Azure Application Insights 中的应用程序映射更一致。
* 侧面板已更新，但尚不具有服务映射中支持的整套集成：更新管理、更改跟踪、安全性和服务台。 
* 用于选择要映射的组和计算机的选项已更新。 它现在支持订阅、资源组、Azure 虚拟机规模集和云服务。
* 无法在用于 VM 的 Azure Monitor 映射功能中创建新的服务映射计算机组。 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>为何我的性能图表显示虚线？

性能图表显示虚线而不是实线的几个原因包括：
* 数据集合中可能存在间隙。 

* 数据采样的默认设置是每 60 秒一次。 如果为图表选择的时间范围较窄，并且采样频率小于图表中使用的 Bucket 大小，则可能会显示虚线。 假设选择的采样频率为 10 分钟，而图表上的每个 Bucket 为 5 分钟。 在这种情况下，选择查看更宽的时间范围应该可使图表线条显示为实线而不是虚线。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 是否支持组？
是，安装依赖项代理之后，我们会从 VM 中收集信息，以根据订阅、资源组、虚拟机规模集和云服务显示组。 如果你一直在使用服务映射并创建了计算机组，则也会显示这些组。 如果你为正在查看的工作区创建了计算机组，那么这些组也会显示在组筛选器中。 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何显示有关在聚合性能图表中驱动第 95 百分位数线的因素的详细信息？
默认情况下，列表会进行排序以显示对于所选指标具有最高第 95 百分位数值的 VM。 一个例外情况是“可用内存”图表，其中显示具有最低第五百分位数值的计算机。 选择图表可打开“前 N 项列表”视图，其中已选择相应的指标。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>“映射”功能如何跨不同虚拟网络和子网处理重复 IP？
如果跨子网和虚拟网络使用 VM 或 Azure 虚拟机规模集复制 IP 范围，则可能导致用于 VM 的 Azure Monitor“映射”功能显示错误的信息。 我们已意识到此问题，正在研究可改进体验的选项。

## <a name="does-the-map-feature-support-ipv6"></a>“映射”功能是否支持 IPv6？
“映射”功能目前仅支持 IPv4，我们正在研究 IPv6 的支持。 我们还支持 IPv6 隧道内部的 IPv4。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>加载资源组或其他大型组的映射时，为何难以查看映射？
尽管我们已改进了“映射”功能以便处理大型和复杂的配置，但我们认识到，映射中可能包含大量节点、连接以及以群集方式工作的节点。 我们将持续致力增强支持以提高可伸缩性。  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>为何“性能”选项卡上的网络图表不同于 Azure VM“概述”页上的网络图表？

Azure VM 的“概述”页基于来宾 VM 中的活动的主机度量值显示图表。 Azure VM“概述”中的网络图表仅显示计费的网络流量。 此显示不包括虚拟网络之间的流量。 为用于 VM 的 Azure Monitor 显示的数据和图表基于来宾 VM 中的数据，网络图表显示入站和出站到该 VM 的所有 TCP/IP 流量，包括虚拟网络之间的流量。

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Log Analytics 免费定价计划有何限制？
如果已经为 Azure Monitor 配置了使用*免费*定价层的 Log Analytics 工作区，则用于 VM 的 Azure Monitor 的“映射”功能仅支持将五台计算机连接到工作区。 

例如，假设有五个 VM 连接到免费工作区。 如果断开连接一个 VM，然后连接一个新 VM，则新 VM 不会受到监视并反映在“映射”页上。 在这种情形中，当打开新 VM 时，系统会提示使用“立即尝试”选项并从左窗格中选择“见解(预览版)”，即使是在 VM 中安装了它之后。 但是，系统不会使用在 VM 未部署到用于 VM 的 Azure Monitor 时通常所有的选项来提示你。 

## <a name="next-steps"></a>后续步骤
若要了解有关启用虚拟机监视的要求和方法，请参阅[部署用于 VM 的 Azure Monitor（预览版）](vminsights-onboard.md)。
