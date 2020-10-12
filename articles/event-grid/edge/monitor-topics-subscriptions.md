---
title: 监视主题和事件订阅-Azure 事件网格 IoT Edge |Microsoft Docs
description: 监视主题和事件订阅
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d230be4f74abd61ad7b7f0fdb3facb32ee63da73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171527"
---
# <a name="monitor-topics-and-event-subscriptions"></a>监视主题和事件订阅

边缘的事件网格公开了多个 [Prometheus 处于阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)的主题和事件订阅的指标。 本文介绍可用的指标以及如何启用它们。

## <a name="enable-metrics"></a>启用指标

将该模块配置为发出指标，方法是 `metrics__reporterType` `prometheus` 在容器创建选项中将环境变量设置为：

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
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

适用于 http 和 https 的模块中提供了度量值 `5888/metrics` `4438/metrics` 。 例如， `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` 对于 http。 此时，指标模块可以轮询终结点以收集指标，如此 [示例结构](https://github.com/veyalla/ehm)中所示。

## <a name="available-metrics"></a>可用度量值

主题和事件订阅都发出指标，使你能够深入了解事件传递和模块性能。

### <a name="topic-metrics"></a>主题指标

| 指标 | 描述 |
| ------ | ----------- |
| EventsReceived | 发布到主题的事件数
| UnmatchedEvents | 已发布到与事件订阅不匹配并且被删除的主题的事件数
| SuccessRequests | 主题收到的入站发布请求数
| SystemErrorRequests | 由于内部系统错误，入站发布请求失败次数
| UserErrorRequests | 由于用户错误（例如 JSON 格式错误）导致入站发布请求失败
| SuccessRequestLatencyMs | 发布请求响应延迟时间（毫秒）


### <a name="event-subscription-metrics"></a>事件订阅指标

| 指标 | 描述 |
| ------ | ----------- |
| DeliverySuccessCounts | 成功传递到配置的终结点的事件数
| DeliveryFailureCounts | 未能传递到配置的终结点的事件数
| DeliverySuccessLatencyMs | 成功传递的事件滞后时间（毫秒）
| DeliveryFailureLatencyMs | 事件传递失败的延迟时间（毫秒）
| SystemDelayForFirstAttemptMs | 第一次传递尝试前事件的系统延迟（毫秒）
| DeliveryAttemptsCount | 事件传递尝试次数-成功和失败
| ExpiredCounts | 已过期并且未传递到配置的终结点的事件数