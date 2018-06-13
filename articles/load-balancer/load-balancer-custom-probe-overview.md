---
title: 使用负载均衡器自定义探测来监视运行状况 | Microsoft Docs
description: 了解如何使用 Azure Load Balancer 的自定义探测来监视负载均衡器后面的实例
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
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176782"
---
# <a name="understand-load-balancer-probes"></a>了解负载均衡器探测

Azure 负载均衡器使用运行状况探测来确定应接收新流的后端池实例。 运行状况探测失败时，负载均衡器停止向相应的不正常运行实例发送新流，并且该实例上的现有流不受影响。  所有后端池实例向下探测时，所有现有流将在后端池的所有实例上均超时。

云服务角色（辅助角色和 Web 角色）使用来宾代理探测监视。 当在负载均衡器后面使用 VM 时，必须配置 TCP 或 HTTP 自定义运行状况探测。

## <a name="understand-probe-count-and-timeout"></a>了解探测计数和超时

探测行为取决于：

* 允许实例标记为已开启的成功探测数目。
* 导致实例标记为已关闭的失败探测数目。

在 SuccessFailCount 中设置的超时和频率值可确定是将实例判定为正在运行还是未运行。 在 Azure 门户中，超时设置为频率值的两倍。

终结点（即负载均衡集）的所有负载均衡实例的探测配置必须相同。 对于同一托管服务中特定终结点组合的每个角色实例或 VM 而言，只能使用相同的探测配置。 例如，每个实例必须有相同的本地端口和超时。

> [!IMPORTANT]
> 负载均衡器探测使用 IP 地址 168.63.129.16。 此公共 IP 地址有助于自带 IP Azure 虚拟网络方案中内部平台资源的通信。 虚拟公共 IP 地址 168.63.129.16 用于所有区域，且不会更改。 建议在所有本地防火墙策略中允许此 IP 地址。 不应将它视为安全风险，因为只有内部 Azure 平台可以从该地址获取消息源。 如果在防火墙政策中不允许此 IP 地址，则会在各种方案中出现意外行为。 此行为包括配置相同的 IP 地址范围 168.63.129.16 并复制 IP 地址。

## <a name="learn-about-the-types-of-probes"></a>了解探测类型

### <a name="guest-agent-probe"></a>来宾代理探测

来宾代理探测仅适用于 Azure 云服务。 负载均衡器使用 VM 内部的来宾代理。 然后仅在实例处于就绪状态时，来宾代理才会侦听并以“HTTP 200 正常”响应做出响应。 （其他状态为“繁忙”、“正在回收”或“正在停止”。）

有关详细信息，请参阅[配置运行状况探测的服务定义文件 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx) 或[开始为云服务创建公共负载均衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>来宾代理探测将实例标记为状况不良的原因有哪些？

如果来宾代理未能返回“HTTP 200 正常”响应，则负载均衡器会将实例标记为无响应。 然后停止向该实例发送流量。 负载均衡器将继续 ping 实例。 如果来宾代理使用 HTTP 200 响应，则负载均衡器会再次向该实例发送流量。

使用 Web 角色时，网站代码通常在不受 Azure 结构或来宾代理监视的 w3wp.exe 中运行。 w3wp.exe 中的故障（例如，HTTP 500 响应）不会向来宾代理报告。 因此，负载均衡器会继续轮换该实例。

### <a name="http-custom-probe"></a>HTTP 自定义探测

HTTP 自定义探测：将替代默认来宾代理探测。 可以创建自己的自定义逻辑，以便确定角色实例的运行状况。 默认情况下，负载均衡器每 15 秒探测一次终结点。 如果实例在超时期限内做出 HTTP 200 响应，则认为该实例在负载均衡器轮换阵容中。 默认情况下，超时期限为 31 秒。

如果想要实现自己的逻辑以便从负载均衡器轮换中删除实例，则 HTTP 自定义探测可能很有用。 例如，如果实例的 CPU 利用率超过 90% 并返回非 200 状态，则可决定删除该实例。 如果具有使用 w3wp.exe 的 Web 角色，也可以自动监视网站。 如果网站代码中出现故障，则会向负载均衡器探测返回非 200 状态。

> [!NOTE]
> HTTP 自定义探测仅支持相对路径和 HTTP 协议。 不支持 HTTPS。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>HTTP 自定义探测将实例标记为状况不良的原因有哪些？

* HTTP 应用程序返回非 200 的 HTTP 响应代码（例如，403、404 或 500）。 这是警告应立即将应用程序实例带离服务的肯定回答。
* HTTP 服务器在超时期限之后完全无响应。 根据设置的超时值，在探测标记为未运行之前（也就是说，在发送 SuccessFailCount 探测之前），多个探测请求可能并未获得响应。
* 服务器通过 TCP 重置关闭连接。

### <a name="tcp-custom-probe"></a>TCP 自定义探测

TCP 自定义探测通过使用定义的端口执行三方握手来初始化连接。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>TCP 自定义探测将实例标记为状况不良的原因有哪些？

* TCP 服务器在超时期限之后完全无响应。 当探测标记为未运行的时机取决于失败探测的数目，即，在将探测标记为未运行之前，这些请求未获得答复的次数。
* 探测从角色实例接收 TCP 重置。

有关如何配置 HTTP 运行状况探测或 TCP 探测的详细信息，请参阅[开始使用 PowerShell 在资源管理器中创建公共负载均衡器](load-balancer-get-started-internet-arm-ps.md)。

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>将状况良好的实例添加回负载均衡器轮换阵容

在以下情况下，会将 TCP 和 HTTP 探测视为状况良好，并将角色实例标记为状况良好：

* 负载均衡器在 VM 首次启动时获得有效探测。
* SuccessFailCount 的数字（如前所述）定义了将角色实例标记为状况良好所需的成功探测值。 如果已删除角色实例，成功且连续的探测数目必须大于或等于 SuccessFailCount 的值才能将角色实例标记为正在运行。

> [!NOTE]
> 如果角色实例的运行状况有波动，负载均衡器会等待更长时间，然后将角色实例恢复正常状态。 额外的等待时间保护了用户和基础结构，并且是一种有意而为的策略。

## <a name="use-log-analytics-for-a-load-balancer"></a>使用适用于负载均衡器的 Log Analytics

可以使用 [Log Analytics](load-balancer-monitor-log.md) 来检查负载均衡器探测运行状况和探测计数。 可以配合 Power BI 或 Azure Operation Insights 使用日志记录，以提供有关负载均衡器运行状况的统计信息。
