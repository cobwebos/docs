---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172903"
---
必须认识到有两种方法可以在 Azure 中配置可用性组侦听器。 这些方法在创建侦听器时使用的 Azure 负载均衡器的类型方面有所不同。 下表描述了差异：

| 负载均衡器类型 | 实现 | 使用条件如下： |
| --- | --- | --- |
| **外部** |使用托管虚拟机 (VM) 的云服务的“公用虚拟 IP 地址”。  |需要从虚拟网络外部访问侦听器，包括从 Internet 访问。 |
| **内部** |使用“内部负载均衡器”，其地址专用于侦听器。  |只能从同一个虚拟网络访问侦听器。 该访问包括混合方案中的站点到站点 VPN。 |

> [!IMPORTANT]
> 对于使用云服务公共 VIP（外部负载均衡器）的侦听器，只要客户端、侦听器和数据库位于同一个 Azure 区域中，就不会产生传出费用。 否则，通过侦听程序返回的任何数据将被视为传出数据，因此需要支付正常的数据传输费。 
> 
> 

ILB 只能在具有区域范围的虚拟网络上配置。 已配置关联组的现有虚拟网络无法使用 ILB。 有关详细信息，请参阅[内部负载均衡器概述](../articles/load-balancer/load-balancer-internal-overview.md)。

