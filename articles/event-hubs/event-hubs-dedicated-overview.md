---
title: 专用事件中心概述 - Azure 事件中心 | Microsoft Docs
description: 本文提供专用 Azure 事件中心，提供单个租户部署的事件中心的概述。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708006"
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

*事件中心群集*提供单个租户部署具有最苛刻的流式处理需求的客户。 此单租户产品/服务具有一个保证的 99.99%的 SLA，仅在我们专用定价层上可用。 事件中心群集可以引入数以百万计的每秒含有保障的容量和次秒级滞后时间的事件。 命名空间和事件中心创建专用的群集中包括所有功能的标准产品/服务和的详细信息，但不受任何入口限制。 它还包括热门[事件中心捕获](event-hubs-capture-overview.md)功能时无需额外付费，从而可以自动批处理和日志数据流到 Azure 存储或 Azure Data Lake。 

预配的专用的群集和按计费**容量单位 (Cu)**，有预分配的数量的 CPU 和内存资源。 你可以购买的每个分类的 1、 2、 4、 8、 12、 16 或 20 个 Cu。 多少可以引入和流式处理每个 CU 取决于多种因素，例如生产者和使用者，有效负载形状出口数速率 （请参阅下面有关更多详细信息的基准校验结果）。 

## <a name="why-dedicated"></a>为什么专用？

专用的事件中心需要企业级容量的客户提供三个极具吸引力的优势：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>单租户可保证容量更好的性能

专用的群集可保证完整规模的容量，可以在流量突增流入量至多千兆字节为单位的完全持久的存储和次秒延迟，以适应任何流式处理数据。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>非独占和独占访问功能 
专用产品/服务包括功能，例如捕获在没有额外的费用，以及对即将推出的功能，如 BYOK 独占访问权限。 该服务还管理负载均衡、 操作系统更新、 安全修补程序和分区的客户，以便您可以花费更少时间基础结构维护有更多时间创建客户端的功能。  

#### <a name="cost-savings"></a>节约的成本
在高入口量 (> 100 Tu)，群集成本显著小于每小时购买相当数量的标准产品/服务的吞吐量单位。


## <a name="event-hubs-standard-vs-dedicated"></a>事件中心标准 vs。专用

下表比较了事件中心的各可用服务层级。 专用事件中心产品/服务按标准的大部分功能的使用定价相比固定每月价格收费。 专用层提供标准计划的所有功能，但具有企业规模容量，以满足客户的工作负荷需求。 

| Feature | 标准 | 专用 |
| --- |:---:|:---:|
| 入口事件 | 按每百万个事件支付 | 已含 |
| 吞吐量单位（传入为 1 MB/秒，传出为 2 MB/秒） | 按每小时支付 | 附送 |
| 消息大小 | 1 MB | 1 MB |
| 分区 | 每个命名空间的 40 | 每个 CU 2000 |
| 使用者组 | 每个事件中心的 20 | 每个事件中心的 1000 |
| 最大 带宽 | 20 Tu （最多 40 Tu)    | 20 Cu |
| 中转连接 | 包括 1000 | 包括 100 K |
| 消息保留 | 包括 1 天 | 包括最长 7 天 |
| 捕获 | 按每小时支付 | 附送 |

## <a name="what-can-i-achieve-with-a-cluster"></a>使用群集可以获得什么？

与事件中心的群集，多少可以引入和流式处理依赖于各种因素，例如在生成者、 使用者、 您是引入和处理的速率和更多。 

下表显示了我们在测试期间实现的基准结果：

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 400K 消息/秒 | 800 MB/秒 | 800K 消息/秒 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66.6K 消息/秒 | 1.33 GB/秒 | 133K 消息/秒 | 666 TU | 166 TU |
| 6x32KB 批 | 第 | 1.05 GB/秒 | 34K 消息/秒 | 1.05 GB/秒 | 34K 消息/秒 | 1000 TU | 250 TU |

测试中使用了以下条件：

- 使用具有四个容量单位 (Cu) 的专用层事件中心群集。 
- 用于引入的事件中心包含 200 个分区。 
- 引入的数据由从所有分区接收数据的两个接收方应用程序接收。

## <a name="how-to-onboard"></a>如何加入

若要加入到此 SKU，请[联系计费支持](https://ms.portal.azure.com/#create/Microsoft.Support)或 Microsoft 代表。 通过添加或删除 CU，可以在一个月内随时扩展或缩小容量，满足自身需求。 专用计划独一无二，用户可从事件中心产品团队处获得适合自己的灵活部署，提供一种亲身实践操作体验。 

## <a name="next-steps"></a>后续步骤

请与 Microsoft 销售代表或 Microsoft 支持部门联系，以获其他关于事件中心专用容量的详细信息。 还可访问以下链接，了解有关事件中心定价层的详细信息：

- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 还可以联系 Microsoft 销售代表或 Microsoft 支持部门，获取有关专用事件中心容量的其他详细信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。
