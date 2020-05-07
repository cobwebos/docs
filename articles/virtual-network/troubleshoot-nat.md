---
title: 排查 Azure 虚拟网络 NAT 连接问题
titleSuffix: Azure Virtual Network
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
ms.date: 04/28/2020
ms.author: allensu
ms.openlocfilehash: c9b5aaefeb8ab21eed850f5bf291d38981239aab
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508422"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>排查 Azure 虚拟网络 NAT 连接问题

本文帮助管理员诊断和解决在使用虚拟网络 NAT 时出现的连接问题。

## <a name="problems"></a>问题

* [SNAT 耗尽](#snat-exhaustion)
* [ICMP ping 失败](#icmp-ping-is-failing)
* [连接失败](#connectivity-failures)
* [IPv6 共存](#ipv6-coexistence)

若要解决这些问题，请遵循以下部分中的步骤。

## <a name="resolution"></a>解决方法

### <a name="snat-exhaustion"></a>SNAT 耗尽

一个 [NAT 网关资源](nat-gateway-resource.md)支持 64,000 到 100 万个并发流。  每个 IP 地址为可用库存提供 64,000 个 SNAT 端口。 对于每个 NAT 网关资源，最多可以使用 16 个 IP 地址。  [此文](nat-gateway-resource.md#source-network-address-translation)更详细地介绍了 SNAT 机制。

通常，SNAT 耗尽的根本原因是建立和管理出站连接的方式出现了对立模式，或者可配置的计时器已更改，不再使用默认值。  请认真阅读本部分。

#### <a name="steps"></a>步骤

1. 检查是否已将默认空闲超时修改为大于 4 分钟的值。
2. 调查应用程序如何建立出站连接（例如，通过代码评审或数据包捕获进行调查）。 
3. 确定此活动是否为预期行为，或者应用程序是否行为异常。  使用 Azure Monitor 中的[指标](nat-metrics.md)来证实发现结果。 对“SNAT 连接”指标使用“失败”类别。
4. 评估是否遵循适当的模式。
5. 评估向 NAT 网关资源分配更多的 IP 地址是否应该可以缓解 SNAT 端口耗尽的问题。

#### <a name="design-patterns"></a>设计模式

始终尽量利用连接重用和连接池。  这些模式可以避免资源耗尽问题，并使行为可预测。 在许多开发库和框架中，都可以找到这些模式的根源。

_**解决方法：**_ 使用适当的模式和最佳做法

- NAT 网关资源的默认 TCP 空闲超时为 4 分钟。  如果将此设置更改为更大的值，NAT 绑定到流的时间会更长，可能导致[在 SNAT 端口库存方面出现不必要的压力](nat-gateway-resource.md#timers)。
- 原子请求（每个连接一个请求）是一个拙劣的设计选项。 这种对立模式会限制缩放，降低性能并降低可靠性。 应该重复使用 HTTP/S 连接来减少连接数和关联的 SNAT 端口数。 由于使用 TLS 时可以减少握手次数、系统开销以及加密操作的开销，因此应用规模与性能都会提高。
- 如果客户端不缓存 DNS 解析器的结果，DNS 可能会在卷上引入许多单独的流。 使用缓存。
- UDP 流（例如 DNS 查找）根据空闲超时持续时间分配 SNAT 端口。 空闲超时越长，SNAT 端口上的压力越大。 使用较短的空闲超时（例如 4 分钟）。
- 使用连接池来调整连接卷。
- 切勿以静默方式丢弃 TCP 流，且不要依赖 TCP 计时器来清理流。 如果不允许 TCP 显式关闭连接，中间系统和终结点上将保持已分配状态，使 SNAT 端口不可用于其他连接。 这可能会触发应用程序故障和 SNAT 耗尽。 
- 在对造成的影响了解不深的情况下，请不要更改与 OS 级别的 TCP 关闭相关的计时器值。 当某个连接的终结点不符合预期时，尽管 TCP 堆栈会恢复，但应用程序的性能可能会受负面影响。 需要更改计时器往往意味着底层设计出现了问题。 查看以下建议：

底层应用程序中的其他对立模式往往也会使 SNAT 耗尽问题变得严重。 请查看这些附加模式和最佳做法，以改善服务的可伸缩性和可靠性。

- 了解将 [TCP 空闲超时](nat-gateway-resource.md#timers)减小到更小的值（包括默认的 4 分钟空闲超时）来提前释放 SNAT 端口库存所造成的影响。
- 考虑对长时间运行的操作使用[异步轮询模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)，以释放连接资源供其他操作使用。
- 生存期较长的流（例如重复使用的 TCP 连接）应使用 TCP Keepalive 或应用层 Keepalive，以避免中间系统超时。增大空闲超时是最终手段，不一定可以解决根本原因。 较长的超时可以在超时时间过去时降低失败的频率，同时也会造成延迟和不必要的失败。
- 应使用正常[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)，以避免在发生暂时性故障或故障恢复期间出现频繁重试/突发。
为每个 HTTP 操作创建新 TCP 连接（也称为“原子连接”）是一种对立模式。  原子连接会阻止应用程序正常缩放，并且会浪费资源。  始终通过管道将多个操作输送到同一连接。  应用程序将对事务速度和资源开销有利。  当应用程序使用传输层加密（例如 TLS）时，处理新连接的开销会很大。  有关其他最佳做法模式，请查看 [Azure 云设计模式](https://docs.microsoft.com/azure/architecture/patterns/)。

#### <a name="additional-possible-mitigations"></a>其他可能的缓解措施

_**解决方法：**_ 按如下所述缩放出站连接：

| 场景 | 证据 |缓解操作 |
|---|---|---|
| 在使用高峰期遇到 SNAT 端口争用和 SNAT 端口耗尽的情况。 | Azure Monitor 中“SNAT 连接”[指标](nat-metrics.md)的“失败”类别显示一段时间内的暂时性或持续性故障，以及高连接量。  | 确定是否可以添加更多的公共 IP 地址资源或公共 IP 前缀资源。 添加这些资源可让 NAT 网关总共最多获得 16 个 IP 地址。 这种做法将为可用 SNAT 端口（每个 IP 地址 64,000 个端口）提供更多库存，并让你进一步缩放方案。|
| 已获得 16 个 IP 地址，但仍遇到 SNAT 端口耗尽的问题。 | 尝试添加更多 IP 地址失败。 公共 IP 地址资源或公共 IP 前缀资源的 IP 地址总数超过了 16 个。 | 跨多个子网分布应用程序环境，并为每个子网提供一个 NAT 网关资源。  重新评估设计模式，根据前面的[指导](#design-patterns)进行优化。 |

>[!NOTE]
>必须了解发生 SNAT 消耗的原因。 确保为可缩放、可靠的方案使用适当的模式。  只有在万不得已时，才能在不了解需求原因的情况下将更多 SNAT 端口添加到方案。 如果你不知道方案为何给 SNAT 端口库存施加压力，通过添加更多 IP 地址将更多 SNAT 端口添加到库存，只能在应用程序缩放时延缓相同的资源耗尽问题。  其他低效的做法和对立模式可能会被掩盖。

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

* 由于配置错误而导致的永久性故障。
* NAT 网关出现暂时性或持续性的 [SNAT 耗尽](#snat-exhaustion)；
* Azure 基础结构出现暂时性故障； 
* Azure 与公共 Internet 目标之间的路径出现暂时性故障； 
* 公共 Internet 目标出现暂时性或持续性故障。

使用如下所述的工具来验证连接。 [不支持 ICMP ping](#icmp-ping-is-failing)。

| 操作系统 | 常规 TCP 连接测试 | TCP 应用层测试 | UDP |
|---|---|---|---|
| Linux | nc（常规连接测试） | curl（TCP 应用层测试） | 特定于应用程序 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 特定于应用程序 |

#### <a name="configuration"></a>配置

检查以下各项：
1. NAT 网关资源是至少具有一个公共 IP 资源还是一个公共 IP 前缀资源？ 它必须至少具有一个与 NAT 网关关联的 IP 地址，才能提供出站连接。
2. 虚拟网络的子网是否配置为使用 NAT 网关？
3. 是否正在使用 UDR（用户定义的路由）？是否要替代目标？  NAT 网关资源将在配置的子网上成为默认路由 (0/0)。

#### <a name="snat-exhaustion"></a>SNAT 耗尽

请参阅本文中有关 [SNAT 耗尽](#snat-exhaustion)的部分。

#### <a name="azure-infrastructure"></a>Azure 基础结构

Azure 会非常严谨地监视和运行其基础结构。 但暂时性故障仍可能会发生，且无法保证传输内容不会丢失。  使用允许 TCP 应用程序进行 SYN 重传的设计模式。 使用足够大的连接超时，以允许 TCP SYN 重传，从而减轻 SYN 丢包造成的暂时性影响。

_**解决方法：**_

* 查看 [SNAT 耗尽](#snat-exhaustion)。
* TCP 堆栈中用于控制 SYN 重传行为的配置参数名为 RTO（[重传超时](https://tools.ietf.org/html/rfc793)）。 RTO 值可调整，通常默认为 1 秒或更高，支持指数退让。  如果应用程序的连接超时过短（例如 1 秒），可能会出现偶发性的连接超时。  增大应用程序连接超时。
* 如果在使用默认应用程序行为时观察到长时间的意外超时，请提出支持案例，让我们做进一步的故障排除。

我们不建议人为减小 TCP 连接超时或优化 RTO 参数。

#### <a name="public-internet-transit"></a>公共 Internet 传输

如果通往目标的路径较长且存在较多的中间系统，则出现暂时性故障的几率会增大。 通过 [Azure 基础结构](#azure-infrastructure)传输时，出现暂时性故障的频率预期会增大。 

请遵循前面的 [Azure 基础结构](#azure-infrastructure)部分中的相同指导。

#### <a name="internet-endpoint"></a>Internet 终结点

可以运用前面部分中的指导，同时，遵循与用于建立通信的 Internet 终结点相关的注意事项。 可能影响连接成功的其他因素为：

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

NAT 网关在源 VM 上为处理过程中未识别到的流量生成 TCP 重置。

一个可能的原因是 TCP 连接发生空闲超时。可将空闲超时从 4 分钟调整为最长 120 分钟。

不会在 NAT 网关资源的公共端上生成 TCP 重置。 目标端上的 TCP 重置是由源 VM 生成的，而不是由 NAT 网关资源生成的。

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

