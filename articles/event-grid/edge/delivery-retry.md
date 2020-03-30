---
title: 传递和重试 - Azure 事件网格 IoT 边缘 |微软文档
description: 在 IoT 边缘的事件网格中传递和重试。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841706"
---
# <a name="delivery-and-retry"></a>传递和重试

事件网格提供持久传送。 它尝试立即为每个匹配的订阅至少传递一次每条消息。 如果订户的终结点不承认收到事件或失败，事件网格会基于固定**重试计划和****重试策略**重试传递。  默认情况下，事件网格模块一次向订阅者提供一个事件。 但是，有效负载是具有单个事件的数组。 通过启用输出批处理功能，可以使模块一次提供多个事件。 有关此功能的详细信息，请参阅[输出批处理](delivery-output-batching.md)。  

> [!IMPORTANT]
>事件数据没有持久性支持。 这意味着重新部署或重新启动事件网格模块将导致您丢失尚未传递的任何事件。

## <a name="retry-schedule"></a>重试计划

事件网格在发送消息后最多等待 60 秒以等待响应。 如果订阅者的终结点不确认响应，则消息将排队在我们的一个 backoff 队列中进行后续重试。

有两个预配置的退退队列，确定将尝试重试的计划。 它们分别是：

| 计划 | 描述 |
| ---------| ------------ |
| 1 分钟 | 最终在这里的消息每分钟都会尝试。
| 10 分钟 | 结束在这里的消息每隔 10 分钟尝试一次。

### <a name="how-it-works"></a>工作原理

1. 消息到达事件网格模块。 尝试立即交付它。
1. 如果传递失败，则消息将排队到 1 分钟的队列中，并在一分钟后重试。
1. 如果传递继续失败，则消息将排队到 10 分钟的队列中，每 10 分钟重试一次。
1. 在成功或重试策略限制之前，将尝试交付。

## <a name="retry-policy-limits"></a>重试策略限制

有两种配置决定了重试策略。 它们分别是：

* 最大尝试次数
* 活动生存时间 （TTL）

如果达到重试策略的任一限制，将删除事件。 重试计划本身在"重试计划"部分中介绍。 可以对所有订阅者或基于订阅执行这些限制的配置。 以下部分将介绍每个部分的进一步详细信息。

## <a name="configuring-defaults-for-all-subscribers"></a>配置所有订阅者的默认值

有两个属性：`brokers__defaultMaxDeliveryAttempts``broker__defaultEventTimeToLiveInSeconds`可以配置为事件网格部署的一部分，该部署控制所有订阅者的重试策略默认值。

| 属性名称 | 描述 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 传递事件的最大尝试次数。 默认值：30。
| `broker__defaultEventTimeToLiveInSeconds` | 事件 TTL，在几秒钟内，如果未传递事件，将丢弃事件。 默认值： **7200**秒

## <a name="configuring-defaults-per-subscriber"></a>配置每个订阅者的默认值

您还可以根据每个订阅指定重试策略限制。
有关如何为每个订阅者配置默认值的信息，请参阅我们的[API 文档](api.md)。 订阅级默认值覆盖模块级别配置。

## <a name="examples"></a>示例

以下示例在事件网格模块中设置重试策略，最大尝试次数 = 3 和事件 TTL 为 30 分钟

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
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

以下示例设置最大尝试次数 = 3 和事件 TTL 30 分钟的 Web 挂钩订阅

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
