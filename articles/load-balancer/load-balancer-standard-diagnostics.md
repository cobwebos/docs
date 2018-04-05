---
title: Azure 标准负载均衡器 - 诊断 | Microsoft Docs
description: 使用可用的指标和运行状况信息进行 Azure 标准负载均衡器诊断
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 1d39cdc13e69740dc99e67f935b60db218536044
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>标准负载均衡器的指标和运行状况诊断

Azure 标准负载均衡器针对资源提供以下诊断功能：
* 多维指标：标准负载均衡器为公共和内部负载均衡器配置提供新的多维诊断功能。 因此我们便可以监视、管理负载均衡器资源以及排查其问题。

* 资源运行状况：Azure 门户中的“负载均衡器”页和 Monitor 中的“资源运行状况”页会显示标准负载均衡器的公共负载均衡器配置的资源运行状况。

本文将会概要介绍这些功能，以及如何对标准负载均衡器使用这些功能。

## <a name = "MultiDimensionalMetrics"></a>多维指标

Azure 负载均衡器通过门户中的新“Azure 指标(预览版)”来提供新的多维指标，帮助你实时洞察负载均衡器资源的诊断信息。 

以下指标适用于当前的不同标准负载均衡器 (LB) 配置：

| 指标 | 资源类型 | 说明 | 建议的聚合 |
| --- | --- | --- | --- |
| VIP 可用性（数据路径可用性） | 公共 LB | 标准负载均衡器持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。| 平均值 |
| DIP 可用性（运行状况探测状态） |  公共和内部 LB | 标准负载均衡器使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。  可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。 |  平均值 |
| SYN 数据包 |  公共 LB | 标准负载均衡器不会终止 TCP 连接，也不会与 TCP 或 UDP 数据包流交互。 流及其握手始终位于源和 VM 实例之间。 若要更好地排查 TCP 协议方案的问题，可以使用 SYN 数据包计数器了解进行了多少次 TCP 连接尝试。 该指标将报告接收到的 TCP SYN 数据包数目。| 平均值 |
| SNAT 连接 |  公共 LB |标准负载均衡器报告公共 IP 地址前端上伪装的出站流数。 SNAT 端口是可耗竭性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。  将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。| 平均值 |
| 字节计数器 |  公共和内部 LB | 标准负载均衡器按前端报告处理的数据。| 平均值 |
| 数据包计数器 |  公共和内部 LB | 标准负载均衡器按前端报告处理的数据包。| 平均值 |

### <a name="view-load-balancer-metrics-in-the-portal"></a>在门户中查看负载均衡器指标

Azure 门户通过“指标(预览版)”页公开负载均衡器指标，可在特定负载均衡器资源的“负载均衡器资源”页中访问该页；另外，“Azure Monitor”页中也会提供这些指标。 

若要查看标准负载均衡器资源的指标，请通过这些位置之一浏览到“指标(预览版)”，从下拉列表中选择“指标类型”（在“Monitor”页中请选择负载均衡器资源），设置相应的“聚合类型”，并选择性地配置所需的筛选和分组，然后，可以查看符合指定条件的指标的历史视图。

![标准负载均衡器的指标预览版](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*图 - 负载均衡器的 DIP 可用性/运行状况探测状态指标*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>通过 API 以编程方式检索多维指标

[监视 REST API 演练 > 多维 API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api) 中提供了有关检索多维指标定义和值的 API 指导

### <a name = "DiagnosticScenarios"></a>常见诊断场景和建议的视图

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>数据路径是否已启动并适用于我的负载均衡器 VIP？

VIP 可用性指标描述区域中用于计算 VM 所在主机的数据路径的运行状况。 它反映了 Azure 基础结构的运行状况。 使用此指标可以：
- 监视服务的外部可用性
- 深入分析和了解部署服务的平台是否正常，或者来宾 OS 或应用程序实例是否正常
- 查明某个事件是与服务还是底层数据平面相关。 请不要将此指标与运行状况探测状态（“DIP 可用性”）相混淆。

获取标准负载均衡器资源的 VIP 可用性：
- 确保选择正确的负载均衡器资源。 
- 从“指标”下拉列表中选择“VIP 可用性”。 
- 为“聚合”选择“平均值”。 
- 此外，将基于 VIP 地址或 VIP 端口的筛选器添加为维度，并添加所需的前端 IP 地址或前端端口，然后根据选定的维度分组。

![VIP 探测](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*图 - 负载均衡器 VIP 探测详细信息*

将会根据活动的带内度量值生成该指标。 区域中的探测服务根据此测量值发起流量，使用公共前端创建部署后，此服务会立即激活，并一直运行到删除了前端为止。 

>[!NOTE]
>目前不支持内部前端。 

会定期生成与部署前端和规则匹配的数据包。 该服务在区域中从源遍历到后端池中 VM 所在的主机。 与针对其他所有流量一样，负载均衡器基础结构执行相同的负载均衡和转换运算。 此探测在负载均衡终结点上的带内执行。 探测抵达后端池中 VM 所在的计算主机后，计算主机将向探测服务生成响应。 VM 看不到此流量。
VIP 可用性探测会出于原因而失败：
- 后端池中没有剩余的可用于部署的正常 VM。 
- 发生基础结构服务中断，导致 VIP 可用性探测失败。

可以[结合使用 VIP 可用性指标和运行状况探测状态进行诊断](https://aka.ms/lbdiagnostics#vipavailabilityandhealthprobes)。

在大多数情况下，可以使用“平均值”作为聚合。

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>VIP 的后端实例是否正在响应探测？

运行状况探测状态指标描述在配置负载均衡器的运行状况探测时，由你配置的应用程序部署的运行状况。 负载均衡器使用运行状况探测的状态来确定要将新流量发送到何处。 运行状况探测源自某个 Azure 基础结构地址，并会显示在 VM 的来宾 OS 中。

获取标准负载均衡器资源的 DIP 可用性：
- 选择“DIP 可用性”作为指标，选择“平均值”作为聚合类型。 
- 应用基于所需 VIP IP 地址和/或端口的筛选器。

![DIP 可用性](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*图 - 负载均衡器的 VIP 可用性*

运行状况探测会出于以下原因而失败：
- 针对不在侦听、无响应或者使用错误协议做出响应的端口配置运行状况探测。 服务使用 DSR（浮动 IP）规则，而你需要确保服务侦听 NIC IP 配置的 IP 地址，而不仅仅是侦听使用前端 IP 地址配置的环回地址。
- 网络安全组、VM 的来宾 OS 防火墙或应用层筛选器不允许你的探测。

在大多数情况下，可以使用“平均值”作为聚合。

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>如何检查出站连接统计信息？ 

“SNAT 连接”指标描述成功和失败连接的数量（适用于[出站流](https://aka.ms/lboutbound)）。

如果失败连接数量大于零，则表示 SNAT 端口已耗尽。 必须进一步调查，并确定失败的可能原因。 SNAT 端口耗尽的表现形式是无法建立[出站流](https://aka.ms/lboutbound)。 请查看有关出站连接的文章，以了解相关的场景和运行机制，以及如何缓解/尽量避免 SNAT 端口耗尽。 

获取 SNAT 连接统计信息：
- 选择“SNAT 连接”作为指标类型，并选择“总和”作为聚合。 
- 根据不同行中显示的成功和失败 SNAT 连接计数的“连接状态”进行分组。 

![SNAT 连接](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*图 - 负载均衡器的 SNAT 连接计数*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>如何检查服务的入站/出站连接尝试？

“SYN 数据包”指标描述收到或发送的、与给定前端关联的 TCP SYN 数据包数量（适用于[出站流](https://aka.ms/lboutbound)）。 使用此指标可以了解对服务发起的 TCP 连接尝试。
在大多数情况下，可以使用“总计”作为聚合。

![SYN 连接](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*图 - 负载均衡器的 SYN 计数*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>如何检查网络带宽消耗？ 

“字节/数据包计数器”指标描述服务发送或收到的字节和数据包数量，根据前端显示信息。
在大多数情况下，可以使用“总计”作为聚合。

获取字节或数据包计数统计信息
- 选择“字节计数”和/或“数据包计数”作为指标类型，并选择“平均值”作为聚合。 
- 应用基于特定前端 IP、相关前端端口或后端 IP 或者端口的筛选器。 
- 或者不使用任何筛选器获取负载均衡器资源的总体统计信息。


使用不同配置的指标的部分示例视图

![字节计数](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*图 - 负载均衡器的字节计数*

#### <a name = "vipavailabilityandhealthprobes"></a>如何诊断负载均衡器部署？

在单个图表中结合使用“VIP 可用性”和“运行状况探测”指标可以识别查找和解决问题的位置。 可以参考这些信息来确认 Azure 是否正常工作，并最终确定配置或应用程序是否为问题的根本原因。

可以使用运行状况探测指标来了解 Azure 如何根据提供的配置查看部署的运行状况。 在监视或确定原因时，查看运行状况探测始终是合理的第一个动作。

然后可以采取进一步的措施，并使用 VIP 可用性指标来深入了解 Azure 如何查看负责特定部署的底层数据平面的运行状况。 结合使用两个指标，可以查明错误的所在位置，如以下示例所示：

![VIP 诊断](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*图 - 结合使用 DIP 和 VIP 可用性指标*

图表中显示了以下信息：
- 基础结构本身正常，托管 VM 的基础结构可访问，并且已将多个 VM 置于后端。 VIP 可用性的蓝色轨迹（显示 100%）指示了此状态。 
- 但是，根据橙色轨迹所示，图表开头的运行状况探测状态（DIP 可用性）为 0%。 红色圆圈突出显示了运行状况探测（DIP 可用性）变为正常的位置，以及客户部署可以接受新流量的位置。

客户可以使用该图表来自行排查部署问题，而无需猜测是否发生了其他问题或请求支持。 客户的服务之所以不可用，是因为配置不当或应用程序故障导致运行状况探测失败。

### <a name = "Limitations"></a>限制

- VIP 可用性目前仅适用于公共前端。

## <a name = "ResourceHealth"></a>资源运行状况

可以通过“Monitor”>“服务运行状况”下面的现有“资源运行状况”公开标准负载均衡器资源的运行状况。

>[!NOTE]
>负载均衡器的资源运行状况目前仅适用于标准负载均衡器的“公共”配置。 内部负载均衡器资源或者负载均衡器资源的基本 SKU 不会公开资源运行状况。

查看公共标准负载均衡器资源的运行状况：
 - 浏览到“Monitor”>“服务运行状况”。

  ![“Monitor”页](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *图 - Azure Monitor 中的服务运行状况*

 - 选择“资源运行状况”，并确保正确选择“订阅 ID”和“资源类型 = 负载均衡器”。

  ![资源运行状况](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *图 - 选择要查看其运行状况的资源*

 - 在列表中单击要查看其历史运行状况的负载均衡器资源。

    ![负载均衡器运行状况](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *图 - 负载均衡器资源运行状况视图*
 
下表列出了各种资源运行状况及其说明。 

| 资源运行状况 | 说明 |
| --- | --- |
| 可用 | 公共标准负载均衡器资源正常且可用 |
| 不可用 | 公共标准负载均衡器资源不正常。 通过“Azure Monitor”>“指标”诊断运行状况。 （这可能还意味着资源不是公共标准负载均衡器） |
| Unknown | 尚未更新公共标准负载均衡器的资源运行状况。 （这可能还意味着资源不是公共标准负载均衡器）  |

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](load-balancer-standard-overview.md)
- 详细了解[负载均衡器出站连接](https://aka.ms/lboutbound)


