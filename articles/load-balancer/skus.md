---
title: Azure 负载均衡器 SKU
description: Azure 负载均衡器 SKU 概述
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: faf77411abca63516b00ac953bc7203da69f3ca9
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854085"
---
# <a name="azure-load-balancer-skus"></a>Azure 负载均衡器 SKU

Azure 负载均衡器有两种风格或 SKU。

## <a name="sku-comparison"></a><a name="skus"></a> SKU 比较

负载均衡器支持基本和标准 SKU。 这些 SKU 在场景规模、功能和定价方面有差异。 使用基本负载均衡器可以实现的任何方案都可以使用标准负载均衡器来创建。

请参阅下表来比较和了解差别。 有关详细信息，请参阅 [Azure 标准负载均衡器概述](load-balancer-standard-overview.md)。

>[!NOTE]
> Microsoft 建议使用标准负载均衡器。
独立 VM、可用性集和虚拟机规模集只能连接到一个 SKU，永远无法同时连接到两个 SKU。 将负载均衡器与公共 IP 地址配合使用时，负载均衡器与公共 IP 地址 SKU 必须匹配。 负载均衡器和公共 IP SKU 不可变。

| | 标准负载均衡器 | 基本负载均衡器 |
| --- | --- | --- |
| [后端池大小](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | 最多支持 1000 个实例。 | 最多支持 300 个实例。 |
| 后端池终结点 | 单个虚拟网络中的任何虚拟机或虚拟机规模集。 | 单个可用性集或虚拟机规模集中的虚拟机。 |
| [运行状况探测](./load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [运行状况探测停止行为](./load-balancer-custom-probe-overview.md#probedown) | TCP 连接在实例探测停止时以及在所有探测停止时保持活动状态  。 | TCP 连接在实例探测停止时保持活动状态。 所有 TCP 连接在所有探测停止时都会终止。 |
| 可用性区域 | 用于入站和出站流量的区域冗余和区域前端。 | 不可用 |
| 诊断 | [Azure Monitor 多维指标](./load-balancer-standard-diagnostics.md) | [Azure Monitor 日志](./load-balancer-monitor-log.md) |
| HA 端口 | [可用于内部负载均衡器](./load-balancer-ha-ports-overview.md) | 不可用 |
| 默认保护 | 除非网络安全组允许，否则对入站流关闭。 请注意，允许从 VNet 到内部负载均衡器的内部流量。 | 默认处于打开状态。 可选的网络安全组。 |
| 出站规则 | [声明性出站 NAT 配置](./load-balancer-outbound-rules-overview.md) | 不可用 |
| 空闲时 TCP 重置 | [可用于任何规则](./load-balancer-tcp-reset.md) | 不可用 |
| [多个前端](./load-balancer-multivip-overview.md) | 入站和[出站](./load-balancer-outbound-connections.md) | 仅限入站 |
| 管理操作 | 大多数操作都小于 30 秒 | 通常为 60 - 90 多秒 |
| SLA | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 不可用 | 

有关详细信息，请参阅[负载均衡器限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)。 对于标准负载均衡器，请参阅[概述](load-balancer-standard-overview.md)、[定价](https://aka.ms/lbpricing)和 [SLA](https://aka.ms/lbsla)。

## <a name="limitations"></a>限制

- SKU 是不可变的。 你不能更改现有资源的 SKU。
- 独立的虚拟机资源、可用性集资源或虚拟机规模集资源可以引用一个 SKU，绝不能同时引用两个。
- 标准负载均衡器和标准公共 IP 资源不支持[移动订阅操作](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

## <a name="next-steps"></a>后续步骤

- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)，开始使用负载均衡器。
- 了解如何使用[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 了解如何[对出站连接使用负载均衡器](load-balancer-outbound-connections.md)。
- 了解[具有 HA 端口负载均衡规则的标准负载均衡器](load-balancer-ha-ports-overview.md)。
- 详细了解[网络安全组](../virtual-network/security-overview.md)。
