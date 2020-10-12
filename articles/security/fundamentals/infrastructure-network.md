---
title: Azure 网络体系结构
description: 本文提供有关 Microsoft Azure 基础结构网络的一般说明。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567855"
---
# <a name="azure-network-architecture"></a>Azure 网络体系结构
Azure 网络体系结构提供从 Internet 到 Azure 数据中心的连接。 在 Azure 上部署 (IaaS、PaaS 和 SaaS) 的任何工作负荷都利用了 Azure 数据中心网络。

## <a name="network-topology"></a>网络拓扑
Azure 数据中心的网络体系结构由以下组件组成：

- 边缘网络
- 广域网络
- 区域网关网络
- 数据中心网络

![Azure 网络示意图](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>网络组件
网络组件的简短说明。

- 边缘网络

   - Microsoft 网络和其他网络之间的分界点 (例如，Internet、企业网络) 
   - 向 Azure 提供 Internet 和 [ExpressRoute](../../expressroute/expressroute-introduction.md) 对等互连

- 广域网络

   - 全球范围内的 Microsoft 智能主干网络
   - 提供[Azure 区域](https://azure.microsoft.com/global-infrastructure/geographies/)之间的连接

- 区域网关

   - Azure 区域中所有数据中心的聚合点
   - 在 Azure 区域内的数据中心之间提供大规模连接 (例如，每个数据中心有几百个 terabits) 

- 数据中心网络

   - 提供数据中心内服务器之间的连接，具有较低的超额订阅带宽

上述网络组件旨在提供最大的可用性，以支持始终可用的、始终可用的云业务。 从物理方面一直到控制协议，将冗余设计并内置到网络中。

## <a name="datacenter-network-resiliency"></a>数据中心网络复原
让我们使用数据中心网络展示复原设计原则。

数据中心网络是 [Clos 网络](https://en.wikipedia.org/wiki/Clos_network)的修改版本，可为云规模流量提供较高的 bi 版本。 网络是使用大量商用设备构造的，以降低单个硬件故障造成的影响。 这些设备在不同的物理位置上战略定位在不同的物理位置，从而降低了环境事件的影响。  在控制平面上，所有网络设备都作为 OSI 模型第3层路由模式运行，从而消除了流量循环的历史问题。 不同层之间的所有路径都处于活动状态，以便使用 Equal-Cost 多路径 (ECMP) 路由来提供高冗余和带宽。

下图演示了数据中心网络由不同的网络设备层构造。 该图中的条形表示提供冗余和高带宽连接的网络设备组。

![数据中心网络](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何帮助保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](physical-security.md)
- [Azure 基础结构可用性](infrastructure-availability.md)
- [Azure 信息系统的组件和边界](infrastructure-components.md)
- [Azure 生产网络](production-network.md)
- [Azure SQL 数据库安全功能](infrastructure-sql.md)
- [Azure 生产运营和管理](infrastructure-operations.md)
- [Azure 基础结构监视](infrastructure-monitoring.md)
- [Azure 基础结构完整性](infrastructure-integrity.md)
- [Azure 客户数据保护](protection-customer-data.md)
