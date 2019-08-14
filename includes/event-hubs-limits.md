---
title: include 文件
description: include 文件
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912289"
---
下表列出了特定于 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)的配额和限制。 有关事件中心定价的信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | 范围 | 说明 | ReplTest1 |
| --- | --- | --- | --- |
| 每个订阅的事件中心命名空间数 |订阅 |- |100 |
| 每个命名空间的事件中心数 |命名空间 |创建新事件中心的后续请求会被拒绝。 |10 |
| 每个事件中心的分区数 |实体 |- |32 |
| 事件中心事件的最大大小|实体 |- |1 MB |
| 事件中心名称的最大大小 |实体 |- |50 个字符 |
| 每个使用者组的非 epoch 接收者数 |实体 |- |5 |
| 最大吞吐量单位 |命名空间 |超出吞吐量单位限制会导致数据受到限制, 并生成[服务器忙异常](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 若要为标准层请求更多的吞吐量单位, 请提供[支持请求](/azure/azure-supportability/how-to-create-azure-support-request)。 [额外的吞吐量单位](../articles/event-hubs/event-hubs-auto-inflate.md)将基于承诺的购买以大小为 20 个单位的块的形式提供。 |20 |
| 每个命名空间的授权规则数量 |命名空间|将拒绝后续的授权规则创建请求。|12 |
| 对 GetRuntimeInformation 方法的调用次数 | 实体 | - | 每秒50 | 
| 虚拟网络 (VNet) 和 IP 配置规则的数量 | 实体 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>事件中心基本和标准-配额和限制
| 限制 | 范围 | 说明 | 基本 | 标准 |
| --- | --- | --- | -- | --- |
| 每个事件中心的使用者组数 |实体 | - |1 |20 |
| 每个命名空间的 AMQP 连接数 |命名空间 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 |100 |5,000|
| 事件数据的最长保留期限 |实体 | - |1 天 |1-7 天 |
|启用 Apache Kafka 命名空间|命名空间 |使用 Kafka 协议的事件中心命名空间流应用程序 |否 | 是 |
|捕获 |实体 | 启用后, 同一个流上的微批处理 |否 |是 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中心专用层-配额和限制
事件中心专用层产品按固定的每月价格计费, 最少使用4小时。 专用层提供标准计划的所有功能, 但具有企业规模容量和对具有要求的工作负荷的客户的限制。 

| 功能 | 限制 |
| --- | ---|
| 带宽 |  20 CU |
| 命名空间 | 每 CU 50 |
| 事件中心 |  每个命名空间1000 |
| 入口事件 | 已包含 |
| 消息大小 | 1000000字节 |
| 分区 | 每个 CU 2000 |
| 使用者组 | 对于每个 CU, 每个事件中心无限制, 1000 |
| 中转连接 | 包括 100 K |
| 消息保留 | 最多7天 (即将推出90天的保留期)、每个 CU 包含 10 TB |
| 捕获 | 已包含 |
