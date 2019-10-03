---
title: include 文件
description: include 文件
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 07/09/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 43d1942b1413569d77c6c17fee9fff14dc83c924
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362455"
---
### <a name="what-is-expressroute-direct"></a>什么是 ExpressRoute Direct？

借助 ExpressRoute Direct，用户可直接连接到巧妙分布在全球对等互连位置的 Microsoft 的全球网络。 ExpressRoute Direct 提供双100或 10 Gbps 连接, 支持大规模主动/主动连接。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>客户如何连接到 ExpressRoute Direct？ 

客户需要联系他们的本地运营商和主机托管提供商以连接到 ExpressRoute 路由器才能利用 ExpressRoute Direct。

### <a name="what-locations-currently-support-expressroute-direct"></a>目前, 哪些位置支持 ExpressRoute 直接？ 

可用端口将为动态端口，并且将由 PowerShell 用于查看容量。 位置包括以下区域并且将基于可用性而有所更改：

* 阿姆斯特丹
* 阿姆斯特丹 2
* Auckland 
* 芝加哥
* 达拉斯
* Dublin
* 香港特别行政区
* 伦敦
* 墨尔本
* New York City
* 珀斯
* San Antonio
* 西雅图
* Seoul
* 硅谷
* 新加坡2 
* 悉尼
* 中国台北
* 东京
* 多伦多
* 华盛顿特区
* 华盛顿特区 2

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct 的 SLA 是什么？

ExpressRoute Direct 将使用与 [ExpressRoute 企业级](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)相同的 SLA。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>在哪些方案中客户应考虑使用 ExpressRoute Direct？  

ExpressRoute 直接向客户提供了在 Microsoft 全球主干中直接100或 10 Gbps 端口对。 将为客户带来最大效益的方案包括：大量数据引入、受管制市场的物理隔离和适用于突发方案（例如呈现）的专用容量。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct 的计费模型是什么？ 

ExpressRoute Direct 将针对端口对按固定金额计费。 标准线路将包含在内（不含额外的小时），高级线路将稍加一些附加费用。 流出量将基于对等互连位置的区域按每条线路进行计费。

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>何时开始为 ExpressRoute 直接端口对计费？

创建 ExpressRoute Direct 资源 45 天后，或启用了一个或两个链接时，ExpressRoute Direct 端口对开始计费，以先到者为准。 我们提供 45 天的宽限期，让客户能够完成与主机托管提供程序的交叉连接过程。
