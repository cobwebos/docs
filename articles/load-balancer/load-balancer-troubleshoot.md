---
title: 排查 Azure 负载均衡器问题
description: 了解如何排查 Azure 负载均衡器的已知问题。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 26a4ae7d1a2ef253c0cb62f6bb53f83152676595
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590258"
---
# <a name="troubleshoot-azure-load-balancer"></a>排查 Azure 负载均衡器问题

此页介绍基本和标准 Azure 负载均衡器的常见问题的疑难解答信息。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-diagnostics.md)。

当负载均衡器连接不可用时，最常见的症状如下： 

- 负载均衡器后端的 VM 不响应运行状况探测 
- 负载均衡器后端的 VM 不响应已配置端口上的通信

当后端 VM 的外部客户端通过负载均衡器时，将使用客户端的 IP 地址进行通信。 请确保将客户端的 IP 地址添加到 NSG 允许列表中。 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>故障描述：负载均衡器后端的 VM 不响应运行状况探测
后端服务器必须通过探测检查后，才可加入负载均衡器集。 有关运行状况探测的详细信息，请参阅[了解负载均衡器探测](load-balancer-custom-probe-overview.md)。 

负载均衡器后端池 VM 因下列任意原因而不响应探测： 
- 负载均衡器后端池 VM 不正常 
- 负载均衡器后端池 VM 未侦听探测端口 
- 防火墙或网络安全组要阻止负载均衡器后端池 VM 上的端口 
- 负载均衡器中的其他错误配置

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>原因 1：负载均衡器后端池 VM 不正常 

验证及解决方法

要解决此问题，请登录参与的 VM，并检查 VM 状态是否正常，能否从池中的另一 VM 响应 PsPing 或 TCPing。 如果 VM 不正常或无法响应探测，必须先解决此问题并使 VM 恢复正常状态，然后该 VM 才可参与负载均衡。

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>原因 2：负载均衡器后端池 VM 未侦听探测端口
如果 VM 处于正常状态但未响应探测，则可能是因为参与的 VM 上的探测端口未打开，或 VM 未侦听此端口。

验证及解决方法

1. 登录到后端 VM。 
2. 打开命令提示符并运行下列命令，以验证是否有应用程序在侦听探测端口：   
            netstat -an
3. 如果端口状态未列为“正在侦听”，请配置适当的端口。 
4. 或者，选择其他列为“正在侦听”的端口，并相应地更新负载均衡器配置。              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3：防火墙或网络安全组要阻止负载均衡器后端池 VM 上的端口  
如果 VM 上的防火墙要阻止探测端口，或者子网或 VM 上配置的一个或多个网络安全组禁止探测到达端口，则 VM 无法响应运行状况探测。          

验证及解决方法

* 如果启用了防火墙，请检查它是否配置为允许探测端口。 如果没有，请将其配置为允许探测端口上的流量并重新测试。 
* 在网络安全组列表中，检查探测端口上的传入或传出流量是否受到干扰。 
* 此外，检查 VM NIC 或子网上是否存在优先级高于允许 LB 探测和流量的默认规则的“全部拒绝”网络安全组规则（网络安全组必须允许负载均衡器 IP 168.63.129.16）。 
* 如果上述某规则要阻止探测流量，请将其删除并将规则配置为允许探测流量。  
* 测试 VM 是否现已开始响应运行状况探测。 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4：负载均衡器中的其他错误配置
如果上述各原因貌似都经过验证和正确解决，但后端 VM 仍未响应运行状况探测，请手动测试连接性，并收集一些跟踪信息以了解连接性。

验证及解决方法

* 使用来自 VNet 中其他 VM 的 Psping 进行探测端口响应测试（例如 .\psping.exe -t 10.0.0.4:3389）并记录结果。 
* 使用来自 VNet 中其他 VM 的 TCPing 进行探测端口响应测试（例如 .\tcping.exe 10.0.0.4 3389）并记录结果。 
* 如果在这些 ping 测试中未收到响应，则
    - 在目标后端池 VM 中同时运行一个 Netsh 跟踪，再运行一个来自相同 VNet 的测试 VM。 现在，运行 PsPing 一段时间，收集一些网络跟踪，并停止测试。 
    - 分析网络捕获，查看是否同时存在与 ping 查询相关的传入和传出数据包。 
        - 如果在后端池 VM 中未观察到传入数据包，可能是某个网络安全组或 UDR 错误配置阻止了流量。 
        - 如果在后端池 VM 中未观察到传出数据包，需检查 VM 是否存在任何不相关的问题（例如，应用程序阻止探测端口）。 
    - 验证在到达负载均衡器之前，探测数据包是否强制发送到其他目标（可能通过 UDR 设置发送）。 这会使流量永远无法达到后端 VM。 
* 更改探测类型（例如从 HTTP 到 TCP），并在网络安全组 ACL 和防火墙中配置相应端口，以验证问题是否与探测响应的配置有关。 有关运行状况探测配置的详细信息，请参阅[终结点负载均衡运行状况探测配置](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)。

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>故障描述：负载均衡器后端的 VM 不响应已配置数据端口上的通信

如果后端池 VM 被列为正常且响应运行状况探测，但仍未参与负载均衡，或未响应数据流量，可能是由于以下某项原因： 
* 负载均衡器后端池 VM 未侦听数据端口 
* 网络安全组要阻止负载均衡器后端池 VM 上的端口  
* 从相同的 VM 和 NIC 访问负载均衡器 
* 从参与的负载均衡器后端池 VM 访问 Internet 负载均衡器前端 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1：负载均衡器后端池 VM 未侦听数据端口 
如果 VM 未响应数据流量，这可能是因为参与的 VM 上的目标端口未打开，或者 VM 未侦听此端口。 

验证及解决方法

1. 登录到后端 VM。 
2. 打开命令提示符并运行下列命令，以验证是否有应用程序在侦听数据端口：  netstat -an 
3. 如果端口状态未被列为“正在侦听”，请配置适当的侦听端口 
4. 如果端口被标记为“正在侦听”，请检查该端口的目标应用程序是否存在问题。

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2：网络安全组要阻止负载均衡器后端池 VM 上的端口  

如果子网或 VM 上配置的一个或多个网络安全组要阻止源 IP 或端口，则此 VM 无法响应。

对于公共负载均衡器，将使用 Internet 客户端的 IP 地址在客户端和负载均衡器后端 VM 之间通信。 请确保后端 VM 的网络安全组允许使用客户端的 IP 地址。

1. 列出后端 VM 上配置的网络安全组。 有关详细信息，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)
1. 在网络安全组列表中，检查：
    - 数据端口上的传入或传出流量是否受到干扰。 
    - 检查 VM NIC 或子网上是否存在优先级高于允许负载均衡探测和流量的默认规则的“全部拒绝”网络安全组规则（网络安全组必须允许负载均衡器 IP 168.63.129.16 - 即探测端口）
1. 如果某规则要阻止流量，请将其删除并将规则重新配置为允许数据流量。  
1. 测试 VM 是否现已开始响应运行状况探测。

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3：从相同的 VM 和网络接口访问负载均衡器 

如果负载均衡器后端 VM 上托管的应用程序正尝试通过同一网络接口访问同一后端 VM 上托管的其他应用程序，该操作不受支持且会失败。 

解决方法 - 可通以下方法解决该问题：
* 为每个应用程序配置单独的后端池 VM。 
* 在双 NIC VM 中配置应用程序，以便每个应用程序均使用自己的网络接口和 IP 地址。 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4：从参与的负载均衡器后端池 VM 访问 Internet 负载均衡器前端

如果在 VNet 中配置了内部负载均衡器，并且某个参与的后端 VM 正在尝试访问内部负载均衡器前端，则当将流映射到原始 VM 时会发生故障。 不支持这种情况。 有关详细讨讨论，请参阅[限制](concepts.md#limitations)。

解决方案：有几种方法来取消阻止此方案，包括使用代理。 评估应用程序网关或其他第三方代理服务器（例如 nginx 或 haproxy）。 有关应用程序网关的详细信息，请参阅[应用程序网关概述](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>故障描述：无法更改已在后端池中部署了 VM 规模集的负载均衡器的现有 LB 规则的后端端口。 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>原因：无法针对 VM 规模集引用的负载均衡器的运行状况探测所使用的负载均衡规则修改后端端口。
**解决方案**：若要更改端口，可以通过更新 VM 规模集来删除运行状况探测，更新端口，然后重新配置运行状况探测。

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>故障描述：从负载均衡器的后端池中删除 VM 后，仍有少量流量通过负载均衡器。 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因：从后端池中删除的 VM 不应再接收流量。 少量的网络流量可能与 Azure 中的存储、DNS 和其他功能有关。 
若要进行验证，可以执行网络跟踪。 用于 blob 存储帐户的 FQDN 会列在每个存储帐户的属性中。  在 Azure 订阅中的虚拟机上，可以执行 nslookup 以确定分配给该存储帐户的 Azure IP。

## <a name="additional-network-captures"></a>附加网络捕获
如果决定打开支持案例，请收集下列信息，以更快获得解决方案。 选择单个后端 VM 执行下列测试：
- 使用来自 VNet 中后端 VM 的 Psping 进行探测端口响应测试（例如 psping 10.0.0.4:3389）并记录结果。 
- 如果这些 ping 测试未收到响应，请在运行 PsPing 时，在后端 VM 和 VNet 测试 VM 上同时运行 Netsh 跟踪，并停止 Netsh 跟踪。 
 
## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。

