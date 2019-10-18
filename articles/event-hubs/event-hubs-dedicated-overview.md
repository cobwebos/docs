---
title: 专用事件中心概述 - Azure 事件中心 | Microsoft Docs
description: 本文概述了专用的 Azure 事件中心，该中心提供事件中心的单租户部署。
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516748"
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

*事件中心群集*为具有最苛刻的流式处理需求的客户提供单租户部署。 这项单租户产品/服务保证 SLA 可达 99.99%，只能在专用定价层上提供。 事件中心群集可以每秒传入数百万个事件，并可保证容量和秒的延迟。 在专用群集中创建的命名空间和事件中心包括标准产品/服务的所有功能，但没有任何入口限制。 它还包括常见的[事件中心捕获](event-hubs-capture-overview.md)功能，无需额外付费，使你能够将数据流自动分批传输到 Azure 存储或 Azure Data Lake。 

按**容量单位（cu）** 预配和计费群集，预分配的 CPU 和内存资源量。 可为每个群集购买 1、2、4、8、12、16 或 20 个 CU。 每个 CU 可以引入和流式传输的量取决于各种因素，如制造商和使用者的数量、负载形状和出口率（有关详细信息，请参阅下面的基准测试结果）。 

> [!NOTE]
> 默认情况下，所有事件中心群集都是 Kafka 启用的，并且支持现有的基于 Kafka 的应用程序可使用的 Kafka 终结点。 群集上启用了 Kafka 不会影响非 Kafka 用例;不需要在群集上禁用 Kafka。

## <a name="why-dedicated"></a>为什么是专用？

专用事件中心为需要企业级容量的客户提供三个诱人的优势：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>单租户保证容量以获得更好的性能

专用群集保证容量完全可扩展，并使用完全持久的存储和秒的延迟进入多达千兆字节的流数据，以适应流量的突发。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>对功能的非独占和独占访问 
专用产品/服务包括无需额外付费的捕获功能，以及对即将推出的功能（如创建自己的密钥（BYOK））的独占访问权限。 该服务还为客户管理负载均衡、操作系统更新、安全修补程序和分区，以便您可以花费更少的时间来维护基础结构，以及生成客户端功能的时间更长。  

#### <a name="cost-savings"></a>节约成本
在较高的入口卷（> 100 Tu）上，群集的成本要比在标准产品中购买可比较数量的吞吐量单位要少得多。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中心专用层配额和限制

事件中心专用层产品按固定的每月价格计费，最少使用4小时。 专用层提供标准计划的所有功能，但具有企业规模容量和对具有要求的工作负荷的客户的限制。 

| Feature | 标准版 | 专用 |
| --- |:---:|:---:|
| 带宽 | 20 Tu （最多 40 Tu） | 20 Cu |
| 命名空间 |  第 | 每 CU 50 |
| 事件中心 |  每个命名空间10个 | 每个命名空间1000 |
| 入口事件 | 按每百万个事件支付 | 包含 |
| 消息大小 | 1000000字节 | 1000000字节 |
| 分区 | 每个事件中心32 | 每个事件中心1024 |
| 消费者群体 | 每个事件中心20个 | 对于每个 CU，每个事件中心无限制，1000 |
| 中转连接 | 包括1000，最大5000 | 包括 100 K 和 max |
| 消息保留期 | 7天，每 TU 包含 84 GB | 90天，每个 CU 包含 10 TB |
| 捕获 | 按每小时支付 | 包含 |

## <a name="how-to-onboard"></a>如何加入

通过[Azure 门户](https://aka.ms/eventhubsclusterquickstart)[创建事件中心群集](event-hubs-dedicated-cluster-create-portal.md)的自助服务体验现在处于预览阶段。 如果你有任何疑问或需要事件中心专用层的帮助加入，请联系[事件中心团队](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常见问题解答

#### <a name="what-can-i-achieve-with-a-cluster"></a>使用群集可以实现哪些功能？

对于事件中心群集，可以引入和流式传输多少取决于不同的因素，例如，你的发生器、消费者、你引入和处理的速度等。 

下表显示了我们在测试期间实现的基准结果：

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 每秒的400k 消息数 | 800 MB/秒 | 每秒的800k 消息数 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66.6 k 条消息数/秒 | 1.33 GB/秒 | 每秒的133k 消息数 | 666 TU | 166 TU |
| 6x32KB 批 | 第 | 1.05 GB/秒 | 每秒的34k 消息数 | 1.05 GB/秒 | 每秒的34k 消息数 | 1000 TU | 250 TU |

测试中使用了以下条件：

- 使用了具有四个容量单位（Cu）的专用层事件中心群集。 
- 用于引入的事件中心包含 200 个分区。 
- 引入的数据由从所有分区接收数据的两个接收方应用程序接收。

#### <a name="can-i-scale-updown-my-cluster"></a>是否可以扩展/缩减群集？

创建后，群集将按最少4小时的使用量计费。 在自助服务体验的预览版本中，你可以向事件中心团队提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)，并在*技术 > 配额 > 请求中纵向扩展或缩减专用群集*，使群集扩大或缩小。 完成向下缩放群集的请求可能需要最多7天的时间。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>异地灾难恢复如何使用我的群集？

你可以使用专用层群集下的其他命名空间在专用层群集下对命名空间进行地域配对。 我们不鼓励将专用层命名空间与标准产品中的命名空间配对，因为吞吐量限制将会导致错误。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>是否可以将标准命名空间迁移到属于专用层群集？
目前，我们不支持将事件中心数据从标准命名空间迁移到专用命名空间的自动迁移过程。 

## <a name="next-steps"></a>后续步骤

请联系你的 Microsoft 销售代表或 Microsoft 支持部门获取有关事件中心专用层的更多详细信息。 还可以通过访问以下链接来创建群集或了解有关事件中心定价层的详细信息：

- [通过 Azure 门户创建事件中心群集](https://aka.ms/eventhubsclusterquickstart) 
- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 还可以联系 Microsoft 销售代表或 Microsoft 支持部门，获取关于专用事件中心容量的其他详细信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。
