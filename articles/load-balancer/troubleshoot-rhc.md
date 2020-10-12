---
title: 排查 Azure 负载均衡器资源运行状况、前端和后端可用性问题
description: 使用提供的指标来诊断降级的或不可用的 Azure 标准负载均衡器。
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
ms.openlocfilehash: 1af3ce7125d30ed0cb9b8ca6b3cb9322dc14c520
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855253"
---
# <a name="troubleshoot-resource-health-frontend-and-backend-availability-issues"></a>排查资源运行状况、前端和后端可用性问题 

本文介绍了如何调查影响负载均衡器前端 IP 和后端资源的可用性的问题。 

## <a name="about-the-metrics-well-use"></a>关于我们将使用的指标
要使用的两个指标是“数据路径可用性”和“运行状况探测状态”，必须了解其含义才能得出正确的见解。 

## <a name="data-path-availability"></a>数据路径可用性
在配置了负载均衡和入站 NAT 规则的所有前端端口上，TCP ping 每隔 25 秒就会生成数据路径可用性指标。 然后，此 TCP ping 将路由到任何正常的（探测结果为正常运行的）后端实例。 如果服务收到对 ping 的响应，则可将其视为成功，指标的总和会被迭代一次；如果失败，则不会执行此操作。 此指标的计数为每个采样期间的 TCP ping 的总数的 1/100。 因此，我们希望考虑平均值，这样就会显示该时间段内的总和/计数的平均值。 因此，按平均值聚合的数据路径可用性指标为每个负载均衡规则和入站 NAT 规则提供了针对前端 IP:端口的 TCP ping 的成功率百分比。

## <a name="health-probe-status"></a>运行状况探测状态
运行状况探测状态指标是由运行状况探测中定义的协议的 ping 生成的。 此 ping 将发送到后端池中的每个实例，以及运行状况探测中定义的端口上。 对于 HTTP 和 HTTPS 探测，成功的 ping 要求获得 HTTP 200 OK 响应，而对于 TCP 探测，任何响应都会被视为成功。 然后，可以通过每个探测的连续成功或失败来确定后端实例是否正常运行，以及是否能够接收后端池分配到的负载均衡规则的流量。 与数据路径可用性类似，我们使用平均聚合，它告诉我们在采样间隔期间的平均成功/总计 ping 数。 此运行状况探测状态值通过探测后端实例而不通过前端来发送流量，指示与负载均衡器隔离的后端运行状况。

>[!IMPORTANT]
>运行状况探测状态每分钟采样一次。 这可能会导致本应稳定的值出现轻微波动。 例如，如果有两个后端实例，一个的探测结果为正常运行，另一个的探测结果为关闭，则运行状况探测服务可以为正常的实例捕获 7 个样本，为不正常的实例捕获 6 个样本。 这将导致以前的稳定值 50 在一分钟时间间隔内显示为 46.15。 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>诊断降级的和不可用的负载均衡器
如[“资源运行状况”一文](load-balancer-standard-diagnostics.md#resource-health-status)所述，降级的负载均衡器显示的数据路径可用性为 25% 到 90%，而不可用的负载均衡器在两分钟内的数据路径可用性低于 25%。 可以采取这些相同的步骤来调查在你配置的任何运行状况探测状态或数据路径可用性警报中看到的失败。 我们将探讨一个案例。在该案例中，我们在检查了资源运行状况后发现，如果数据路径可用性为 0%，则我们的负载均衡器不可用 - 我们的服务已关闭。

首先，我们转到负载均衡器见解边栏选项卡的详细指标视图。 你可以通过负载均衡器资源边栏选项卡或通过资源运行状况消息中的链接来执行此操作。  接下来，我们导航到前端和后端可用性选项卡，查看一个出现降级或不可用状态的三十分钟时段窗口。 如果我们发现数据路径可用性已为 0%，则表明有一个问题阻碍了所有负载均衡和入站 NAT 规则的流量，我们可以看到此影响的持续时间。 

接下来，我们需要检查运行状况探测状态指标，以便确定数据路径不可用是不是因为我们没有正常的后端实例来提供流量。 如果我们的所有负载均衡和入站规则都有至少一个正常的后端实例，则表明不是我们的配置导致数据路径不可用。 这种情况表明 Azure 平台有问题，尽管很少见。如果发现这些问题，请不要担心，因为系统会向我们的团队发送自动警报，这样就可以快速解决所有平台问题。

## <a name="diagnose-health-probe-failures"></a>诊断运行状况探测失败
假设我们检查运行状况探测状态后发现所有实例都显示为不正常。 此检查结果说明了为何数据路径不可用，其表现为流量无处可去。 接下来，我们应按以下清单进行检查，以排除常见的配置错误：
* 检查你的资源的 CPU 利用率，以检查实例实际上是否正常
  * 你可以检查此情况 
* 如果使用 HTTP 或 HTTPS 探测，请检查应用程序是否正常运行且做出响应
  * 通过使用与后端实例关联的专用 IP 地址或实例级公共 IP 地址直接访问应用程序，验证此功能是否正常
* 查看应用于后端资源的网络安全组。 确保不存在优先级高于 AllowAzureLoadBalancerInBound 的规则，这种规则会阻止运行状况探测
  * 为此，可访问后端 VM 或虚拟机规模集的“网络”边栏选项卡
  * 如果你发现存在此 NSG 问题，请移动现有的 Allow 规则，或创建一个新的高优先级规则以允许 AzureLoadBalancer 流量
* 检查 OS。 确保你的 VM 正在侦听探测端口，并查看其 OS 防火墙规则，以确保它们没有阻止来自 IP 地址 168.63.129.16 的探测流量。
  * 可以通过在 Windows 命令提示符中运行 netstat -a 或在 Linux 终端中运行 netstat -l 来检查侦听端口
* 请勿在负载均衡器的后端池中放置防火墙 NVA VM，请使用[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)通过防火墙将流量路由到后端实例
* 请确保使用正确的协议。如果使用 HTTP 来探测侦听非 HTTP 应用程序的端口，则探测会失败

如果已按此清单检查完毕，但仍然发现运行状况探测失败，则可能存在影响实例的探测服务的罕见平台问题。 在这种情况下，Azure 会为你提供支持。系统会向我们的团队发送自动警报，因此可以快速解决所有平台问题。

## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 负载均衡器运行状况探测](load-balancer-custom-probe-overview.md)
* [详细了解 Azure 负载均衡器指标](load-balancer-standard-diagnostics.md)


