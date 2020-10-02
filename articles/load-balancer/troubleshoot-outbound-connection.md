---
title: 排查 Azure 负载均衡器中的出站连接问题
description: 通过 Azure 负载均衡器排查出站连接常见问题的解决方法。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: c37c0e9b914854ff41053526740d3454c5c23f90
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628989"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> 排查出站连接故障

本文旨在为 Azure 负载均衡器的出站连接可能出现的常见问题提供解决方法。 客户遇到的大多数出站连接问题是由于 SNAT 端口耗尽和连接超时导致数据包丢失。 本文提供了用于缓解上述每个问题的步骤。

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>应对 SNAT (PAT) 端口耗尽问题
用于 [PAT](load-balancer-outbound-connections.md) 的[临时端口](load-balancer-outbound-connections.md)是可用尽的资源，如[无公共 IP 地址的独立 VM](load-balancer-outbound-connections.md) 和[无公共 IP 地址的负载均衡 VM](load-balancer-outbound-connections.md) 中所述。 可以根据[此](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation)指南来监视临时端口的使用情况，并将其与当前分配进行比较，以确定 SNAT 耗尽的风险或确认 SNAT 是否耗尽。

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，观察失败的出站连接，或者支持人员通知已耗尽 SNAT 端口（[PAT](load-balancer-outbound-connections.md) 使用的预先分配[临时端口](load-balancer-outbound-connections.md#preallocatedports)），则有几个常见缓解选项可供选择。 查看这些选项，确定可用且最适合自己的方案的选项。 一个或多个选项可能有助于管理此方案。

如果难以理解出站连接行为，可以使用 IP 堆栈统计 (netstat)。 或者使用数据包捕获来观察连接行为。 可以在实例的来宾 OS 中执行这些数据包捕获，或使用[网络观察程序来捕获数据包](../network-watcher/network-watcher-packet-capture-manage-portal.md)。 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>手动分配 SNAT 端口以最大限度地增加每个 VM 的 SNAT 端口数
按照[预分配端口](load-balancer-outbound-connections.md#preallocatedports)中的定义，负载均衡器会根据后端的 VM 数量自动分配端口。 默认情况下，此操作保守地进行，以确保可伸缩性。 如果已知后端会有的最大 VM 数，则可以在每个出站规则中手动分配 SNAT 端口。 例如，如果你知道最多有 10 个VM，则可以为每个 VM 分配 6,400 个 SNAT 端口，而不是默认的 1024 个。 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>修改应用程序以重复使用连接 
在应用程序中重复使用连接，可以降低对用于 SNAT 的临时端口的需求。 连接重用与 HTTP/1.1 之类的协议尤其相关，在这些协议中，默认设置是重复使用连接。 其他使用 HTTP 作为其传输（如 REST）的协议也可以因此受益。 

对每个请求来说，重复使用总是优于单独的原子 TCP 连接。 重复使用可以带来更高的性能和非常高效的 TCP 事务。

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>修改应用程序以使用连接池
可以在应用程序中使用连接池方案，其中在一组固定的连接（在可能的情况下，重复使用每一个）上，内部分布请求。 此方案会限制正在使用的临时端口的数量，并创建更加可预测的环境。 此方案还可以通过在操作答复上单个连接阻塞时允许多个同时操作，增加请求的吞吐量。  

连接池可能已经存在于正在使用的框架中，以开发应用程序或应用程序的配置设置。 可将连接池与连接重复使用相结合。 多个请求使用指向相同目标 IP 地址和端口的可预测固定数量的端口。 请求还可以从 TCP 事务的高效使用中受益，从而减少延迟和资源利用。 UDP 事务也能从中受益，因为管理 UDP 流数又能避免耗尽情况和管理 SNAT 端口利用率。

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>修改应用程序以使用主动性较低的重试逻辑
当用于 [PAT](load-balancer-outbound-connections.md) 的[预先分配临时端口](load-balancer-outbound-connections.md#preallocatedports)耗尽或应用程序故障发生时，无衰减或回退逻辑的积极重试或暴力重试会使耗尽状况再次发生或一直持续。 使用主动性较低的重试逻辑，可以降低对临时端口的需求。 

临时端口有 4 分钟的空闲超时（不可调整）。 如果重试太过积极，则消耗没有机会进行自行清除。 因此，应用程序停用事务的方式和频率对于设计至关重要。

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>向每个 VM 分配公共 IP
分配公共 IP 地址会将方案更改为 [VM 的公共 IP](load-balancer-outbound-connections.md)。 用于各 VM 的公共 IP 的所有临时端口都可供 VM 使用。 与使用与相应后端池关联的所有 Vm 共享公共 IP 的临时端口的方案相反 (。 ) 需要考虑一些权衡，如公共 IP 地址的额外成本和筛选大量单个 IP 地址的潜在影响。

>[!NOTE] 
>此选项不适用于 Web 辅助角色。

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>使用多个前端
使用公共标准负载均衡器时，可[为出站连接分配多个前端 IP 地址](load-balancer-outbound-connections.md)，并[将可用的 SNAT 端口数倍增](load-balancer-outbound-connections.md#preallocatedports)。  创建前端 IP 配置、规则和后端池，以触发将 SNAT 编程到前端公共 IP。  该规则不需要运行，并且运行状况探测不需要成功。  如果对入站连接（而不仅仅是出站连接）也使用多个前端，应使用自定义的运行状况探测来确保可靠性。

>[!NOTE]
>在大多数情况下，SNAT 端口耗尽是设计不当的征兆。  在使用更多前端来添加 SNAT 端口之前，请确保已了解端口耗尽的原因。  否则可能会忽视问题，导致以后出现故障。

## <a name="scale-out"></a><a name="scaleout"></a>横向扩展
[预分配端口](load-balancer-outbound-connections.md#preallocatedports)是根据后端池大小分配的并且分组到各个层中，这样，当某些端口必须重新分配以适应后端池更大的下一个层时，可以最大限度地减少中断。  有可能可以通过将后端池扩展到给定层的最大大小来提高给定前端的 SNAT 端口利用率。  请记住，应用程序需要分配默认端口才能有效横向扩展而不会有耗尽 SNAT 的风险。

例如，后端池中的两个虚拟机对于每个 IP 配置将有 1024 个 SNAT 端口可用，整个部署总共有 2048 个 SNAT 端口。  如果部署已增加到 50 台虚拟机，虽然每台虚拟机的预分配端口数保持不变，但整个部署可以使用 51,200 (50 x 1024) 个 SNAT 端口。  如果希望横向扩展你的部署，请检查每层的[预分配端口](load-balancer-outbound-connections.md#preallocatedports)的数量，确保将横向扩展规划为各自层的最大容量。  在上述示例中，如果选择了横向扩展到 51 个而非 50 个实例，则你将提升到下一个层，最终，每台 VM 的 SNAT 端口数以及端口总数会更少。

如果横向扩展到后端池较大的下一层，并且需要重新分配已分配端口，则部分出站连接可能会超时。  如果仅使用部分 SNAT 端口，则在后端池较大的下一层中横向扩展无意义。  每次移动到后端池的下一层时，半数现有端口将重新分配。  如果不希望发生此行为，则需要将部署规划为层大小。  或者确保应用程序可以根据需要进行检测和重试。  TCP keepalive 可以帮助检测 SNAT 端口何时由于被重新分配而不再工作。

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>保持 keepalive 重置出站空闲超时
出站连接有 4 分钟的空闲超时。 此超时可通过[出站规则](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout)进行调整。 还可以使用传输（例如，TCP Keepalives）或应用程序层 Keepalives 刷新空闲流，并在必要时重置此空闲超时。  

使用 TCP keepalive 时，在连接的一端启用它们就足够了。 例如，若要重置流的空闲计时器，在服务器端启用它们就足够了，没有必要在两端都启动 TCP keepalive。  应用程序层（包括数据库客户端-服务器配置）也存在类似的概念。  检查服务器端对于特定于应用程序的 keepalive 存在哪些选项。

## <a name="next-steps"></a>后续步骤
我们一直在寻求改善客户体验。 如果你遇到本文未列出或未解决的出站连接问题，请通过此页底部的 GitHub 提交反馈，我们将尽快解决你的反馈。
