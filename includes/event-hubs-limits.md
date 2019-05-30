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
ms.openlocfilehash: 3f3b60c3744ce9dea61054b3fa0aaccfea27d784
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238179"
---
下表列出了特定于 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)的配额和限制。 有关事件中心定价的信息，请参阅[事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 限制 | 范围 | 说明 | 值 |
| --- | --- | --- | --- |
| 每个订阅的事件中心命名空间数 |订阅 |- |100 |
| 每个命名空间的事件中心数 |命名空间 |创建新事件中心的后续请求会被拒绝。 |10 |
| 每个事件中心的分区数 |实体 |- |32 |
| 每个事件中心的使用者组数 |实体 |- |20 |
| 每个命名空间的 AMQP 连接数 |命名空间 |系统会拒绝后续的附加连接请求，且调用代码会收到异常。 |5,000 |
| 事件中心事件的最大大小|实体 |- |1 MB |
| 事件中心名称的最大大小 |实体 |- |50 个字符 |
| 每个使用者组的非 epoch 接收者数 |实体 |- |5 |
| 事件数据的最长保留期限 |实体 |- |1-7 天 |
| 最大吞吐量单位 |命名空间 |超出吞吐量单位限制会导致数据受到限制，并生成[服务器忙异常](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 若要请求更多的标准层的吞吐量单位，文件[支持请求](/azure/azure-supportability/how-to-create-azure-support-request)。 [额外的吞吐量单位](../articles/event-hubs/event-hubs-auto-inflate.md)将基于承诺的购买以大小为 20 个单位的块的形式提供。 |20 |
| 每个命名空间的授权规则数量 |命名空间|将拒绝后续的授权规则创建请求。|12 |
| GetRuntimeInformation 方法对的调用数 | 实体 | - | 每秒 50 | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中心专用-配额和限制
事件中心专用产品/服务按固定的每月价格，至少包含 4 个小时的使用量计费。 专用的层提供标准计划的但具有企业规模容量和限制的所有功能的客户的要求苛刻的工作负荷。 

| Feature | 限制 |
| --- | ---|
| 带宽 |  20 CU |
| 命名空间 | CU 每 50 个 |
| 事件中心 |  事件中心/主题上没有限制 |
| 入口事件 | 附送 |
| 消息大小 | 1 百万个字节 |
| 分区 | 每个 CU 2000 |
| 使用者组 | 每个事件中心的 1000年每 CU，没有限制 |
| 中转连接 | 包括 100 K |
| 消息保留 | 90 天，每个 CU 包含 10 TB |
| 捕获 | 附送 |
