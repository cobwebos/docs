---
title: 使用负载均衡器运行状况探测保护服务 | Microsoft Docs
description: 了解如何使用运行状况探测来监视负载均衡器后的实例
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2018
ms.author: kumud
ms.openlocfilehash: 7366273e30132daf7dc5ea15072c574180d1bc8b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397273"
---
# <a name="load-balancer-health-probes"></a>负载均衡器运行状况探测

Azure 负载均衡器使用运行状况探测来确定要接收新流的后端池实例。 可以使用运行状况探测来检测后端实例上应用程序的故障。 还可以对运行状况探测生成自定义响应，并使用运行状况探测进行流控制和向负载均衡器发出信号，指明是继续向后端实例发送新流还是停止发送新流。 此方法可用于管理负载或计划内停机时间。

运行状况探测失败时，负载均衡器停止向各个不正常的实例发送新流。 新流和现有流的行为取决于流是 TCP 还是 UDP，以及使用的负载均衡器 SKU。  有关详细信息请查看[探测停止行为](#probedown)。

> [!IMPORTANT]
> 负载均衡器运行状况探测源自 IP 地址 168.63.129.16，要使探测将实例标记为运行，不得阻止这些探测。  有关详细信息，请查看[探测源 IP 地址](#probesource)。

## <a name="health-probe-types"></a>运行状况探测类型

运行状况探测可以观测后端实例上的任何端口，包括提供实际服务的端口。 运行状况探测支持 TCP 侦听器或 HTTP 终结点。 

对于 UDP 负载均衡，应使用 TCP 或 HTTP 运行状况探测为后端实例生成自定义运行状况探测信号。

对[标准负载均衡器](load-balancer-standard-overview.md)使用 [HA 端口负载均衡规则](load-balancer-ha-ports-overview.md)时，将对所有端口进行负载均衡个运行状况探测响应应该反映整个实例的状态。  

不应通过接收运行状况探测的实例在 VNet 中的另一个实例上 NAT 或代理某个运行状况探测，因为这可能导致方案中出现连锁失败。

如果想要测试运行状况探测失败或者将单个实例标记为关闭，可以使用安全组显式阻止该运行状况探测（目标或[源](#probesource)）。

### <a name="tcp-probe"></a>TCP 探测器

TCP 探测通过使用定义的端口执行三方开放式 TCP 握手来初始化连接。  然后，执行四方封闭式 TCP 握手。

最小探测间隔为 5 秒，不正常响应的最小数目为 2。  总持续时间不能超过 120 秒。

如果出现以下情况，TCP 探测将会失败：
* 实例上的 TCP 侦听器在超时期限内根本未做出响应。  根据失败的探测请求（配置为标记探测停止之前未获应答的请求）数目，已将探测标记为停止。
* 探测从实例接收 TCP 重置。

### <a name="http-probe"></a>HTTP 探测器

HTTP 探测建立 TCP 连接，并发出包含指定路径的 HTTP GET。 HTTP 探测支持 HTTP GET 的相对路径。 如果实例在超时期限内做出响应并返回 HTTP 状态 200，则将运行状况探测标记为运行。  默认情况下，HTTP 运行状况探测每隔 15 秒尝试检查配置的运行状况探测端口。 最小探测间隔为 5 秒。 总持续时间不能超过 120 秒。 


如果想要实现自己的逻辑以便从负载均衡器轮转中删除实例，则 HTTP 探测也可能很有用。 例如，如果实例的 CPU 利用率超过 90% 并返回非 200 HTTP 状态，则你可以决定删除该实例。 

如果使用云服务，并且现有的 Web 角色使用 w3wp.exe，则你还可以实现自动网站监视。 如果网站代码中出现故障，则会向负载均衡器探测返回非 200 状态。  HTTP 探测替代默认的来宾代理探测。 

如果出现以下情况，HTTP 探测将会失败：
* HTTP 探测终结点返回非 200 的 HTTP 响应代码（例如，403、404 或 500）。 这会立即标记运行状况探测停止。 
* 在 31 秒超时期限内，HTTP 探测终结点根本未做出响应。 根据设置的超时值，在探测标记为未运行之前（也就是说，在发送 SuccessFailCount 探测之前），多个探测请求可能并未获得响应。
* HTTP 探测终结点通过 TCP 重置关闭连接。

### <a name="guest-agent-probe-classic-only"></a>来宾代理探测（仅限经典模式）

云服务角色（辅助角色和 Web 角色）默认使用来宾代理进行探测监视。   应将此方案视为最后一个选项。  始终应该使用 TCP 或 HTTP 探测显式定义运行状况探测。 对于大多数应用程序方案而言，来宾代理探测的有效性不如显式定义的探测。  

来宾代理探测是对 VM 中来宾代理执行的检查。 然后仅在实例处于就绪状态时，来宾代理才会侦听并以“HTTP 200 正常”响应做出响应。 （其他状态为“繁忙”、“正在回收”或“正在停止”。）

有关详细信息，请参阅[配置运行状况探测的服务定义文件 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx) 或[开始为云服务创建公共负载均衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

如果来宾代理未能返回“HTTP 200 正常”响应，则负载均衡器会将实例标记为无响应。 然后停止向该实例发送流。 负载均衡器继续检查实例。 

如果来宾代理使用 HTTP 200 做出响应，则负载均衡器会再次向该实例发送新流。

使用 Web 角色时，网站代码通常在不受 Azure 结构或来宾代理监视的 w3wp.exe 中运行。 w3wp.exe 中的故障（例如，HTTP 500 响应）不会向来宾代理报告。 因此，负载均衡器会继续轮换该实例。

## <a name="probe-health"></a>探测运行状况

在以下情况下，会将 TCP 和 HTTP 运行状况探测视为正常，并将角色实例标记为正常：

* VM 首次启动时，运行状况探测成功。
* SuccessFailCount 的数字（如前所述）定义了将角色实例标记为状况良好所需的成功探测值。 如果已删除角色实例，成功且连续的探测数目必须大于或等于 SuccessFailCount 的值才能将角色实例标记为正在运行。

> [!NOTE]
> 如果角色实例的运行状况有波动，负载均衡器会等待更长时间，然后将角色实例恢复正常状态。 额外的等待时间保护了用户和基础结构，并且是一种有意而为的策略。

## <a name="probe-count-and-timeout"></a>探测计数和超时

探测行为取决于：

* 允许将实例标记为运行所要成功的探测次数。
* 导致将实例标记为关闭的失败探测数目。

在 SuccessFailCount 中设置的超时和频率值可确定是将实例判定为正在运行还是未运行。 在 Azure 门户中，超时设置为频率值的两倍。

负载均衡规则包含单个定义了相应后端池的运行状况探测。

## <a name="probedown"></a>探测停止行为

### <a name="tcp-connections"></a>TCP 连接

正常且包含能够接受新流的来宾 OS 和应用程序的后端实例会成功建立新的 TCP 连接。

如果后端实例的运行状况探测失败，与此后端实例建立的 TCP 连接会继续。

如果后端池中所有实例的所有探测都失败，则不会将任何新流发送到后端池。 标准负载均衡器将允许已建立的 TCP 流继续。  基本负载均衡器会终止发往后端池的所有现有 TCP 流。
 
由于流始终在客户端与 VM 的来宾 OS 之间传送，所有探测均停止的池会导致前端不会对 TCP 连接打开尝试做出响应，因为没有任何正常的后端实例可以接收流。

### <a name="udp-datagrams"></a>UDP 数据报

UDP 数据报将传送到正常的后端实例。

UDP 是无连接的，并且系统不会跟踪 UDP 的流状态。 如果后端实例的任何运行状况探测失败，现有的 UDP 流可以转移到后端池中的另一个正常实例。

如果后端池中所有实例的所有探测都失败，则基本和标准负载均衡器的现有 UDP 流将会终止。


## <a name="probesource"></a>探测源 IP 地址

所有负载均衡器运行状况探测源自 IP 地址 168.63.129.16（源）。  将自己的 IP 地址放入 Azure 虚拟网络时，可以保证此运行状况探测源 IP 地址是唯一的，因为 Microsoft 会全局保留此地址。  此地址在所有区域中相同，并且不会更改。 不应将它视为安全风险，因为只有内部 Azure 平台可以从此 IP 地址获取数据包源。 

要使负载均衡器的运行状况探测将实例标记为运行，**必须**在任何 Azure [安全组](../virtual-network/security-overview.md)和本地防火墙策略中允许此 IP 地址。

如果在防火墙策略中不允许此 IP 地址，运行状况探测将会失败，因为它无法访问实例。  而由于发生运行状况探测失败，负载均衡器会将实例标记为关闭。  这会导致负载均衡服务失败。 

此外，不应使用包含 168.63.129.16 的、使用 Microsoft 拥有的 IP 地址范围来配置 VNet。  此地址范围与运行状况探测的 IP 地址相冲突。

如果 VM 上有多个接口，则需要确保能够响应收到请求的接口上的探测。  这可能需要基于每个接口，以唯一的方式对 VM 中的此地址执行源 NAT。

## <a name="monitoring"></a>监视

所有[标准负载均衡器](load-balancer-standard-overview.md)会针对每个实例，通过 Azure Monitor 将运行状况探测状态公开为多维指标。

基本负载均衡器针对每个后端池，通过 Log Analytics 公开运行状况探测状态。  这只适用于公共基本负载均衡器，而不适用于内部基本负载均衡器。  可以使用 [Log Analytics](load-balancer-monitor-log.md) 来检查公共负载均衡器探测运行状况和探测计数。 可以配合 Power BI 或 Azure Operation Insights 使用日志记录，以提供有关负载均衡器运行状况的统计信息。


## <a name="limitations"></a>限制

-  HTTP 运行状况探测不支持 TLS (HTTPS)。  请改用端口 443 进行 TCP 探测。

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 在资源管理器中开始创建公共负载均衡器](load-balancer-get-started-internet-arm-ps.md)
- [运行状况探测的 REST API](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

