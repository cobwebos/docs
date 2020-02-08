---
title: 监视主题和事件订阅-Azure 事件网格 IoT Edge |Microsoft Docs
description: 监视主题和事件订阅
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086675"
---
# <a name="monitor-topics-and-event-subscriptions"></a>监视主题和事件订阅

边缘的事件网格公开了多个[Prometheus 处于阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)的主题和事件订阅的指标。 本文介绍可用的指标以及如何启用它们。

## <a name="enable-metrics"></a>启用指标

将该模块配置为发出指标，方法是将 `metrics__reporterType` 环境变量设置为容器创建选项中 `prometheus`：

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

在 `5888/metrics` 适用于 http 的模块以及 https `4438/metrics` 的指标。 例如，`http://<modulename>:5888/metrics?api-version=2019-01-01-preview` http。 此时，指标模块可以轮询终结点以收集指标，如此[示例结构](https://github.com/veyalla/ehm)中所示。

## <a name="available-metrics"></a>可用指标

主题和事件订阅都发出指标，使你能够深入了解事件传递和模块性能。

### <a name="topic-metrics"></a>主题指标

| 跃点数 | 说明 |
| ------ | ----------- |
| EventsReceived | 发布到主题的事件数
| UnmatchedEvents | 已发布到与事件订阅不匹配并且被删除的主题的事件数
| SuccessRequests | 主题收到的入站发布请求数
| SystemErrorRequests | 由于内部系统错误，入站发布请求失败次数
| UserErrorRequests | 由于用户错误（例如 JSON 格式错误）导致入站发布请求失败
| SuccessRequestLatencyMs | 发布请求响应延迟时间（毫秒）


### <a name="event-subscription-metrics"></a>事件订阅指标

| 跃点数 | 说明 |
| ------ | ----------- |
| deliverySuccessCounts | 成功传递到配置的终结点的事件数
| deliveryFailureCounts | 未能传递到配置的终结点的事件数
| deliverySuccessLatencyMs | 成功传递的事件滞后时间（毫秒）
| deliveryFailureLatencyMs | 事件传递失败的延迟时间（毫秒）
| systemDelayForFirstAttemptMs | 第一次传递尝试前事件的系统延迟（毫秒）
| deliveryAttemptsCount | 事件传递尝试次数-成功和失败
| expiredCounts | 已过期并且未传递到配置的终结点的事件数