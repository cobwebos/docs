---
title: include 文件
description: include 文件
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1bcadc18172535649a0ceb482939ca6a75477e25
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170722"
---
### <a name="what-is-expressroute-direct"></a>什么是 ExpressRoute Direct？

借助 ExpressRoute Direct，用户可直接连接到巧妙分布在全球对等互连位置的 Microsoft 的全球网络。 ExpressRoute Direct 提供双 100 Gbps 连接，支持大规模的主动/主动连接。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>客户如何连接到 ExpressRoute Direct？ 

客户需要联系他们的本地运营商和主机托管提供商以连接到 ExpressRoute 路由器才能利用 ExpressRoute Direct。

### <a name="what-locations-currently-support-expressroute-direct"></a>哪些位置当前支持 ExpressRoute Direct？ 

可用端口将为动态端口，并且将由 PowerShell 用于查看容量。 位置包括以下区域并且将基于可用性而有所更改：

* 阿姆斯特丹
* 堪培拉
* 芝加哥
* 华盛顿特区
* 达拉斯 
* 香港特别行政区
* 伦敦
* 洛杉矶
* New York City
* 巴黎
* 珀斯
* 多伦多
* San Antonio
* 西雅图
* Seoul
* 硅谷
* 新加坡 
* 悉尼

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct 的 SLA 是什么？

ExpressRoute Direct 将使用与 [ExpressRoute 企业级](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)相同的 SLA。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>在哪些方案中客户应考虑使用 ExpressRoute Direct？  

ExpressRoute Direct 为客户提供到 Microsoft 全球主干线中的直接 100 Gbps 端口对。 将为客户带来最大效益的方案包括：大量数据引入、受管制市场的物理隔离和适用于突发方案（例如呈现）的专用容量。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct 的计费模型是什么？ 

ExpressRoute Direct 将针对端口对按固定金额计费。 标准线路将包含在内（不含额外的小时），高级线路将稍加一些附加费用。 流出量将基于对等互连位置的区域按每条线路进行计费。

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>何时开始计费的？ ExpressRoute 直接端口对

创建 ExpressRoute Direct 资源 45 天后，或启用了一个或两个链接时，ExpressRoute Direct 端口对开始计费，以先到者为准。 我们提供 45 天的宽限期，让客户能够完成与主机托管提供程序的交叉连接过程。
