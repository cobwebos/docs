---
title: Azure 事件网格中的输出批处理 IoT Edge |Microsoft Docs
description: 在 IoT Edge 的事件网格中输出批处理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841741"
---
# <a name="output-batching"></a>输出批处理

事件网格支持在单个传递请求中传递多个事件。 利用此功能，无需支付 HTTP 每请求开销，就能增加总体交付吞吐量。 批处理默认情况下处于关闭状态，并且可以打开每个订阅。

> [!WARNING]
> 处理每个传递请求所允许的最大持续时间不会更改，即使订阅服务器代码可能需要对每个批处理请求执行更多的工作。 传递超时默认为60秒。

## <a name="batching-policy"></a>批处理策略

可以通过调整以下两个设置，为每个订阅服务器自定义事件网格的批处理行为：

* 每批的最大事件数

  此设置设置可添加到批处理传递请求的事件数的上限。

* 首选批大小（Kb）

  此旋钮用于进一步控制每个传递请求可发送的最大 kb 数（kb）

## <a name="batching-behavior"></a>批处理行为

* 全部或无

  事件网格操作的语义全部为 "无" 或 "无"。 它不支持批处理交付的部分成功。 订户应注意只需要为每批请求尽可能多的事件，因为它们可以在60秒内合理地处理。

* 开放式批处理

  批处理策略设置在批处理行为上并不严格，会尽力进行。 如果事件速率较低，则会发现批大小低于每批请求的最大事件数。

* 默认设置为 "关闭"

  默认情况下，事件网格仅向每个传递请求添加一个事件。 启用批处理的方式是设置之前在事件订阅 JSON 的项目中提到的一个设置。

* 默认值

  创建事件订阅时，无需同时指定设置（每批的最大事件数和近似的批大小）。 如果仅设置了一个设置，则事件网格将使用（可配置）默认值。 请参阅以下各节以了解默认值，以及如何替代它们。

## <a name="turn-on-output-batching"></a>打开输出批处理

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>配置最大允许值

以下部署时间设置控制创建事件订阅时允许的最大值。

| 属性名称 | Description |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes` 旋钮允许的最大值。 默认 `1033`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 旋钮允许的最大值。 默认 `50`。

## <a name="configuring-runtime-default-values"></a>配置运行时默认值

以下部署时间设置控制每个旋钮在事件订阅中未指定时的运行时默认值。 重申一遍，必须在事件订阅上设置至少一个旋钮才能打开批处理行为。

| 属性名称 | Description |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 仅指定 `MaxEventsPerBatch` 时的最大传递请求大小。 默认 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅指定 `MaxBatchSizeInBytes` 时要添加到批中的事件的最大数量。 默认 `10`。
