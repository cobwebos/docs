---
title: Azure 虚拟网络 NAT 的指标和警报
titleSuffix: Azure Virtual Network
description: 了解 Azure 虚拟网络 NAT 可用的 Azure Monitor 指标和警报。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 51713db8532eb33f102de9320dea9eaeb98c4019
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359083"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure 虚拟网络 NAT 指标

Azure 虚拟网络 NAT 网关资源提供多维指标。 可以使用这些指标来观察操作以及进行[故障排除](nat-metrics.md)。  可以针对严重问题（例如 SNAT 耗尽）配置警报。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="用于出站 Internet 连接的虚拟网络 NAT">
</p>

图：*用于出站 Internet 连接的虚拟网络 NAT*

>[!NOTE] 
>虚拟网络 NAT 暂时以公共预览版提供。 目前它只能在有限的几个[区域](nat-overview.md#region-availability)中使用。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="metrics"></a>指标

NAT 网关资源在 Azure Monitor 中提供以下多维指标：

| 指标 | 说明 | 建议的聚合 | 维度 |
|---|---|---|---|
| 字节 | 处理的入站和出站字节数 | SUM | 方向（传入；传出），协议（6 TCP；17 UDP） |
| 数据包数 | 处理的入站和出站数据包数 | SUM | 方向（传入；传出），协议（6 TCP；17 UDP） |
| 丢弃的数据包数 | NAT 网关丢弃的数据包数 | SUM | / |
| SNAT 连接计数 | 每个间隔的状态转换次数 | SUM | 连接状态，协议（6 TCP；17 UDP） |
| SNAT 连接总数 | 当前的活动 SNAT 连接数（使用的大约 SNAT 端口数） | SUM | 协议（6 TCP；17 UDP） |


## <a name="alerts"></a>警报

可以在 Azure Monitor 中针对上述每个[指标](#metrics)配置警报。

## <a name="limitations"></a>限制

不支持资源运行状况。

## <a name="next-steps"></a>后续步骤

* 了解[虚拟网络 NAT](nat-overview.md)。
* 了解 [NAT 网关资源](nat-gateway-resource.md)
* 了解 [Azure Monitor](../azure-monitor/overview.md)
* 了解如何[排查 NAT 网关资源问题](troubleshoot-nat.md)。
* [在 UserVoice 中告诉我们下一步为虚拟网络 NAT 构建什么](https://aka.ms/natuservoice)。


