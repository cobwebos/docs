---
title: 排查 Azure 负载均衡器资源运行状况、前端和后端可用性问题
description: 使用可用指标来诊断降级或不可用的 Azure 标准负载均衡器。
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 6148cedbf004e3e63200ac50b91a40866c5b18db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719632"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>排查资源运行状况、前端和后端可用性问题 

本文介绍了如何调查影响负载均衡器前端 IP 和后端资源可用性的问题。 

## <a name="about-the-metrics-well-use"></a>关于我们将使用的指标
要使用的两个度量值是 *数据路径可用性* 和 *运行状况探测状态* ，并且必须了解其含义才能得出正确的见解。 

## <a name="data-path-availability"></a>数据路径可用性
在配置了负载均衡和入站 NAT 规则的所有前端端口上，每隔25秒由 TCP ping 生成数据路径可用性指标。 然后，此 TCP ping 将路由到任何正常 (已) 后端实例。 如果服务接收到对 ping 的响应，则认为该服务成功，并且将循环访问该度量值的总和（如果失败）。 此指标的计数为每个采样期间的 TCP ping 的总数1/100。 因此，我们想要考虑平均值，这会显示时间段的总和/计数的平均值。 因此，按平均值聚合的数据路径可用性指标为每个负载均衡规则和入站 NAT 规则提供了针对前端 IP 的 TCP ping 的成功率百分比。

## <a name="health-probe-status"></a>运行状况探测状态
运行状况探测状态指标是由运行状况探测中定义的协议的 ping 生成的。 此 ping 将发送到后端池中的每个实例，以及运行状况探测中定义的端口上。 对于 HTTP 和 HTTPS 探测，成功的 ping 需要 HTTP 200 OK 响应，而 TCP 探测则视为成功。 然后，每个探测的连续成功或失败将确定后端实例是否正常运行，以及是否能够接收后端池分配到的负载均衡规则的流量。 与数据路径可用性类似，我们使用平均聚合，它在采样间隔期间告诉我们平均成功/总数 ping。 此运行状况探测状态值通过探测后端实例，无需通过前端发送流量，来指示与负载均衡器隔离的后端运行状况。

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>诊断降级和不可用的负载均衡器
如 [资源运行状况一文](load-balancer-standard-diagnostics.md#resource-health-status)中所述，降级的负载均衡器是指25% 到90% 的数据路径可用性，而不可用的负载均衡器是一分钟内的数据路径可用性低于25% 的负载均衡器。 可以采取这些相同的步骤来调查你在任何运行状况探测状态或配置的数据路径可用性警报中看到的失败。 我们将探讨我们已检查资源运行状况的情况，发现，如果数据路径可用性为0%，我们的负载均衡器不可用-我们的服务已关闭。

首先，请参阅我们的负载均衡器 insights 边栏选项卡的详细指标视图。 可以通过负载均衡器资源边栏选项卡或资源运行状况消息中的链接来执行此操作。  接下来，导航到前端和后端可用性选项卡，并查看处于降级或不可用状态的时间段的三十分钟时间段。 如果我们发现数据路径可用性已为0%，我们知道有一个问题阻碍了所有负载均衡和入站 NAT 规则的流量，并可以查看此影响持续的时间。 

接下来，我们需要检查运行状况探测状态指标来确定数据路径是否不可用，因为我们没有正常的后端实例来提供流量。 如果我们的所有负载均衡和入站规则都有至少一个正常的后端实例，我们知道我们的配置不会导致数据路径不可用。 这种情况表示 Azure 平台问题，但如果你发现这些是自动警报，则不会烦恼，因为这样可以快速解决所有平台问题。

## <a name="diagnose-health-probe-failures"></a>诊断运行状况探测失败
假设我们检查运行状况探测状态，找出所有实例都显示为 "不正常"。 此查找说明了当流量不会到达时，数据路径不可用的原因。 接下来，我们应完成以下清单，排除常见配置错误：
* 检查资源的 CPU 利用率，以检查实例是否确实正常
  * 你可以查看此 
* 如果使用 HTTP 或 HTTPS 探测检查（如果应用程序正常运行且响应）
  * 通过使用与后端实例关联的专用 IP 地址或实例级公共 IP 地址，验证此功能是否正常运行
* 查看应用到后端资源的网络安全组。 确保不存在优先级高于 AllowAzureLoadBalancerInBound 的规则会阻止运行状况探测
  * 为此，可访问后端 Vm 或虚拟机规模集的网络边栏选项卡
  * 如果在这种情况下发现此 NSG 问题，请移动现有允许规则，或创建新的高优先级规则以允许 AzureLoadBalancer 流量
* 检查你的操作系统。 确保 Vm 正在侦听探测端口并查看其 OS 防火墙规则，以确保它们不会阻止来自 IP 地址168.63.129.16 的探测流量。
  * 可以通过运行 netstat-a Windows 命令提示符或 Linux 终端中的 netstat-l 来检查侦听端口
* 请勿在负载均衡器的后端池中放置防火墙 NVA VM，使用 [用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) 通过防火墙将流量路由到后端实例
* 如果使用 HTTP 探测非 HTTP 应用程序的端口，则请确保使用正确的协议。

如果已完成此核对清单，但仍在发现运行状况探测失败，则可能存在影响实例的探测服务的罕见平台问题。 在这种情况下，Azure 会返回，并且会向我们的团队发送自动警报，以便快速解决所有平台问题。

## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 负载均衡器运行状况探测的详细信息](load-balancer-custom-probe-overview.md)
* [详细了解 Azure 负载均衡器指标](load-balancer-standard-diagnostics.md)


