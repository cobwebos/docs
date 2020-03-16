---
title: 排查 Azure 虚拟网络 NAT 连接问题
titleSuffix: Azure Virtual Network NAT troubleshooting
description: 排查虚拟网络 NAT 的问题。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674328"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>排查 Azure 虚拟网络 NAT 连接问题

本文帮助管理员诊断和解决在使用虚拟网络 NAT 时出现的连接问题。

>[!NOTE] 
>虚拟网络 NAT 暂时以公共预览版提供。 目前它只能在有限的几个[区域](nat-overview.md#region-availability)中使用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="problems"></a>问题

* [SNAT 耗尽](#snat-exhaustion)
* [ICMP ping 失败](#icmp-ping-is-failing)
* [连接失败](#connectivity-failures)
* [IPv6 共存](#ipv6-coexistence)

若要解决这些问题，请遵循以下部分中的步骤。

## <a name="resolution"></a>解决方法

### <a name="snat-exhaustion"></a>SNAT 耗尽

一个 [NAT 网关资源](nat-gateway-resource.md)支持 64,000 到 100 万个并发流。  每个 IP 地址为可用库存提供 64,000 个 SNAT 端口。 对于每个 NAT 网关资源，最多可以使用 16 个 IP 地址。  [此文](nat-gateway-resource.md#source-network-address-translation)更详细地介绍了 SNAT 机制。

通常，SNAT 耗尽的根本原因是建立和管理出站连接的方式出现了对立模式。  请认真阅读本部分。

#### <a name="steps"></a>步骤

1. 调查应用程序如何建立出站连接（例如，通过代码评审或数据包捕获进行调查）。 
2. 确定此活动是否为预期行为，或者应用程序是否行为异常。  使用 Azure Monitor 中的[指标](nat-metrics.md)来证实发现结果。 对“SNAT 连接”指标使用“失败”类别。
3. 评估是否遵循适当的模式。
4. 评估向 NAT 网关资源分配更多的 IP 地址是否应该可以缓解 SNAT 端口耗尽的问题。

#### <a name="design-patterns"></a>设计模式

始终尽量利用连接重用和连接池。  这些模式可以彻底避免资源耗尽问题，并使行为可预测、可靠且可缩放。 在许多开发库和框架中，都可以找到这些模式的根源。

_**解决方法：**_ 使用适当的模式

- 考虑对长时间运行的操作使用[异步轮询模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)，以释放连接资源供其他操作使用。
- 生存期较长的流（例如重复使用的 TCP 连接）应使用 TCP Keepalive 或应用层 Keepalive，以避免中间系统超时。
- 应使用正常[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)，以避免在发生暂时性故障或故障恢复期间出现频繁重试/突发。
为每个 HTTP 操作创建新 TCP 连接（也称为“原子连接”）是一种对立模式。  原子连接会阻止应用程序正常缩放，并且会浪费资源。  始终通过管道将多个操作输送到同一连接。  应用程序将对事务速度和资源开销有利。  当应用程序使用传输层加密（例如 TLS）时，处理新连接的开销会很大。  有关其他最佳做法模式，请查看 [Azure 云设计模式](https://docs.microsoft.com/azure/architecture/patterns/)。

#### <a name="possible-mitigations"></a>可能的缓解措施

_**解决方法：**_ 按如下所述缩放出站连接：

| 场景 | 证据 |缓解操作 |
|---|---|---|
| 在使用高峰期遇到 SNAT 端口争用和 SNAT 端口耗尽的情况。 | Azure Monitor 中“SNAT 连接”[指标](nat-metrics.md)的“失败”类别显示一段时间内的暂时性或持续性故障，以及高连接量。  | 确定是否可以添加更多的公共 IP 地址资源或公共 IP 前缀资源。 添加这些资源可让 NAT 网关总共最多获得 16 个 IP 地址。 这种做法将为可用 SNAT 端口（每个 IP 地址 64,000 个端口）提供更多库存，并让你进一步缩放方案。|
| 已获得 16 个 IP 地址，但仍遇到 SNAT 端口耗尽的问题。 | 尝试添加更多 IP 地址失败。 公共 IP 地址资源或公共 IP 前缀资源的 IP 地址总数超过了 16 个。 | 跨多个子网分布应用程序环境，并为每个子网提供一个 NAT 网关资源。  重新评估设计模式，根据前面的[指导](#design-patterns)进行优化。 |

>[!NOTE]
>必须了解发生 SNAT 耗尽的原因。 确保为可缩放、可靠的方案使用适当的模式。  只有在万不得已时，才能在不了解需求原因的情况下将更多 SNAT 端口添加到方案。 如果你不知道方案为何给 SNAT 端口库存施加压力，通过添加更多 IP 地址将更多 SNAT 端口添加到库存，只能在应用程序缩放时延缓相同的资源耗尽问题。  其他低效的做法和对立模式可能会被掩盖。

### <a name="icmp-ping-is-failing"></a>ICMP ping 失败

[虚拟网络 NAT](nat-overview.md) 支持 IPv4 UDP 和 TCP 协议。 ICMP 不受支持，预期将会失败。  

_**解决方法：**_ 请改用 TCP 连接测试（例如“TCP ping”）和 UDP 特定的应用层测试来验证端到端的连接。

可以使用下表作为起点，来确定要使用哪些工具开始测试。

| 操作系统 | 常规 TCP 连接测试 | TCP 应用层测试 | UDP |
|---|---|---|---|
| Linux | nc（常规连接测试） | curl（TCP 应用层测试） | 特定于应用程序 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 特定于应用程序 |

### <a name="connectivity-failures"></a>连接失败

[虚拟网络 NAT](nat-overview.md) 的连接问题可能是由多个不同的因素造成的：

* NAT 网关出现暂时性或持续性的 [SNAT 耗尽](#snat-exhaustion)；
* Azure 基础结构出现暂时性故障； 
* Azure 与公共 Internet 目标之间的路径出现暂时性故障； 
* 公共 Internet 目标出现暂时性或持续性故障。

使用如下所述的工具来验证连接。 [不支持 ICMP ping](#icmp-ping-is-failing)。

| 操作系统 | 常规 TCP 连接测试 | TCP 应用层测试 | UDP |
|---|---|---|---|
| Linux | nc（常规连接测试） | curl（TCP 应用层测试） | 特定于应用程序 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 特定于应用程序 |

#### <a name="snat-exhaustion"></a>SNAT 耗尽

请参阅本文中有关 [SNAT 耗尽](#snat-exhaustion)的部分。

#### <a name="azure-infrastructure"></a>Azure 基础结构

尽管 Azure 会非常严谨地监视和运行其基础结构，但暂时性故障仍会不时发生，因为无法保证传输内容不会丢失。  使用允许 TCP 应用程序进行 SYN 重传的设计模式。 使用足够大的连接超时，以允许 TCP SYN 重传，从而减轻 SYN 丢包造成的暂时性影响。

_**解决方法：**_

* 查看 [SNAT 耗尽](#snat-exhaustion)。
* TCP 堆栈中用于控制 SYN 重传行为的配置参数名为 RTO（[重传超时](https://tools.ietf.org/html/rfc793)）。 RTO 值可调整，通常默认为 1 秒或更高，支持指数退让。  如果应用程序的连接超时过短（例如 1 秒），可能会出现偶发性的连接超时。  增大应用程序连接超时。
* 如果在使用默认应用程序行为时观察到长时间的意外超时，请提出支持案例，让我们做进一步的故障排除。

我们不建议人为减小 TCP 连接超时或优化 RTO 参数。

#### <a name="public-internet-transit"></a>公共 Internet 传输

如果通往目标的路径较长且存在较多的中间系统，则出现暂时性故障的概率会增大。 通过 [Azure 基础结构](#azure-infrastructure)传输时，出现暂时性故障的频率预期会增大。 

请遵循前面的 [Azure 基础结构](#azure-infrastructure)部分中的相同指导。

#### <a name="internet-endpoint"></a>Internet 终结点

除了与用于建立通信的 Internet 终结点相关的注意事项以外，还可以遵循前面部分中的指导。 可能影响连接成功的其他因素为：

* 目标端的流量管理，包括
- 目标端施加的 API 速率限制
- 巨流量 DDoS 攻击缓解措施或传输层流量造型
* 目标上的防火墙或其他组件 

通常，在源和目标（如果适用）上进行数据包捕获需要确定当前发生的情况。

_**解决方法：**_

* 查看 [SNAT 耗尽](#snat-exhaustion)。 
* 验证与同一区域或其他位置的终结点的连接，以进行比较。  
* 如果要创建高流量或事务速率测试，请观察降低速率是否会减少故障的发生。
* 如果更改速率会影响故障率，请检查是否已达到目标端上的 API 速率限制或其他约束。
* 如果调查没有结论，请提出支持案例，让我们做进一步的故障排除。

#### <a name="tcp-resets-received"></a>收到 TCP 重置

如果发现源 VM 上收到 TCP 重置（TCP RST 数据包），这些响应可能是在处理过程中未识别到的流的专用端上的 NAT 网关生成的。  一个可能的原因是 TCP 连接发生空闲超时。可将空闲超时从 4 分钟调整为最长 120 分钟。

NAT 网关资源的公共端不会生成 TCP 重置。 如果目标端收到 TCP 重置，它们是由源 VM 的堆栈生成的，而不是 NAT 网关资源生成的。

_**解决方法：**_

* 查看[设计模式](#design-patterns)建议。  
* 如有必要，请提出支持案例，让我们做进一步的故障排除。

### <a name="ipv6-coexistence"></a>IPv6 共存

[虚拟网络 NAT](nat-overview.md) 支持 IPv4 UDP 和 TCP 协议，[不支持在使用 IPv6 前缀的子网](nat-overview.md#limitations)中进行部署。

_**解决方法：**_ 在不使用 IPv6 前缀的子网中部署 NAT 网关。

可以通过[虚拟网络 NAT UserVoice](https://aka.ms/natuservoice) 来表明对其他功能的兴趣。

## <a name="next-steps"></a>后续步骤

* 了解[虚拟网络 NAT](nat-overview.md)。
* 了解 [NAT 网关资源](nat-gateway-resource.md)
* 了解 [NAT 网关资源的指标和警报](nat-metrics.md)。
* [在 UserVoice 中告诉我们接下来想要为虚拟网络 NAT 开发什么功能](https://aka.ms/natuservoice)。

