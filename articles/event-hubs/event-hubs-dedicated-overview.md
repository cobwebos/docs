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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138663"
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

Azure 事件中心群集提供单个租户部署具有最苛刻的流式处理需求的客户。 此单租户产品/服务有一个保证的 99.99%的 SLA。 它是仅在专用定价层上可用。

事件中心群集可以引入数以百万计的每秒含有保障的容量和次秒级滞后时间的事件。 命名空间和事件中心创建专用的群集中包括所有功能的标准产品/服务和的详细信息，但不受任何入口限制。 它还包括[事件中心捕获](event-hubs-capture-overview.md)功能时无需额外付费。 您可以使用它自动批处理和日志数据的流到 Azure 存储或 Azure Data Lake。

预配的专用的群集和按容量单位 (Cu) 计费。 Cu 都有预先分配的数量的 CPU 和内存资源。 你可以购买 1、 2、 4、 8、 12、 16 或 20 Cu 为每个群集。 多少可引入，每个 CU 流依赖于的生成者和使用者、 负载形状和出口率数等因素。

有关详细信息，请参阅与基准结果表。

## <a name="why-use-event-hubs-dedicated"></a>为什么要使用专用事件中心？

专用事件中心需要企业级容量的客户提供三个优势。

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>单租户可保证容量更好的性能

专用的群集可保证完整规模的容量。 它可以流入量至多千兆字节为单位的完全持久的存储和次秒级延迟，以适应流量突发状况的流式处理数据。

#### <a name="inclusive-and-exclusive-access-to-features"></a>非独占和独占访问功能 
专用产品/服务包括功能，如捕获且不另外收费。 它还提供对即将推出的功能，如 BYOK 独占访问权限。 该服务还管理负载均衡、 操作系统更新、 安全修补程序和分区。 使用这些功能，可以在基础结构维护和构建客户端功能的更多时间花更少的时间。

#### <a name="cost-savings"></a>成本节约
在高入口量时，在标准产品/服务中购买相当数量的吞吐量单位 (Tu，) 群集远少于每个成本相比的小时。 高卷 > 100 Tu。


## <a name="event-hubs-standard-vs-dedicated"></a>事件中心标准 vs。专用

下表比较了事件中心的各可用服务层级。 事件中心专用产品/服务按标准的大部分功能的使用定价相比固定每月价格收费。 专用的层提供标准计划的但具有企业规模容量，以客户的要求苛刻的工作负荷的所有功能。

| Feature | 标准 | 专用 |
| --- |:---:|:---:|
| 入口事件 | 按每百万个事件支付 | 已含 |
| 吞吐量单位（传入为 1 MB/秒，传出为 2 MB/秒） | 按每小时支付 | 附送 |
| 消息大小 | 1 MB | 1 MB |
| 分区 | 每个命名空间的 40 | 每 CU 2,000 个 |
| 使用者组 | 每个事件中心的 20 | 每个事件中心为 1000 |
| 最大带宽 | 20 Tu （最多 40 Tu) | 20 Cu |
| 中转连接 | 包括 1000 | 包含 100,000 |
| 消息保留期 | 包含的一天 | 包含最多七天 |
| 捕获 | 按每小时支付 | 附送 |

## <a name="what-can-i-achieve-with-a-cluster"></a>使用群集可以获得什么？

对于事件中心群集，多少可以引入和流式传输取决于你生成者、 使用者、 速率的引入和处理，以及更多内容。

下表显示了在测试期间实现的基准测试结果。

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 每秒 400,000 消息数 | 800 MB/秒 | 800000 消息数/秒 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66,600 消息数/秒 | 1.33 GB/秒 | 133,000 消息数/秒 | 666 TU | 166 TU |
| 6x32KB 批 | 第 | 1.05 GB/秒 | 34,000 消息数/秒 | 1.05 GB/秒 | 34,000 消息数/秒 | 1,000 Tu | 250 TU |

在测试中，使用以下条件：

- 使用具有四个容量单位的专用层事件中心群集。
- 用于引入的事件中心包含 200 个分区。
- 两个接收方应用程序收到已引入的数据。 他们接收到来自所有分区的数据。

## <a name="use-event-hubs-dedicated"></a>使用专用事件中心

若要使用专用事件中心，[联系计费支持](https://ms.portal.azure.com/#create/Microsoft.Support)或 Microsoft 代表。 通过添加或删除 CU，可以在一个月内随时扩展或缩小容量，满足自身需求。 事件中心产品团队可帮助你获取的灵活部署，最适合你。

## <a name="next-steps"></a>后续步骤

请联系您的 Microsoft 销售代表或 Microsoft 支持部门以获取有关专用事件中心容量的其他信息。 若要详细了解事件中心定价层，使用以下链接：

- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 你还可以联系你的 Microsoft 销售代表或 Microsoft 支持部门以获取有关专用事件中心容量的其他信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。
