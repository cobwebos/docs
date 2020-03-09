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
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302978"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>排查 Azure 虚拟网络 NAT 连接问题

本文帮助管理员诊断和解决在使用虚拟网络 NAT 时出现的连接问题。

>[!NOTE] 
>虚拟网络 NAT 暂时以公共预览版提供。 目前它只能在有限的几个[区域](nat-overview.md#region-availability)中使用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="problems"></a>问题

- [SNAT 耗尽](#snat-exhaustion)。
- [ICMP ping 失败](#icmp-ping-is-failing)。

若要解决这些问题，请遵循以下部分中的步骤。

## <a name="resolution"></a>解决方法

### <a name="snat-exhaustion"></a>SNAT 耗尽

一个 [NAT 网关资源](nat-gateway-resource.md)支持 64,000 到 100 万个并发流。  每个 IP 地址为可用库存提供 64,000 个 SNAT 端口。 对于每个 NAT 网关资源，最多可以使用 16 个 IP 地址。  [此文](nat-gateway-resource.md#source-network-address-translation)更详细地介绍了 SNAT 机制。

#### <a name="steps"></a>步骤：

1. 调查应用程序如何建立出站连接（例如，通过代码评审或数据包捕获进行调查）。 
2. 确定此活动是否为预期行为，或者应用程序是否行为异常。  使用 Azure Monitor 中的[指标](nat-metrics.md)来证实发现结果。
3. 评估是否遵循适当的模式。
4. 评估向 NAT 网关资源分配更多的 IP 地址是否应该可以缓解 SNAT 端口耗尽的问题。

#### <a name="design-pattern"></a>设计模式：

始终尽量利用连接重用和连接池。  此模式可以彻底避免资源耗尽问题，并使行为可预测。 在许多开发库和框架中，都可以找到这些模式的根源。
- 考虑对长时间运行的操作使用[异步轮询模式](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)，以释放连接资源供其他操作使用。
- 生存期较长的流（例如重复使用的 TCP 连接）应使用 TCP Keepalive 或应用层 Keepalive，以避免中间系统超时。
- 应使用正常[重试模式](https://docs.microsoft.com/azure/architecture/patterns/retry)，以避免在发生暂时性故障或故障恢复期间出现频繁重试/突发。
为每个 HTTP 操作创建新 TCP 连接（也称为“原子连接”）是一种对立模式。  原子连接会阻止应用程序正常缩放，并且会浪费资源。  始终通过管道将多个操作输送到同一连接。  应用程序将对事务速度和资源开销有利。  当应用程序使用传输层加密（例如 TLS）时，处理新连接的开销会很大。  有关其他最佳做法模式，请查看 [Azure 云设计模式](https://docs.microsoft.com/azure/architecture/patterns/)。

#### <a name="mitigations"></a>缓解措施

可按如下所述缩放出站连接：

| 场景 | 缓解操作 |
|---|---|
| 在使用高峰期遇到 SNAT 端口争用和 SNAT 端口耗尽的情况。 | 确定是否可以添加更多的公共 IP 地址资源或公共 IP 前缀资源。 添加这些资源可让 NAT 网关总共最多获得 16 个 IP 地址。 这种做法将为可用 SNAT 端口（每个 IP 地址 64,000 个端口）提供更多库存，并让你进一步缩放方案。|
| 已获得 16 个 IP 地址，但仍遇到 SNAT 端口耗尽的问题。 | 跨多个子网分布应用程序环境，并为每个子网提供一个 NAT 网关资源。 |

>[!NOTE]
>必须了解发生 SNAT 消耗的原因。 确保为可缩放、可靠的方案使用适当的模式。  只有在万不得已时，才能在不了解需求原因的情况下将更多 SNAT 端口添加到方案。 如果你不知道方案为何给 SNAT 端口库存施加压力，通过添加更多 IP 地址将更多 SNAT 端口添加到库存，只能在应用程序缩放时延缓相同的资源耗尽问题。  其他低效的做法和对立模式可能会被掩盖。

### <a name="icmp-ping-is-failing"></a>ICMP ping 失败

[虚拟网络 NAT](nat-overview.md) 支持 IPv4 UDP 和 TCP 协议。 ICMP 不受支持，预期将会失败。  请改用 TCP 连接测试（例如“TCP ping”）和 UDP 特定的应用层测试来验证端到端的连接。

可以使用下表作为起点，来确定要使用哪些工具开始测试。

| 操作系统 | 常规 TCP 连接测试 | TCP 应用层测试 | UDP |
|---|---|---|---|
| Linux | nc（常规连接测试） | curl（TCP 应用层测试） | 特定于应用程序 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 特定于应用程序 |

## <a name="next-steps"></a>后续步骤

- 了解[虚拟网络 NAT](nat-overview.md)。
- 了解 [NAT 网关资源](nat-gateway-resource.md)
- 了解 [NAT 网关资源的指标和警报](nat-metrics.md)。
