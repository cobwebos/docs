---
title: "Azure 中的高可用性端口概述 | Microsoft Docs"
description: "了解在内部负载均衡器上进行负载均衡的高可用性端口"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e41c70b982b97c6aab7b6c0322c193c61370a26
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="high-availability-ports-overview"></a>高可用性端口概述

Azure 负载均衡器的标准 SKU 引入了高可用性 (HA) 端口功能 - 能够为所有支持的协议分配来自所有端口的流量。 配置内部负载均衡器时，用户可以配置 HA 端口规则（可将前端和后端端口设置为 **0**，将协议设置为 **all**），从而允许所有流量流经内部负载均衡器。

负载均衡算法仍保持不变，并基于五元组 <源 IP 地址、源端口、目标 IP 地址、目标端口、协议> 选择目标。 但此配置允许单个 LB 规则处理所有可用流量，并减少配置复杂性以及由可以添加的最大负载均衡规则数施加的任何限制。

HA 端口实现的关键方案之一是在 Azure 虚拟网络中进行网络虚拟设备的高可用性部署。 此外，HA 端口实现的另一个常见方案是对某一范围的端口进行负载均衡。 

## <a name="why-use-high-ha-ports"></a>为何使用高 HA 端口

Azure 客户主要依赖于网络虚拟设备 (NVA) 来保护其工作负荷免遭多种类型的安全威胁。 此外，NVA 必须可靠且高度可用。  

HA 端口无需采用更复杂的解决方案（如 zookeeper）从而减少了 NVA HA 方案的复杂性，并通过提供更快的故障转移和横向扩展选项提高了可靠性。 现在可以通过在 Azure 内部负载均衡器的后端池中添加 NVA，然后配置 HA 端口负载均衡器规则来实现 NVA HA。

以下示例显示了一个中心辐射型虚拟网络部署，其中轮辐在离开受信任的空间前，强制通过 NVA 使用隧道技术将流量传送到中心虚拟网络。 NVA 位于具有 HA 端口配置的内部负载均衡器后面，因此可以处理所有流量，并相应地转发。 

![ha 端口示例](./media/load-balancer-ha-ports-overview/nvaha.png)

图 1 - 包含以 HA 模式部署的 NVA 的中心辐射型虚拟网络

## <a name="caveats"></a>注意事项

以下是 HA 端口支持的配置或异常：

- 单个前端 ip 配置可以有包含 HA 端口（所有端口）的单条 DSR 负载均衡器规则，也可以有包含高 HA 端口（所有端口）的单条非 DSR 负载均衡器规则。 它不能同时具有这两者。
- 单个网络接口 IP 配置只能有包含 HA 端口的一条非 DSR 负载均衡器规则。 不能为此 ipconfig 配置任何其他规则。
- 单个网络接口 IP 配置可以有一条或多条包含 HA 端口的 DSR 负载均衡器规则，前提是其所有相应的前端 ip 配置都是唯一的。
- 如果所有负载均衡规则都包含 HA 端口（仅 DSR），或所有规则都包含非 HA 端口（DSR 和非 DSR），则两条（或更多）指向同一后端池的负载均衡器规则可以共存。 如果存在 HA 端口和非 HA 端口规则的组合，这两种 LB 规则不能共存。
- 启用了 IPv6 的租户上的 HA 端口不可用。


## <a name="next-steps"></a>后续步骤

[配置内部负载均衡器上的 HA 端口](load-balancer-configure-ha-ports.md)


