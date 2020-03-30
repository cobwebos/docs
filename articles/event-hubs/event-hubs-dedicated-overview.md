---
title: 专用事件中心概述 - Azure 事件中心 | Microsoft Docs
description: 本文概述专用 Azure 事件中心，它提供事件中心的单租户部署。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516748"
---
# <a name="overview-of-event-hubs-dedicated"></a>专用事件中心概述

*事件中心群集*为具有最苛刻的流式处理需求客户提供单租户部署。 此单租户产品具有 99.99% 的保证 SLA，仅在我们的专用定价层提供。 事件中心群集每秒可以侵入数百万个事件，并且有保证的容量和次秒的延迟。 在专用群集中创建的命名空间和事件中心包括标准产品的所有功能等，但没有任何入口限制。 它还包括流行的[事件中心捕获](event-hubs-capture-overview.md)功能，无需额外费用，允许您自动批处理和记录数据流到 Azure 存储或 Azure 数据湖。 

群集由**容量单位 （CUS）** 进行预配和计费，容量单位是预分配的 CPU 和内存资源量。 可为每个群集购买 1、2、4、8、12、16 或 20 个 CU。 每个 CU 可以引入和流式传输多少取决于多种因素，例如生产者和消费者的数量、有效负载形状、输出率（有关详细信息，请参阅下面的基准结果）。 

> [!NOTE]
> 默认情况下，所有事件中心群集都启用卡夫卡，并支持卡夫卡终结点，这些终结点可用于现有的基于卡夫卡的应用程序。 在群集上启用 Kafka 不会影响非卡夫卡用例;因此，在群集上启用 Kafka 不会影响您的非 Kafka 用例。没有选项或需要禁用群集上的 Kafka。

## <a name="why-dedicated"></a>为什么专用？

专用活动中心为需要企业级容量的客户提供了三个引人注目的优势：

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>单租户保证容量，提高性能

专用群集可确保全量级容量，并且可以以完全持久的存储和次秒延迟进入高达千兆字节的流数据，以适应任何突发流量。 

#### <a name="inclusive-and-exclusive-access-to-features"></a>对功能的包容性和独占性访问 
专用产品包括免费捕获等功能，以及对即将推出的功能（如自带密钥 （BYOK） 的独占访问权限。 该服务还为客户管理负载平衡、操作系统更新、安全修补程序和分区，以便您可以在基础结构维护上花费更少的时间，在构建客户端功能上花费更多时间。  

#### <a name="cost-savings"></a>成本节约
在高入口容量（>100 个图量）下，群集每小时的成本明显低于标准产品/地区购买可比吞吐量单位数量的成本。


## <a name="event-hubs-dedicated-quotas-and-limits"></a>事件中心专用配额和限制

事件中心专用层产品/服务按固定的月度价格计费，至少使用 4 个小时。 专用层提供标准计划的所有功能，但具有企业规模容量和限制，以满足客户的工作负荷需求。 

| Feature | Standard | 专用 |
| --- |:---:|:---:|
| 带宽 | 20 TU（最多 40 TU） | 20 CU |
| 命名空间 |  1 | 每个 CU 50 |
| 事件中心 |  每个命名空间 10 | 每个命名空间 1000 |
| 入口事件 | 按每百万个事件支付 | 附送 |
| 消息大小 | 1000000 字节 | 1000000 字节 |
| “度量值组” | 每个事件中心 32 个 | 每个事件中心 1024 个 |
| 使用者组 | 每个事件中心 20 | 每个 CU 无限制，每个事件中心 1000 |
| 中转连接 | 包括 1,000 个，最大 5,000 个 | 包括 100000 个，最大 100000 个 |
| 消息保留 | 7 天，每个 TU 包含 84 GB | 90 天，每个 CU 包含 10 TB |
| 捕获 | 按每小时支付 | 附送 |

## <a name="how-to-onboard"></a>如何加入

通过[Azure 门户](https://aka.ms/eventhubsclusterquickstart)[创建事件中心群集](event-hubs-dedicated-cluster-create-portal.md)的自助服务体验现在处于预览状态。 如果您有任何问题或需要帮助载入活动中心专用，请联系[活动中心团队](mailto:askeventhubs@microsoft.com)。

## <a name="faqs"></a>常见问题解答

#### <a name="what-can-i-achieve-with-a-cluster"></a>使用群集可以实现什么？

对于事件中心群集，可以引入和流式传输多少取决于各种因素，例如生产者、使用者、摄入和处理的速度等等。 

下表显示了我们在测试期间实现的基准结果：

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 400k 消息数/秒 | 800 MB/秒 | 800k 消息数/秒 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66.6k 消息数/秒 | 1.33 GB/秒 | 133k 消息数/秒 | 666 TU | 166 TU |
| 6x32KB 批 | 1 | 1.05 GB/秒 | 34k 消息数/秒 | 1.05 GB/秒 | 34k 消息数/秒 | 1000 TU | 250 TU |

测试中使用了以下条件：

- 使用了具有四个容量单元 （CUS） 的专用层事件中心群集。 
- 用于引入的事件中心包含 200 个分区。 
- 引入的数据由从所有分区接收数据的两个接收方应用程序接收。

#### <a name="can-i-scale-updown-my-cluster"></a>我可以向上/缩小群集吗？

创建后，群集将至少收取 4 小时的使用量。 在自助服务体验的预览版本中，您可以在*技术>配额>请求向上扩展或缩小专用群集以*向上或向下扩展群集的情况下向事件中心团队提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)。 可能需要长达 7 天的时间才能完成缩减群集的请求。 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Geo-DR 如何使用我的群集？

可以将专用层群集下的命名空间与专用层群集下的另一个命名空间进行地理配对。 我们不鼓励在标准产品中将专用层命名空间与命名空间配对，因为吞吐量限制将不兼容，从而导致错误。 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>是否可以将标准命名空间迁移到专用层群集？
我们目前不支持自动迁移过程，用于将事件中心数据从标准命名空间迁移到专用命名空间。 

## <a name="next-steps"></a>后续步骤

请与您的 Microsoft 销售代表或 Microsoft 支持部门联系，了解有关活动中心专用的其他详细信息。 您还可以通过访问以下链接创建群集或了解有关事件中心定价层的更多情况：

- [通过 Azure 门户创建事件中心群集](https://aka.ms/eventhubsclusterquickstart) 
- [专用事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。 还可以联系 Microsoft 销售代表或 Microsoft 支持部门，获取关于专用事件中心容量的其他详细信息。
- [事件中心常见问题解答](event-hubs-faq.md)中包含了定价信息并解答了一些有关事件中心的常见问题。
