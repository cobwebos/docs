---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012852"
---
下表提供了特定于 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)的配额和限制。 有关事件中心定价的信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

以下限制是基本层和标准层的通用限制。 

| 限制 | 范围 | 说明 | 值 |
| --- | --- | --- | --- |
| 每个订阅的事件中心命名空间数 |订阅 |- |100 |
| 每个命名空间的事件中心数 |命名空间 |系统会拒绝创建新事件中心的后续请求。 |10 |
| 每个事件中心的分区数 |实体 |- |32 |
| 事件中心名称的最大大小 |实体 |- | 256 个字符 |
| 使用者组名称的最大大小 |实体 |- | 256 个字符 |
| 每个使用者组的非 epoch 接收者数 |实体 |- |5 |
| 最大吞吐量单位 |命名空间 |超出吞吐量单位限制会导致数据受到限制，并生成[服务器繁忙异常](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 要为标准层请求大量的吞吐量单位，可填写[支持请求](/azure/azure-portal/supportability/how-to-create-azure-support-request)。 [额外的吞吐量单位](../articles/event-hubs/event-hubs-auto-inflate.md)将基于承诺的购买以大小为 20 个单位的块的形式提供。 |20 |
| 每个命名空间的授权规则数量 |命名空间|系统会拒绝创建授权规则的后续请求。|12 |
| GetRuntimeInformation 方法的调用数 | 实体 | - | 每秒 50 个 | 
| 虚拟网络 (VNet) 和 IP 配置规则的数量 | 实体 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>事件中心基本层和标准层 - 配额和限制
| 限制 | 范围 | 说明 | 基本 | Standard |
| --- | --- | --- | -- | --- |
| 事件中心事件的最大大小|实体 | &nbsp; | 256 KB | 1 MB |
| 每个事件中心的使用者组数 |实体 | &nbsp; |1 |20 |
| 每个命名空间的 AMQP 连接数 |命名空间 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 |100 |5,000|
| 事件数据的最长保留期限 |实体 | &nbsp; |1 天 |1-7 天 |
|启用 Apache Kafka 的命名空间|命名空间 |使用 Kafka 协议的事件中心命名空间流应用程序。 有关详细信息，请参阅[通过 Apache Kafka 应用程序使用 Azure 事件中心](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)。 |否 | 是 |
|捕获 |实体 | 启用后，同一个流上的微批。 有关详细信息，请参阅[通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件](../articles/event-hubs/event-hubs-capture-overview.md)。 |否 |是 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中心专用层 - 配额和限制
事件中心专用层产品/服务按固定的每月价格计费，最少用量为 4 小时。 专用层提供标准计划的所有功能，但具有企业规模容量和限制，以满足客户的工作负荷需求。 

| Feature | 限制 |
| --- | ---|
| 带宽 |  20 个 CU |
| 命名空间 | 每个 CU 50 个 |
| 事件中心 |  每个命名空间 1000 个 |
| 入口事件 | 附送 |
| 消息大小 | 1 MB |
| 分区 | 每个 CU 2000 个 |
| 使用者组 | 每个 CU 的对应数量无限制，每个事件中心 1000 个 |
| 中转连接 | 包括 100 K |
| 消息保留 | 90 天，每个 CU 包含 10 TB |
| 捕获 | 附送 |
