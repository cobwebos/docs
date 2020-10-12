---
title: 通过指标、警报和资源运行状况进行诊断 - Azure 标准负载均衡器
description: 使用可用的指标、警报和资源运行状况信息对 Azure 标准负载均衡器进行诊断。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 97541a4f8d86b90bf6045fc2a9e5abbe86aee5cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88717330"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>通过指标、警报和资源运行状况进行标准负载均衡器诊断

Azure 标准负载均衡器公开了以下诊断功能：

* **多维指标和警报**：通过 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 针对标准负载均衡器配置提供多维诊断功能。 可以监视、管理和排查标准负载均衡器资源问题。

* **资源运行状况**：负载均衡器的资源运行状况状态显示在“监视器”下的“资源运行状况”页面中。 此自动检查会通知你负载均衡器资源当前的可用性。

本文概要介绍这些功能，以及如何对标准负载均衡器使用这些功能。 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>多维指标

Azure 负载均衡器通过 Azure 门户中的 Azure 指标来提供多维指标，帮助你获取负载均衡器资源的实时诊断见解。 

各种标准负载均衡器配置提供以下指标：

| 指标 | 资源类型 | 说明 | 建议的聚合 |
| --- | --- | --- | --- |
| 数据路径可用性 | 公共和内部负载均衡器 | 标准负载均衡器持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。| 平均值 |
| 运行状况探测状态 | 公共和内部负载均衡器 | 标准负载均衡器使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。 可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。 |  平均值 |
| SYN（同步）数据包 | 公共和内部负载均衡器 | 标准负载均衡器不会终止传输控制协议 (TCP) 连接，也不会与 TCP 或 UDP 数据包流交互。 流及其握手始终位于源和 VM 实例之间。 若要更好地排查 TCP 协议方案的问题，可以使用 SYN 数据包计数器了解进行了多少次 TCP 连接尝试。 该指标将报告接收到的 TCP SYN 数据包数目。| 平均值 |
| SNAT 连接 | 公共负载均衡器 |标准负载均衡器报告公共 IP 地址前端上伪装的出站流数。 源网络地址转换 (SNAT) 端口是消耗性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。 将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。| 平均值 |
| 已分配的 SNAT 端口数 | 公共负载均衡器 | 标准负载均衡器报告每个后端实例分配的 SNAT 端口数 | 平均值。 |
| 已用 SNAT 端口数 | 公共负载均衡器 | 标准负载均衡器报告每个后端实例使用的 SNAT 端口数。 | 平均值 | 
| 字节计数器 |  公共和内部负载均衡器 | 标准负载均衡器按前端报告处理的数据。 你可能会注意到，这些字节并没有均匀地分布在后端实例中。 这是正常的，因为 Azure 的负载均衡器算法基于流 | 平均值 |
| 数据包计数器 |  公共和内部负载均衡器 | 标准负载均衡器按前端报告处理的数据包。| 平均值 |

  >[!NOTE]
  >通过 NVA 或防火墙使用来自内部负载均衡器的分发流量时，Syn 数据包、字节计数器和数据包计数器指标不可用，并将显示为零。 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>在 Azure 门户中查看负载均衡器指标

Azure 门户通过“指标”页公开负载均衡器指标，可在特定资源的负载均衡器资源页以及 Azure Monitor 页中访问该页。 

若要查看标准负载均衡器资源的指标，请执行以下操作：
1. 转到“指标”页，执行以下操作之一：
   * 在负载均衡器资源页的下拉列表中选择指标类型。
   * 在 Azure Monitor 页中选择负载均衡器资源。
2. 设置适当的指标聚合类型。
3. （可选）配置需要的筛选和分组。
4. （可选）配置时间范围和聚合。 默认情况下，时间以 UTC 格式显示。

  >[!NOTE] 
  >解释某些指标时，时间聚合非常重要，因为数据每分钟采样一次。 如果时间聚合设置为五分钟，并且指标聚合类型“求和”用于“SNAT 分配”等指标，则图形将显示分配的 SNAT 端口总数的五倍。 

![标准负载均衡器的指标](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*图：* 标准负载均衡器的“数据路径可用性”指标

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>通过 API 以编程方式检索多维指标

有关如何检索多维指标定义和值的 API 指导，请参阅 [Azure 监视 REST API 演练](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)。 这些指标只能通过“所有指标”选项写入存储帐户。 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>配置针对多维指标的警报 ###

Azure 标准负载均衡器支持易于配置的针对多维指标的警报。 为特定指标配置自定义阈值，用以触发具有不同严重性级别的警报，从而提供无接触的资源监视体验。

配置警报：
1. 转到负载均衡器的“警报”子边栏选项卡
1. 创建新的警报规则
    1.  配置警报条件
    1.  （可选）添加用于自动修复的操作组
    1.  分配警报严重性、名称和说明，以实现直观的反应

  >[!NOTE]
  >警报条件配置窗口将显示信号历史记录的时序。 有一个选项可用于按维度（例如后端 IP）筛选此时序。 这将筛选时序图而不是警报本身。 不能为特定的后端 IP 地址配置警报。

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>常见诊断场景和建议的视图

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>数据路径是否正常可用并适用于我的负载均衡器前端？
<details><summary>展开</summary>

“数据路径可用性”指标描述区域中用于 VM 所在计算主机的数据路径的运行状况。 此指标反映了 Azure 基础结构的运行状况。 使用此指标可以：
- 监视服务的外部可用性
- 深入分析和了解部署服务的平台是否正常，或者来宾 OS 或应用程序实例是否正常。
- 查明某个事件是与服务还是底层数据平面相关。 请不要将此指标与运行状况探测状态（“后端实例可用性”）相混淆。

若要获取标准负载均衡器资源的“数据路径可用性”，请执行以下操作：
1. 确保选择正确的负载均衡器资源。 
2. 在“指标”下拉列表中选择“数据路径可用性”。   
3. 在“聚合”  下拉列表中，选择“平均”  。 
4. 另外，请将基于前端 IP 地址或前端端口的筛选器添加为维度，并添加所需的前端 IP 地址或前端端口，然后根据选定的维度将其分组。

![VIP 探测](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*图：* 负载均衡器前端探测详细信息

将会根据活动的带内度量值生成该指标。 区域中的探测服务根据此测量值发起流量， 使用公共前端创建部署后，此服务会立即激活，并一直运行到删除了前端为止。 

会定期生成与部署前端和规则匹配的数据包。 该服务在区域中从源遍历到后端池中 VM 所在的主机。 负载均衡器基础结构执行的负载均衡和转换运算与针对其他所有流量执行的操作一样。 此探测在负载均衡终结点上的带内执行。 探测抵达后端池中正常 VM 所在的计算主机后，计算主机会针对探测服务生成响应。 VM 看不到此流量。

数据路径可用性探测会由于以下原因而失败：
- 后端池中没有剩余的可用于部署的正常 VM。 
- 发生基础结构服务中断。

可以[结合使用“数据路径可用性”指标和运行状况探测状态](#vipavailabilityandhealthprobes)进行诊断。

在大多数情况下，可以使用“平均值”作为聚合。 
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>我的负载均衡器的后端实例是否正在响应探测？
<details>
  <summary>展开</summary>
运行状况探测状态指标描述在配置负载均衡器的运行状况探测时，由你配置的应用程序部署的运行状况。 负载均衡器使用运行状况探测的状态来确定要将新流量发送到何处。 运行状况探测源自某个 Azure 基础结构地址，并会显示在 VM 的来宾 OS 中。

若要获取标准负载均衡器资源的运行状况探测状态，请执行以下操作：
1. 选择“运行状况探测状态”作为指标，选择“平均值”作为聚合类型。   
2. 应用基于所需前端 IP 地址和/或端口的筛选器。

运行状况探测会出于以下原因而失败：
- 针对不在侦听、无响应或者使用错误协议的端口配置运行状况探测。 如果服务使用直接服务器返回（DSR 或浮动 IP）规则，请确保服务侦听 NIC IP 配置的 IP 地址，而不仅仅是侦听使用前端 IP 地址配置的环回地址。
- 网络安全组、VM 的来宾 OS 防火墙或应用层筛选器不允许你的探测。

在大多数情况下，可以使用“平均值”作为聚合。 
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>如何检查出站连接统计信息？ 
<details>
  <summary>展开</summary>
“SNAT 连接”指标描述适用于[出站流](https://aka.ms/lboutbound)的成功和失败连接的数量。

如果失败连接数量大于零，则表示 SNAT 端口已耗尽。 必须进一步调查，确定失败的可能原因。 SNAT 端口耗尽的表现形式是无法建立[出站流](https://aka.ms/lboutbound)。 请查看有关出站连接的文章，以了解相关的场景和运行机制，并了解如何缓解并尽量避免 SNAT 端口耗尽的情况。 

若要获取 SNAT 连接统计信息，请执行以下操作：
1. 选择“SNAT 连接”作为指标类型，并选择“总和”作为聚合。   
2. 根据不同行中显示的成功和失败 SNAT 连接计数的“连接状态”进行分组。  

![SNAT 连接](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

图：  负载均衡器 SNAT 连接计数
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>如何检查 SNAT 端口用量和分配？
<details>
  <summary>展开</summary>
“使用的 SNAT 端口数”指标跟踪使用的 SNAT 端口数来维护出站流。 它指示在 Internet 源与负载均衡器后面的没有公共 IP 地址的后端 VM 或虚拟机规模集之间建立了多少个唯一流。 通过将你使用的 SNAT 端口数与“分配的 SNAT 端口数”指标进行比较，可以确定服务是否因为遇到了 SNAT 耗尽问题或者面临着这种风险而导致出站流失败。 

如果指标指出了[出站流](https://aka.ms/lboutbound)失败的风险，请参考相应的文章并采取缓解措施，以确保服务正常运行。

若要查看 SNAT 端口用量和分配：
1. 将图形的时间聚合设置为 1 分钟，以确保显示所需的数据。
1. 选择“使用的 SNAT 端口数”和/或“分配的 SNAT 端口数”作为指标类型，选择“平均值”作为聚合
    * 默认情况下，这些指标是分配到每个后端 VM 或 VMSS 的或者它们使用的平均 SNAT 端口数，对应于映射到负载均衡器的所有前端公共 IP，是基于 TCP 和 UDP 聚合得出的。
    * 若要查看负载均衡器使用的或者为其分配的 SNAT 端口总数，请使用指标聚合“求和” 
1. 根据特定的“协议类型”、一组“后端 IP”和/或“前端 IP”进行筛选。   
1. 若要监视每个后端或前端实例的运行状况，请应用拆分。 
    * 请注意，拆分时每次只允许显示一个指标。 
1. 例如，若要监视每台计算机的 TCP 流的 SNAT 用量，请通过“平均”进行聚合，按“后端 IP”进行拆分，并按“协议类型”进行筛选。    

![SNAT 分配和用量](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*图：一组后端 VM 的平均 TCP SNAT 端口分配和用量*

![按后端实例列出的 SNAT 用量](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

图：  每个后端实例的 TCP SNAT 端口用量
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>如何检查服务的入站/出站连接尝试？
<details>
  <summary>展开</summary>
“SYN 数据包”指标描述收到或发送的、与特定前端关联的 TCP SYN 数据包数量（适用于[出站流](https://aka.ms/lboutbound)）。 可以使用此指标了解对服务发起的 TCP 连接尝试。

在大多数情况下，可以使用“总计”作为聚合。 

![SYN 连接](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

图：  负载均衡器 SYN 计数
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>如何检查网络带宽消耗？ 
<details>
  <summary>展开</summary>
字节和数据包计数器指标描述服务发送或收到的字节和数据包数量，根据前端显示信息。

在大多数情况下，可以使用“总计”作为聚合。 

获取字节或数据包计数统计信息：
1. 选择“字节计数”和/或“数据包计数”作为指标类型，并选择“平均值”作为聚合。    
2. 执行以下操作之一：
   * 在特定的前端 IP、前端端口、后端 IP 或后端端口应用筛选器。
   * 不使用任何筛选器获取负载均衡器资源的总体统计信息。

![字节计数](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

图：  负载均衡器字节计数
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>如何诊断负载均衡器部署？
<details>
  <summary>展开</summary>
在单个图表中结合使用数据路径可用性和运行状况探测状态指标可以识别查找和解决问题的位置。 可以确定 Azure 是否正常工作，并据此最终确定配置或应用程序是否为问题的根本原因。

可以使用运行状况探测指标来了解 Azure 如何根据提供的配置查看部署的运行状况。 在监视或确定原因时，查看运行状况探测始终是合理的第一个动作。

然后可以采取进一步的措施，并使用数据路径可用性指标来深入了解 Azure 如何查看负责特定部署的底层数据平面的运行状况。 结合使用两个指标，可以查明错误的所在位置，如以下示例所示：

![组合使用“数据路径可用性”和“运行状况探测状态”指标](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*图：* 组合使用“数据路径可用性”和“运行状况探测状态”指标

此图表显示以下信息：
- 承载 VM 的基础结构在过去不可用，在图表开始处显示为 0%。 稍后，基础结构正常，VM 可访问，多个 VM 置于后端。 数据路径可用性的蓝色轨迹（稍后显示为 100%）指示了此信息。 
- 图表开头紫色轨迹所示的运行状况探测状态为 0%。 绿色圆圈突出显示了运行状况探测状态变为正常的位置，以及客户部署可以接受新流量的位置。

客户可以使用该图表来自行排查部署问题，而无需猜测或询问支持部门是否发生了其他问题。 此服务之所以不可用，是因为配置不当或应用程序故障导致运行状况探测失败。
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>资源运行状况

可以通过“Monitor”>“服务运行状况”下面的现有“资源运行状况”公开标准负载均衡器资源的运行状况。********

若要查看公共标准负载均衡器资源的运行状况，请执行以下步骤：
1. 选择“Monitor” > “服务运行状况”。

   ![“Monitor”页](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *图：Azure Monitor 中的“服务运行状况”链接*

2. 选择“资源运行状况”，然后确保正确选择“订阅 ID”和“资源类型 = 负载均衡器”。************

   ![资源运行状况](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *图：选择要查看其运行状况的资源*

3. 在列表中，选择要查看其历史运行状况的负载均衡器资源。

    ![负载均衡器运行状况](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *图：负载均衡器资源运行状况视图*
 
[RHC 文档](https://docs.microsoft.com/azure/service-health/resource-health-overview)中提供了一般的资源运行状况状态说明。 下表列出了 Azure 负载均衡器的特定状态： 

| 资源运行状况 | 说明 |
| --- | --- |
| 可用 | 标准负载均衡器资源正常且可用。 |
| 已降级 | 标准负载均衡器具有平台或用户启动的影响性能的事件。 “数据路径可用性”指标至少有两分钟报告了低于 90% 但高于 25% 的运行状况。 你将遇到中等到严重程度的性能影响。 [按照数据路径可用性故障排除指南]来确定是否存在用户启动的影响你的可用性的事件。
| 不可用 | 标准负载均衡器资源不正常。 “数据路径可用性”指标至少有两分钟报告了低于 25% 的运行状况。 你会遇到严重的性能影响，或者入站连接不可用。 可能存在导致不可用的用户或平台事件。 [按照数据路径可用性故障排除指南]来确定是否存在用户启动的影响你的可用性的事件。 |
| Unknown | 标准负载均衡器资源的资源运行状况状态尚未更新，或者最近 10 分钟内未收到数据路径可用性信息。 此状态应该是暂时性的，系统在收到数据后会立即反映正确的状态。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](load-balancer-standard-overview.md)。
- 详细了解[负载均衡器出站连接](https://aka.ms/lboutbound)。
- 了解有关 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 的信息。
- 了解有关 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 的信息，以及[如何通过 REST API 检索指标](/rest/api/monitor/metrics/list)。
