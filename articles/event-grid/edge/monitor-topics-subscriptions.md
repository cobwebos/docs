---
title: 监视主题和事件订阅 - Azure 事件网格 IoT 边缘 |微软文档
description: 监视主题和事件订阅
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086675"
---
# <a name="monitor-topics-and-event-subscriptions"></a>监视主题和事件订阅

边缘上的事件网格以[Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)公开主题和事件订阅的一些指标。 本文介绍了可用的指标以及如何启用它们。

## <a name="enable-metrics"></a>启用指标

通过将`metrics__reporterType`环境变量设置为容器创建选项`prometheus`，将模块配置为发出指标：

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

指标将在模块中`5888/metrics`提供 http 和`4438/metrics`https。 例如，`http://<modulename>:5888/metrics?api-version=2019-01-01-preview`对于 http。 此时，指标模块可以轮询终结点以收集指标，如本[示例体系结构](https://github.com/veyalla/ehm)中所示。

## <a name="available-metrics"></a>可用指标

主题和事件订阅都会发布指标，让您深入了解事件交付和模块性能。

### <a name="topic-metrics"></a>主题指标

| 指标 | 描述 |
| ------ | ----------- |
| 收到的事件 | 发布到主题的事件数
| 不匹配的事件 | 发布到主题的事件数与事件订阅不匹配并被删除
| 成功请求 | 主题收到的入站发布请求数
| 系统错误请求 | 由于内部系统错误而失败的入站发布请求数
| 用户错误请求 | 由于用户错误（如格式错误的 JSON），入站发布请求上的编号失败
| 成功请求延迟M | 以毫秒为单位发布请求响应延迟


### <a name="event-subscription-metrics"></a>事件订阅指标

| 指标 | 描述 |
| ------ | ----------- |
| 交付成功计数 | 成功传递到配置的终结点的事件数
| 交付失败计数 | 未能传递到配置的终结点的事件数
| 交付成功延迟M | 以毫秒为单位成功交付的事件延迟
| 交付失败延迟M | 事件传递失败的延迟（以毫秒为单位）
| 系统延迟首次尝试 | 在第一次传递尝试之前的事件的系统延迟（以毫秒为单位）
| 交付尝试计数 | 事件传递尝试次数 - 成功和失败
| 过期计数 | 过期且未传递到配置的终结点的事件数