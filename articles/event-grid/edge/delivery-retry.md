---
title: 传递和重试-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格中的传递和重试。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0a678023b1097c4bdec70d866632da6ae4ad57bb
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992387"
---
# <a name="delivery-and-retry"></a>传递和重试

事件网格提供持久传送。 对于每个匹配的订阅，它会尝试至少为每个消息传递一次。 如果订阅服务器的终结点没有确认收到事件或发生故障，事件网格会根据固定**重试计划**和**重试策略**重试传递。  当前，事件网格模块会将事件一次传递到订阅服务器。 但负载为具有单个事件的数组。

> [!IMPORTANT]
>对于事件数据不存在持久性支持。 这意味着重新部署或重新启动事件网格模块将导致丢失尚未传递的任何事件。

## <a name="retry-schedule"></a>重试计划

传递消息后，事件网格会等待长达60秒的响应。 如果订阅服务器的终结点不能确认响应，则该消息将在某个后端队列中排队等待以后重试。

有两个预配置的后端队列，它们确定将尝试重试的计划。 它们是：-

| 计划 | 描述 |
| ---------| ------------ |
| 1 分钟 | 每隔一分钟就会尝试发送到此处的消息。
| 10 分钟 | 每隔10分钟就会尝试在此处结束的消息。

### <a name="how-it-works"></a>如何运作

1. 消息到达事件网格模块。 尝试立即交付。
1. 如果传递失败，则消息将排队进入1分钟队列，并在一分钟后重试。
1. 如果传递继续失败，则消息将排队进入10分钟队列，并每10分钟重试一次。
1. 在达到成功或重试策略限制之前，会尝试传递。

## <a name="retry-policy-limits"></a>重试策略限制

可以通过两种配置来确定重试策略。 它们是：-

* 最大尝试次数
* 事件生存时间（TTL）

如果达到重试策略的任一限制，则会删除事件。 重试计划本身在重试计划部分中进行了介绍。 对于所有订阅服务器或订阅，可以配置这些限制。 以下部分详细介绍了每一种情况。

## <a name="configuring-defaults-for-all-subscribers"></a>为所有订阅服务器配置默认值

有两个属性： `brokers:defaultMaxDeliveryAttempts` 和 `broker:defaultEventTimeToLiveInSeconds`，可将其配置为事件网格部署的一部分，该部署控制所有订阅服务器的重试策略默认值。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | 尝试传递事件的最大次数。 默认值：30。
| `broker:defaultEventTimeToLiveInSeconds` | 事件 TTL （秒），在此时间之后，如果未传递事件，事件将被丢弃。 默认值： **7200**秒

## <a name="configuring-defaults-per-subscriber"></a>为每个订阅服务器配置默认值

你还可以基于每个订阅指定重试策略限制。
有关如何配置每个订阅服务器的默认值的信息，请参阅[API 文档](api.md)。 订阅级别默认值覆盖模块级别配置。

## <a name="examples"></a>示例

下面的示例在事件网格模块中设置重试策略，其中 maxNumberOfAttempts = 3，事件 TTL 为30分钟

```json
{
  "Env": [
    "broker:defaultMaxDeliveryAttempts=3",
    "broker:defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

下面的示例使用 maxNumberOfAttempts = 3 设置 Web 挂钩订阅，并使用30分钟的事件 TTL 设置

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
