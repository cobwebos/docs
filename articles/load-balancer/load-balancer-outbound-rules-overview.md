---
title: 出站规则-Azure 负载均衡器
description: 使用此学习路径，开始使用出站规则来定义出站网络地址转换。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304185"
---
# <a name="load-balancer-outbound-rules"></a>负载均衡器出站规则

除了入站连接以外，Azure 负载均衡器还提供从虚拟网络的出站连接。  使用出站规则可以更方便地配置公共[标准负载均衡器](load-balancer-standard-overview.md)的出站网络地址转换。  你可以根据具体的需求，以完全声明性的方式控制出站连接，以缩放和优化此功能。

![负载均衡器出站规则](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用负载均衡器的出站规则可以： 
- 从头开始定义出站 NAT。
- 缩放和优化现有出站 NAT 的行为。 

使用出站规则可以控制：
- 哪些虚拟机应转换为哪些公共 IP 地址。 
- 应如何分配[出站 SNAT 端口](load-balancer-outbound-connections.md#snat)。
- 要为哪些协议提供出站转换。
- 用于出站连接空闲超时的持续时间（4-120 分钟）。
- 是否在空闲超时后发送 TCP 重置

出站规则扩展了[出站连接](load-balancer-outbound-connections.md#lb)一文中所述的[方案 2](load-balancer-outbound-connections.md)，方案优先顺序保持不变。

## <a name="outbound-rule"></a>出站规则

与所有负载均衡器规则一样，出站规则遵循负载均衡和入站 NAT 规则的类似语法：

**前端** + **参数** + **后端池**

出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。  参数针对出站 NAT 算法提供更精细的控制。

API 版本“2018-07-01”允许按如下所示构建出站规则定义：

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>有效出站 NAT 配置是所有出站规则与负载均衡规则的组合。 出站规则是对负载均衡规则的补充。 请查看[禁用负载均衡规则的出站 NAT](#disablesnat)，了解如何在将多个规则应用到 VM 时管理有效出站 NAT 转换。 在定义使用与负载均衡规则相同的公共 IP 地址的出站规则时，必须[禁用出站 SNAT](#disablesnat)。

### <a name="scale"></a>使用多个 IP 地址缩放出站 NAT

尽管出站规则只能配合单个公共 IP 地址使用，但出站规则减轻了缩放出站 NAT 的负担。 规划大规模方案时可以使用多个 IP 地址，并可以使用出站规则来缓解容易出现 [SNAT 耗尽](load-balancer-outbound-connections.md#snatexhaust)的模式。  

前端提供的每个附加 IP 地址可提供 64,000 个临时端口，供负载均衡器用作 SNAT 端口。 尽管负载均衡规则或入站 NAT 规则具有单个前端，但出站规则可以扩展前端的概念，并允许为每个规则使用多个前端。  为每个规则使用多个前端时，可用 SNAT 端口的数量将与每个公共 IP 地址相乘，因此可以支持大型方案。

此外，可以直接对出站规则使用[公共 IP 前缀](https://aka.ms/lbpublicipprefix)。  使用公共 IP 前缀可以更轻松地缩放，并可简化将源自 Azure 部署的流加入允许列表的操作。 可以在负载均衡器资源中配置直接引用公共 IP 地址前缀的前端 IP 配置。  这样，负载均衡器将以独占方式控制公共 IP 前缀，而出站规则将自动使用公共 IP 前缀中包含的所有公共 IP 地址来建立出站连接。  公共 IP 前缀范围内的每个 IP 地址提供 64,000 个临时端口，供负载均衡器用作 SNAT 端口。   

使用此选项时，无法从公共 IP 前缀创建单个公共 IP 地址资源，因为出站规则必须拥有公共 IP 前缀的完全控制权。  如果需要更精细的控制，可以从公共 IP 前缀创建单个公共 IP 地址资源，并将多个公共 IP 地址单独分配到出站规则的前端。

### <a name="snatports"></a>优化 SNAT 端口分配

可以使用出站规则[基于后端池大小优化自动 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)，并分配多于或少于自动 SNAT 端口分配所提供的端口数。

使用以下参数可为每个 VM 分配 10,000 个 SNAT 端口（NIC IP 配置）。
 

          "allocatedOutboundPorts": 10000

出站规则的所有前端中的每个公共 IP 地址最多提供 64,000 个可用作 SNAT 端口的临时端口。  负载均衡器以 8 的倍数分配 SNAT 端口。 如果提供的值不能被 8 整除，则会拒绝配置操作。  如果尝试分配的 SNAT 端口数超过了可用端口数（基于公共 IP 地址数确定），则会拒绝配置操作。  例如，如果为每个 VM 分配10000个端口，而后端池中的7个 Vm 共享单个公共 IP 地址，则配置将被拒绝（7 x 10000 SNAT 端口 > 64000 SNAT 端口）。  将更多的公共 IP 地址添加到出站规则的前端即可实现该方案。

可以通过将端口数指定为 0，恢复为[基于后端池大小的自动 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)。 在这种情况下，第一个 50 VM 实例将获得1024端口，51-100 VM 实例会根据表获得512，依此类推。

### <a name="idletimeout"></a>控制出站流空闲超时

出站规则提供一个配置参数用于控制出站流空闲超时，并使该超时符合应用程序的需求。  出站空闲超时默认为 4 分钟。  该参数接受从 4 到 120 的值用于指定与此特定规则匹配的流的空闲超时分钟数。

使用以下参数可将出站空闲超时设置为 1 小时：

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a><a name="tcpreset"></a>启用处于空闲超时的 TCP 重置

负载均衡器的默认行为是在达到出站空闲超时时以静默方式丢弃流。  使用 enableTCPReset 参数可以启用更有预测性的应用程序行为，并控制在发生出站空闲超时时，是否要发送双向 TCP 重置 (TCP RST)。 

使用以下参数可在出站规则中启用 TCP 重置：

           "enableTcpReset": true

有关详细信息（包括区域可用性），请查看有关[空闲超时的 TCP 重置](https://aka.ms/lbtcpreset)。

### <a name="proto"></a>支持具有单个规则的 TCP 和 UDP 传输协议

可以对出站规则的传输协议使用“所有”，但也可以根据需要将出站规则应用到特定的传输协议。

使用以下参数可将协议设置为 TCP 和 UDP：

          "protocol": "All"

### <a name="disablesnat"></a>禁用负载均衡规则的出站 NAT

如前所述，负载均衡规则提供出站 NAT 的自动编程。 但是，某些方案受益于或者要求通过负载均衡规则禁用出站 NAT 的自动编程，以便能够控制或优化行为。  在某些出站规则方案中，必须停止自动出站 NAT 编程。

可通过两种方式使用此参数：
- （可选）禁止将入站 IP 地址用于出站 NAT。  出站规则是对负载均衡规则的补充，如果设置此参数，则出站规则将会受控。
  
- 优化同时用于入站和出站连接的 IP 地址的出站 NAT 参数。  必须禁用自动出站 NAT 编程才能让出站规则接管控制权。  例如，若要更改同时用于入站连接的某个地址的 SNAT 端口分配，则必须将此参数设置为 true。  如果尝试使用出站规则来重新定义同时用于入站连接的某个 IP 地址的参数，但尚未释放负载均衡规则的出站 NAT 编程，则配置出站规则的操作将会失败。

>[!IMPORTANT]
> 如果将此参数设置为 true，但没有任何出站规则（或[实例级公共 IP 方案](load-balancer-outbound-connections.md#ilpip)）定义出站连接，则虚拟机不会建立出站连接。  VM 或应用程序的某些操作可能依赖于可用的出站连接。 请务必了解方案的依赖关系，并考虑此项更改造成的影响。

可以使用以下配置参数在负载均衡规则中禁用出站 SNAT：

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

disableOutboundSNAT 参数默认为 false，这意味着，负载均衡规则**确实**会提供自动出站 NAT 作为负载均衡规则配置的镜像。  

如果在负载均衡规则中将 disableOutboundSnat 设置为 true，则负载均衡规则将释放其他自动出站 NAT 编程的控制权。  出站 SNAT 随着负载均衡规则的启用而禁用。

### <a name="reuse-existing-or-define-new-backend-pools"></a>重复使用现有后端池或定义新的后端池

出站规则没有引入有关定义要应用规则的 VM 组的新概念，  而重复使用后端池的概念（同样用于负载均衡规则）。 可以通过重复使用现有后端池定义或者为出站规则专门创建一个后端池，使用此概念来简化配置。

## <a name="scenarios"></a>方案

### <a name="groom"></a>将出站连接整理成一组特定的公共 IP 地址

可以使用出站规则来整理出站连接，使之看上去像是源自一组特定的公共 IP 地址，以简化允许列表方案。  此源公共 IP 地址可与负载均衡规则使用的 IP 地址相同，也可以是与负载均衡规则使用的 IP 地址不同的一组公共 IP 地址。  

1. 创建[公共 IP 前缀](https://aka.ms/lbpublicipprefix)（或者从公共 IP 前缀创建公共 IP 地址）
2. 创建公共标准负载均衡器
3. 创建引用所要使用的公共 IP 前缀（或公共 IP 地址）的前端
4. 重复使用某个后端池或创建一个后端池，并将 VM 放入公共负载均衡器的后端池
5. 在公共负载均衡器中配置出站规则，以使用前端为这些 VM 的出站 NAT 编程
   
如果不希望将负载均衡规则用于出站连接，则需要在负载均衡规则中[禁用出站 SNAT](#disablesnat)。

### <a name="modifysnat"></a>修改 SNAT 端口分配

可以使用出站规则[基于后端池大小优化自动 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)。

例如，如果你的两个虚拟机共享用于出站 NAT 的单个公共 IP 地址，则在遇到 SNAT 耗尽时，你可能希望增加分配的 SNAT 端口数，而不再使用默认的 1024 个端口。 每个公共 IP 地址最多可以提供 64,000 个临时端口。  如果使用单个公共 IP 地址前端配置出站规则，则总共可以向后端池中的 VM 分配 64,000 个 SNAT 端口。  对于两个 VM，可以使用出站规则最多分配 32,000 个 SNAT 端口 (2x32,000 = 64,000)。

查看[出站连接](load-balancer-outbound-connections.md)，以及有关如何分配和使用 [SNAT](load-balancer-outbound-connections.md#snat) 端口的详细信息。

### <a name="outboundonly"></a>仅启用出站连接

可以使用公共标准负载均衡器为一组 VM 提供出站 NAT。 在此方案中，可以单独使用出站规则，而无需其他任何规则。

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>仅对 VM 使用出站 NAT（无入站连接）

定义一个公共标准负载均衡器，将 VM 放入后端池，配置一个出站规则用来为出站 NAT 编程，并整理出站连接，使其看上去源自特定的公共 IP 地址。 还可以使用公共 IP 前缀来简化出站连接源的允许列表操作。

1. 创建公共标准负载均衡器。
2. 创建一个后端池，并将 VM 放入公共负载均衡器的后端池。
3. 在公共负载均衡器中配置出站规则，以便为这些 VM 的出站 NAT 编程。

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>内部标准负载均衡器方案的出站 NAT

使用内部标准负载均衡器时，只有显式声明出站连接之后，出站 NAT 才可用。 你可以通过以下步骤，使用出站规则为内部标准负载均衡器后面的 VM 创建出站连接，以定义出站连接：

1. 创建公共标准负载均衡器。
2. 除了内部负载均衡器，还要创建一个后端池，并将 VM 放入公共负载均衡器的后端池。
3. 在公共负载均衡器中配置出站规则，以便为这些 VM 的出站 NAT 编程。

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>使用公共标准负载均衡器为出站 NAT 启用 TCP 和 UDP 协议

- 使用公共标准负载均衡器时，提供的自动出站 NAT 编程与负载均衡规则的传输协议相匹配。  

   1. 在负载均衡规则中禁用出站 SNAT。
   2. 在同一个负载均衡器上配置出站规则。
   3. 重复使用 VM 已用的后端池。
   4. 在出站规则中指定“协议”:“所有”。

- 只使用入站 NAT 规则时，不会提供出站 NAT。

   1. 将 VM 放入后端池。
   2. 使用公共 IP 地址或公共 IP 前缀定义一个或多个前端 IP 配置。
   3. 在同一个负载均衡器上配置出站规则。
   4. 在出站规则中指定“协议”:“所有”

## <a name="limitations"></a>限制

- 每个前端 IP 地址的最大可用临时端口数为 64,000。
- 可配置的出站空闲超时范围为 4 到 120 分钟（240 到 7200 秒）。
- 负载均衡器不支持将 ICMP 用于出站 NAT。
- 出站规则只能应用于 NIC 的主 IP 配置。  支持多个 Nic。

## <a name="next-steps"></a>后续步骤

- 了解如何[对出站连接使用负载均衡器](load-balancer-outbound-connections.md)。
- 了解[标准负载均衡器](load-balancer-standard-overview.md)。
- 了解[空闲超时时的双向 TCP 重置](load-balancer-tcp-reset.md)。
- [使用 Azure CLI 2.0 配置出站规则](configure-load-balancer-outbound-cli.md)。
