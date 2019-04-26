---
title: 常见问题 - Azure 事件中心 | Microsoft Docs
description: 本文提供了有关 Azure 事件中心的常见问题 (FAQ) 和解答的列表。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d1ed16465efb6c70b4426f22e8b9983112142c79
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162639"
---
# <a name="event-hubs-frequently-asked-questions"></a>事件中心常见问题

## <a name="general"></a>常规

### <a name="what-is-an-event-hubs-namespace"></a>什么是事件中心命名空间？
命名空间是事件中心/Kafka 主题的范围容器。 它提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)。 命名空间充当容装多个事件中心/Kafka 主题的应用程序容器。 

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>事件中心基本和标准这两种服务层有什么不同？

Azure 事件中心标准层提供的功能超出了基本层中提供的功能。 以下功能是标准层附带的：

* 更长的事件保留期
* 其他中转连接，对于超出包含的数量的部分收取超额费用
* 多个[使用者组](event-hubs-features.md#consumer-groups)
* [捕获](event-hubs-capture-overview.md)
* [Kafka 集成](event-hubs-for-kafka-ecosystem-overview.md)

有关定价层的详细信息（包括专用事件中心），请参阅[事件中心定价详细信息](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="where-is-azure-event-hubs-available"></a>Azure 事件中心在哪些区域可用？

在所有支持的 Azure 区域中都可使用 Azure 事件中心。 有关列表，请访问 [Azure 区域](https://azure.microsoft.com/regions/)页。  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>是否可以使用单个 AMQP 连接来与多个事件中心相互收发数据？

可以，但前提是所有事件中心都在同一个命名空间中。

### <a name="what-is-the-maximum-retention-period-for-events"></a>事件的最长保留期有多久？

事件中心标准版目前支持的最长保留期为七天。 事件中心并非永久性的数据存储。 大于 24 小时的保留期适用于将事件流重播到相同系统中的情形；例如，为了基于现有数据来培训或验证新计算机学习模型。 如果需要将消息保留七天以上，请启用事件中心的[事件中心捕获](event-hubs-capture-overview.md)功能，将数据从事件中心提取到所选的存储帐户或 Azure Data Lake 服务帐户。 启用捕获功能需要支付费用，具体因购买的吞吐量单位而异。

### <a name="how-do-i-monitor-my-event-hubs"></a>如何监视事件中心？
事件中心向 [Azure Monitor](../azure-monitor/overview.md) 发出详尽指标用于提供资源的状态。 此外，参考指标不仅可以在命名空间级别，而且还能在实体级别评估事件中心服务的总体运行状况。 了解 [Azure 事件中心](event-hubs-metrics-azure-monitor.md)提供哪些监视功能。

## <a name="apache-kafka-integration"></a>Apache Kafka 集成

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>如何将现有的 Kafka 应用程序与事件中心集成？
事件中心提供可由基于 Apache Kafka 的现有应用程序使用的 Kafka 终结点。 只需完成一项配置更改，即可获得 PaaS Kafka 体验。 使用该体验就如同运行自己的 Kafka 群集。 事件中心支持 Apache Kafka 1.0 和更高版本的客户端，并且适用于现有的 Kafka 应用程序、工具和框架。 有关详细信息，请参阅[适用于 Kafka 的事件中心存储库](https://github.com/Azure/azure-event-hubs-for-kafka)。

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>要使现有的应用程序与事件中心通信，需要完成哪些配置更改？
若要连接到支持 Kafka 的事件中心，需要更新 Kafka 客户端配置。 为此，可以创建事件中心命名空间并获取[连接字符串](event-hubs-get-connection-string.md)。 更改 bootstrap.servers，以将事件中心 FQDN 和端口指向 9093。 更新 sasl.jaas.config，以使用正确的身份验证将 Kafka 客户端定向到支持 Kafka 的事件中心终结点（已获取的连接字符串），如下所示：

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

示例：

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

注意：如果 sasl.jaas.config 不是框架中受支持的配置，请查找用于设置 SASL 用户名和密码的配置，并改为使用这些配置。 将用户名设置为 $ConnectionString，将密码设置为事件中心连接字符串。

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>支持 Kafka 的事件中心的消息/事件大小是多少？
支持 Kafka 的事件中心允许的最大消息大小为 1MB。

## <a name="throughput-units"></a>吞吐量单位

### <a name="what-are-event-hubs-throughput-units"></a>什么是事件中心吞吐量单元？
事件中心内的吞吐量定义通过事件中心传入和传出的数据量，以 MB 或者 1-KB 事件的数目（以千计）为单位。 此吞吐量以吞吐量单位 (TU) 来计量。 在开始使用事件中心服务之前，需购买 TU。 可以使用门户或事件中心资源管理器模板显式选择事件中心 TU。 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>吞吐量单位是否适用于命名空间中的所有事件中心？
是的，吞吐量单位 (TU) 适用于事件中心命名空间中的所有事件中心。 这意味着，TU 是在命名空间级别购买的，并在该命名空间下的事件中心之间共享。 每个 TU 为命名空间赋予以下功能：

- 入口事件（发送到事件中心的事件）最多为每秒 1 MB，但每秒不超过 1000 个入口事件、管理操作或控制 API 调用。
- 出口事件（从事件中心耗用的事件）最多达每秒 2 MB，但不超过 4096 个出口事件。
- 事件存储空间最多为 84 GB（对于默认的 24 小时保留期而言已足够）。

### <a name="how-are-throughput-units-billed"></a>吞吐量单位如何计费？
吞吐量单位 (TU) 按小时计费。 计费基于在给定的小时选择的最大单位数目。 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>如何优化吞吐量单位的使用？
可以从最少的一个吞吐量单位 (TU) 着手，并启用[自动扩充](event-hubs-auto-inflate.md)。 自动扩充功能可让你在流量/有效负载增大时扩展 TU。 还可以针对 TU 数量设置上限。

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>事件中心自动扩充功能的工作原理是什么？
使用自动扩充功能可以纵向扩展吞吐量单位 (TU)。 这意味着，一开始可以购买较低的 TU，自动扩充会在入口流量增大时纵向扩展 TU。 自动扩充是一种经济高效的选项，可让你完全控制 TU 数量。 这是一个**仅限纵向扩展**的功能，可以通过更新来完全控制 TU 数量的缩减。 

可以从较低的吞吐量单位 (TU) 着手，例如 2 个 TU。 如果预测流量可能会增大到 15 TU，请对命名空间启用自动扩充功能，并将最大限制设置为 15 TU。 现在，当流量增大时可以自动扩展 TU。

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>启用自动扩充功能是否会产生相关的费用？
此功能**不会**产生相关的费用。 

### <a name="how-are-throughput-limits-enforced"></a>如何强制实施吞吐量限制？
如果某个命名空间中所有事件中心间的总入口吞吐量或总入口事件率超过了聚合吞吐量单位限额，发送方会受到限制，并会收到指明已超出入口配额的错误信息。

如果某个命名空间中所有事件中心间的总出口吞吐量或总出口事件率超过了聚合吞吐量单位限额，接收方会受到限制，并会收到指明已超出出口配额的错误信息。 入口和出口配额是分开强制实施的，因此，任何发送方都不会使事件耗用速度减慢，并且接收方也无法阻止事件发送到事件中心。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>可预留/选择的吞吐量单位 (TU) 数量是否有限制？
在多租户产品/服务中，吞吐量单位最多可扩展到 40 TU（可在门户中最多选择 20 TU，然后提出支持票证，在同一命名空间中将数目提高到 40 TU）。 如果超出 40 TU，事件中心可提供名为“事件中心专用群集”的基于资源/容量的模型。 专用群集按容量单位 (CU) 销售。

## <a name="dedicated-clusters"></a>专用群集

### <a name="what-are-event-hubs-dedicated-clusters"></a>什么是事件中心专用群集？
事件中心专用群集提供单租户部署来满足苛刻的客户要求。 此产品/服务会构建一个不受限于吞吐量单位的基于容量的群集。 这意味着，可以利用该群集，根据群集的 CPU 和内存用量情况来引入和流式传输数据。 有关详细信息，请参阅[事件中心专用群集](event-hubs-dedicated-overview.md)。

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>一个容量单位可以实现多大的处理量？
对于专用群集，可以引入和流式传输的数据量取决于各种因素，例如生成者、使用者、引入和处理速率，等等。 

下表显示了我们在测试期间实现的基准结果：

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 400K 消息/秒 | 800 MB/秒 | 800K 消息/秒 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66.6K 消息/秒 | 1.33 GB/秒 | 133K 消息/秒 | 666 TU | 166 TU |
| 6x32KB 批 | 1 | 1.05 GB/秒 | 34K 消息/秒 | 1.05 GB/秒 | 34K 消息/秒 | 1000 TU | 250 TU |

测试中使用了以下条件：

- 一个专用的事件中心群集，其中使用了四个容量单位 (CU)。 
- 用于引入的事件中心包含 200 个分区。 
- 引入的数据由从所有分区接收数据的两个接收方应用程序接收。

结果大致反映了一个专用事件中心群集可以实现的处理量。 此外，专用群集还为微批和长期保留方案启用了“事件中心捕获”。

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>如何创建事件中心专用群集？
可以通过提交[提高配额支持请求](https://portal.azure.com/#create/Microsoft.Support)或联系[事件中心团队](mailto:askeventhubs@microsoft.com)来创建事件中心专用群集。 通常，我们需要花费大约两周时间来部署群集，并将其转交给你使用。 此过程是暂时性的，到时会 Azure 门户或 Azure 资源管理器模板提供完全自助的服务，只需大约两个小时即可部署群集。

## <a name="best-practices"></a>最佳做法

### <a name="how-many-partitions-do-i-need"></a>需要多少分区？

事件中心的分区数在设置后无法修改。 鉴于这一点，请务必在开始之前考虑需要多少分区。 

事件中心设计用于允许每个用户组使用单个分区读取器。 在大多数用例中，四个分区的默认设置就足够了。 如果希望扩展事件处理，则可以考虑添加其他分区。 对分区没有特定的吞吐量限制，但是命名空间中的聚合吞吐量受吞吐量单位数限制。 增加命名空间中吞吐量单位的数量时，可能需要添加额外分区来允许并发读取器实现其自身的最大吞吐量。

但是，如果有一个模型，其中应用程序具有到特定分区的关联性，则增加分区数可能对你没有任何益处。 有关详细信息，请参阅[可用性和一致性](event-hubs-availability-and-consistency.md)。

## <a name="pricing"></a>定价

### <a name="where-can-i-find-more-pricing-information"></a>我可以在哪里找到更多定价信息？

有关事件中心定价的完整信息，请参阅[事件中心定价详细信息](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>保留事件中心事件超过 24 小时的话，要收取费用吗？

事件中心标准层允许消息保留期长于 24 小时，最长以 7 天为限。 如果所存储事件总量的大小超过所选吞吐量单位数量的存储限制（每个吞吐量单位 84 GB），超出限制的部分会按公布的 Azure Blob 存储区费率收费。 每个吞吐量单元的存储限制包括 24 小时（默认值）的保留期期间的所有存储成本，即使吞吐量单元已经用到了最大入口限制。

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>怎样对事件中心存储区大小进行计算和收费？

所有存储的事件的总大小（包括所有事件中心内事件标头或磁盘存储器结构上的所有内部开销）按整天计量。 在一天结束时，计算存储区大小峰值。 每天的存储限制根据在当天所选择的吞吐量单元的最小数量（每个吞吐量单元提供 84 GB 的限制）来计算。 如果总大小超过计算出的每日存储限额，超出的存储量会采用 Azure Blob 存储费率（按**本地冗余存储**费率）来计费。

### <a name="how-are-event-hubs-ingress-events-calculated"></a>事件中心入口事件是怎样计算的？

发送到事件中心的每个事件均计为一条可计费消息。 *入口事件*定义为小于等于 64 KB 的数据单位。 任何小于等于 64 KB 的事件均被视为一个计费事件。 如果该事件大于 64 KB，则根据事件大小按 64 KB 的倍数来计算计费事件的数量。 例如，发送到事件中心的 8-KB 事件按一个事件计费，而发送到事件中心的 96-KB 的消息则按两个事件计费。

从事件中心耗用的事件，以及管理操作和控制调用（例如检查点），不统计为计费入口事件，但会累计，上限为吞吐量单位限额。

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>中转连接费用是否适用于事件中心？

连接费用只在使用 AMQP 协议时适用。 使用 HTTP 发送事件没有连接费用，无论发送系统或设备的数量是多少。 如果计划使用 AMQP（例如，为了实现更高效的事件流式传输，或者为了对 IoT 命令和控制方案启用双向通信），请参阅[事件中心定价信息](https://azure.microsoft.com/pricing/details/event-hubs/)页，了解有关每个服务层级中包括多少连接的详细信息。

### <a name="how-is-event-hubs-capture-billed"></a>事件中心捕获如何计费？

命名空间中的任何事件中心启用了捕获选项时，即可启用捕获功能。 事件中心捕获按所购买的吞吐量单位以小时计费。 当吞吐量单位计数增加或减少时，事件中心捕获计费将在整个小时增量中反映这些变化。 有关事件中心捕获计费的详细信息，请参阅[事件中心定价信息](https://azure.microsoft.com/pricing/details/event-hubs/)。

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>为事件中心捕获选择的存储帐户是否要付费？

捕获使用在事件中心启用捕获时提供的存储帐户。 因为这是你的存储帐户，任何针对此配置的费用更改都将计入你的 Azure 订阅。

## <a name="quotas"></a>配额

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>是否有与事件中心关联的配额？

如需所有事件中心配额的列表，请参阅[配额](event-hubs-quotas.md)。

## <a name="troubleshooting"></a>故障排除

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>为什么在从其他订阅中删除命名空间后无法创建该命名空间？ 
从订阅中删除命名空间时，请等待 4 个小时，然后才能在另一个订阅中使用相同的名称重新创建它。 否则，可能会收到以下错误消息：`Namespace already exists`。 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>事件中心生成的异常有哪些，建议采取什么操作？

有关可能的事件中心异常的列表，请参阅[异常概述](event-hubs-messaging-exceptions.md)。

### <a name="diagnostic-logs"></a>诊断日志

事件中心支持两种类型的[诊断日志](event-hubs-diagnostic-logs.md) - 捕获错误日志和操作日志 - 这两种日志都用 json 表示并可以通过 Azure 门户启用。

### <a name="support-and-sla"></a>支持和 SLA

事件中心的技术支持可通过[社区论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus)获得。 计费和订阅管理支持免费提供。

若要详细了解我们的 SLA，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)页面。

## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心自动膨胀](event-hubs-auto-inflate.md)
