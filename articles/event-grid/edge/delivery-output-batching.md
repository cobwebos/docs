---
title: 在 Azure 事件网格 IoT 边缘中的输出批处理 |微软文档
description: 在 IoT 边缘的事件网格中输出批处理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841741"
---
# <a name="output-batching"></a>输出批处理

事件网格支持在单个传递请求中传递多个事件。 此功能无需支付每个请求的 HTTP 开销即可提高总体交付吞吐量。 批处理默认情况下处于关闭状态，并且可以按订阅打开。

> [!WARNING]
> 处理每个传递请求的最长允许持续时间不会更改，即使订阅者代码可能必须执行每个批处理请求的更多工作。 交货超时默认为 60 秒。

## <a name="batching-policy"></a>批处理策略

通过调整以下两个设置，可以自定义每个订阅者的事件网格的批处理行为：

* 每个批次的最大事件数

  此设置设置可添加到批处理传递请求的事件数的上限。

* 首选批次大小（以千字节为单位）

  此旋钮用于进一步控制每个传递请求可以发送的最大千字节数

## <a name="batching-behavior"></a>批处理行为

* 全部或无

  事件网格使用全或无语义运行。 它不支持批处理交付的部分成功。 订阅者应小心，每批只要求尽可能多的事件，因为他们可以在 60 秒内合理处理。

* 乐观批处理

  批处理策略设置对批处理行为不是严格限制，并且会尽力而为地遵守。 在低事件速率下，您通常会观察到批处理大小小于每个批次请求的最大事件。

* 默认值设置为"关闭"

  默认情况下，事件网格仅为每个传递请求添加一个事件。 打开批处理的方法是在事件订阅 JSON 中设置本文前面提到的任一设置。

* 默认值

  创建事件订阅时，无需同时指定设置（每个批处理的最大事件和以千字节为单位的近似批处理大小）。 如果仅设置一个设置，事件网格将使用（可配置）默认值。 有关默认值以及如何重写这些值，请参阅以下部分。

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

## <a name="configuring-maximum-allowed-values"></a>配置允许的最大值

以下部署时间设置控制创建事件订阅时允许的最大值。

| 属性名称 | 描述 |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | `PreferredBatchSizeInKilobytes`旋钮允许的最大值。 默认值`1033`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch`旋钮允许的最大值。 默认值`50`。

## <a name="configuring-runtime-default-values"></a>配置运行时默认值

在事件订阅中未指定每个旋钮的运行时默认值时，以下部署时间设置将控制该按钮的运行时默认值。 要重申，必须在事件订阅上设置至少一个旋钮才能打开批处理行为。

| 属性名称 | 描述 |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | 仅`MaxEventsPerBatch`指定时的最大传递请求大小。 默认值`1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 仅`MaxBatchSizeInBytes`指定时要添加到批处理的最大事件数。 默认值`10`。
