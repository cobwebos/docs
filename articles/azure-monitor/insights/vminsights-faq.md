---
title: 用于 VM 的 Azure Monitor（预览版）常见问题解答 | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文将解答一些常见问题。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 675277a33a5613507297f1c77ee9cef3215b22a2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555209"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>用于 VM 的 Azure Monitor（预览版）常见问题解答
本 Microsoft 常见问题解答列出了用于 VM 的 Azure Monitor 的常见问题。 如果对该解决方案还有其他任何问题，请访问[论坛](https://feedback.azure.com/forums/34192--general-feedback)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="can-i-onboard-to-an-existing-workspace"></a>是否可以载入到现有工作区？
如果虚拟机已连接到 Log Analytics 工作区，则在载入到用于 VM 的 Azure Monitor 时，可以继续使用该工作区，前提是该工作区位于[此处](vminsights-enable-overview.md#prerequisites)所列的某个受支持区域中。

载入时，我们将配置该工作区的性能计数器，使得向该工作区报告数据的所有 VM 都会开始收集此信息，以便在用于 VM 的 Azure Monitor 中进行显示和分析。  因此，你会看到与所选工作区连接的所有 VM 发出的性能数据。  只会为指定载入的 VM 启用“运行状况”和“映射”功能。

有关启用了哪些性能计数器的详细信息，请参阅我们的[启用概述](vminsights-enable-overview.md#performance-counters-enabled)一文。

## <a name="can-i-onboard-to-a-new-workspace"></a>是否可以载入到新工作区？ 
如果 VM 当前未连接到现有的 Log Analytics 工作区，则你需要创建一个新工作区来存储数据。 如果通过 Azure 门户为用于 VM 的 Azure Monitor 配置了单个 Azure VM，则系统会自动创建新的默认工作区。

如果选择使用基于脚本的方法，则[使用 Azure PowerShell 或资源管理器模板文章启用用于 VM 的 Azure Monitor （预览版）](vminsights-enable-at-scale-powershell.md)中介绍了这些步骤。 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果 VM 已开始向现有工作区报告，我该怎么做？
如果你已开始从虚拟机收集数据，则可能已将虚拟机配置为向现有的 Log Analytics 工作区报告数据。  只要该工作区在某个受支持区域中，就可以在该现有工作区中启用用于 VM 的 Azure Monitor。  如果使用的工作区不在受支持的区域中，则目前无法载入到用于 VM 的 Azure Monitor。  我们将力求支持更多的区域。

>[!NOTE]
>对于影响到向工作区报告的 VM 的工作区，我们将配置性能计数器，不管你是否选择将这些 VM 载入到用于 VM 的 Azure Monitor。 有关如何为工作区配置性能计数器的详细信息，请参阅我们的[文档](../../azure-monitor/platform/data-sources-performance-counters.md)。 有关为用于 VM 的 Azure Monitor 配置的计数器的信息，请参阅我们的[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md#performance-counters-enabled)一文。  

## <a name="why-did-my-vm-fail-to-onboard"></a>为何我的 VM 无法载入？
从 Azure 门户载入 Azure VM 时，将执行以下步骤：

* 创建默认的 Log Analytics 工作区（如果已选择该选项）。
* 为选定的工作区配置性能计数器。 如果此步骤失败，你会发现，某些性能图表和表不会显示所载入 VM 的数据。 可以运行[此处](vminsights-enable-at-scale-powershell.md#enable-performance-counters)所述的 PowerShell 脚本来解决此问题。
* 使用 VM 扩展在 Azure VM 上安装 Log Analytics 代理（如果确定有必要）。  
* 使用某个扩展在 Azure VM 上安装用于 VM 的 Azure Monitor 映射依赖项代理（如果确定有必要）。  
* 根据需要配置支持“运行状况”功能的 Azure Monitor 组件，并将 VM 配置为报告运行状况数据。

在载入过程中，我们会检查上述每个步骤的状态，并在门户中返回通知状态。 配置工作区和安装代理通常需要 5 到 10 分钟。 在门户中查看监视和运行状况数据需要额外的 5 到 10 分钟时间。  

如果在启动载入后有消息指出 VM 需要载入，则 VM 最长需要花费 30 分钟来完成该过程。 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>我只启用了用于 VM 的 Azure Monitor，为什么发现所有 VM 都受运行状况功能监视？
即使只对单个 VM 启动了该操作，也会为连接到 Log Analytics 工作区的所有 VM 启用该功能。

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>能否修改运行状况条件执行状况评估的时间计划？
不能，在此版本中，无法修改运行状况条件执行任务的时间段和频率。 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>能否对不需要监视的状况禁用运行状况条件？
此版本中无法禁用运行状况条件。

## <a name="are-the-health-alert-severities-configurable"></a>能否配置运行状况警报严重级别？  
运行状况警报严重性不能修改，只能启用或禁用。 另外，某些警报严重级别会根据运行状况条件的状态进行更新。 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>如果对特定运行状况条件的设置进行了重新配置，此配置能否作用于特定实例？  
如果修改某个运行状况条件实例的任何设置，则会修改 Azure VM 上相同类型的所有运行状况条件实例。 例如，如果修改对应于逻辑磁盘 C: 的磁盘可用空间运行状况条件实例的阈值，则该阈值会应用到在同一 VM 中发现和监视的其他所有逻辑磁盘。

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>运行状况功能是否会监视逻辑处理器和核心？
不会，Windows 不包含单个处理器和逻辑处理器级别运行状况条件，仅默认监视 CPU 的总使用率，以根据 Azure VM 可用的逻辑 CPU 总数评估 CPU 压力。 

## <a name="are-all-health-criteria-thresholds-configurable"></a>是否所有运行状况条件阈值均可配置？  
针对 Windows VM 的运行状况条件的阈值不可修改，因为其运行状况状态已设置为“正在运行”或“可用”。 从[工作负荷监视器 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) 查询运行状态时，如果出现以下情况，则会服务或实例的 **LessThan** 或 **GreaterThan** 的 *comparisonOperator* 值，以及阈值 **4**：
   - DNS 客户端服务运行状况 - 服务未运行。 
   - DHCP 客户端服务运行状况 - 服务未运行。 
   - RPC 服务运行状况 - 服务未运行。 
   - Windows 防火墙服务运行状况 - 服务未运行。
   - Windows 事件日志服务运行状况 - 服务未运行。 
   - 服务器服务运行状况 - 服务未运行。 
   - Windows 远程管理服务运行状况 - 服务未运行。 
   - 文件系统错误或损坏 - 逻辑磁盘不可用。

以下 Linux 运行状况条件的阈值不可修改，因为其运行状态已设置为 *true*。 从实体的工作负荷监视 API 查询时，运行状态会显示带有值 **LessThan** 的 *comparisonOperator* 以及阈值 **1**，具体取决于上下文：
   - 逻辑磁盘状态 - 逻辑磁盘未联机/不可用
   - 磁盘状态 - 磁盘未联机/不可用
   - 网络适配器状态 - 网络适配器已禁用

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>如何修改运行状况功能附带的警报？
为每个运行状况条件定义的警报规则不会在 Azure 门户中显示。 只能在[工作负荷监视器 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) 中启用或禁用运行状况警报规则。 此外，也无法在 Azure 门户中为运行状况警报分配 [Azure Monitor 操作组](../../azure-monitor/platform/action-groups.md)。 只能使用通知设置 API 来配置在任何时候触发运行状况警报时要触发的操作组。 目前，可以针对 VM 分配操作组，以便针对 VM 激发的所有运行状况警报都会触发相同的操作组。 如传统的 Azure 警报不同，每个运行状况警报规则没有单独的操作组概念。 此外，触发运行状况警报时，仅支持配置为提供电子邮件或短信通知的操作组。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我的 VM 性能图表中未显示某些数据，或未显示任何数据
如果在磁盘表或某些性能图表中未看到性能数据，则原因可能是未在工作区中配置性能计数器。 若要解决此问题，请运行以下 [PowerShell 脚本](vminsights-enable-at-scale-powershell.md#enable-with-powershell)。

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>用于 VM 的 Azure Monitor 映射功能与服务映射有何不同？
用于 VM 的 Azure Monitor 映射功能基于服务映射，但存在以下差异：

* 可以从“VM”边栏选项卡，或者 Azure Monitor 中的“用于 VM 的 Azure Monitor”访问“映射”视图。
* “映射”中的连接现在可单击，所选连接的边侧面板中会显示连接指标数据的视图。
* 可以使用一个新 API 创建映射，以便更好地支持更复杂的映射。
* 受监视 VM 现在包含在客户端组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 受监视虚拟机现在包含在服务器端口组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 映射样式已更新，与 Application Insights 中的应用映射更一致。
* 已更新侧面板，但没有服务映射-更新管理、更改跟踪、安全和服务台支持的一组完整集成。 
* 用于选择要映射的组和计算机的选项已更新，现在支持订阅、资源组、Azure 虚拟机规模集和云服务。
* 无法在用于 VM 的 Azure Monitor 映射功能中创建新的服务映射计算机组。  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>为何我的性能图表显示虚线？
这有几个原因。  如果数据集合中存在间隙，则我们会绘制虚线。  如果为启用的性能计数器修改了数据采样频率（默认设置为每隔 60 秒收集数据），并且为图表选择较窄的时间范围，而采样频率小于图表中使用的桶大小（例如，采样频率为每隔 10 分钟，图表上的每个桶为 5 分钟），则可能会在图表中看到虚线。  在这种情况下，选择查看更宽的时间范围应该可使图表线条显示为实线而不是虚线。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 是否支持组？
是，一旦安装依赖项代理，我们就会从 VM 中收集信息，以根据订阅、资源组、虚拟机规模集和云服务显示组。  如果你一直在使用服务映射并创建了计算机组，则也会显示这些组。  如果你为正在查看的工作区创建了计算机组，那么这些组也会显示在组筛选器中。 

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

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>对于存储在 VMConnection 中并显示在连接面板和工作簿中的数据，响应时间是如何度量的？

响应时间为近似值。 由于我们不会检测应用程序的代码，因此，我们不会真正知道请求开始的时间和响应到达的时间。 相反，我们观察到正在连接上发送的数据，然后再返回该连接的数据。 代理跟踪这些发送和接收，并尝试对它们进行配对：发送顺序，后跟一系列接收，被解释为请求/响应对。 这些操作之间的时间间隔是响应时间。 它将包括网络延迟和服务器处理时间。

此近似适用于基于请求/响应的协议：在连接上发出单个请求，并收到单个响应。 这种情况适用于 HTTP （S）（不带管道），但对其他协议不满意。

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我使用的是 Log Analytics 免费定价计划，它们有何限制？
如果已经为 Azure Monitor 配置了使用*免费*定价层的 Log Analytics 工作区，则用于 VM 的 Azure Monitor 的映射功能将仅支持已连接到工作区的五台计算机。 如果已有五台 VM 连接到免费工作区，你断开了其中一台 VM 的连接，然后连接到一台新的 VM，则新的 VM 将不受监视并且不会反映在“映射”页上。  

在这种情况下，在你打开 VM 并从左侧窗格中选择“见解(预览版)”时，甚至在 VM 上安装它后，都将通过“立即尝试”选项向你发出提示。  但是，如果此 VM 未加入到用于 VM 的 Azure Monitor，则不会像通常情况下那样通过选项向你发出提示。 

## <a name="next-steps"></a>后续步骤
查看[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md)以了解支持监视虚拟机的要求和方法。
