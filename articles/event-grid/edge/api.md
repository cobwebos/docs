---
title: REST API-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ee2b3a35b6f1817b89541a31d0bde4adf00ade2a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992530"
---
# <a name="rest-api"></a>REST API
本文介绍 IoT Edge 上的 Azure 事件网格的 REST Api

## <a name="common-api-behavior"></a>常见 API 行为

### <a name="base-url"></a>基 URL
IoT Edge 上的事件网格具有通过 HTTP （端口5888）和 HTTPS （端口4438）公开的以下 Api。

* HTTP 的基本 URL： http://eventgridmodule:5888
* HTTPS 的基本 URL： https://eventgridmodule:4438

### <a name="request-query-string"></a>请求查询字符串
所有 API 请求都需要以下查询字符串参数：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>请求内容类型
所有 API 请求必须具有**Content 类型**。

对于**EventGridSchema**或**CustomSchema**，content-type 的值可以是以下值之一：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

对于结构化模式下的**CloudEventSchemaV1_0** ，content-type 的值可以是以下值之一：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

对于二进制模式下的**CloudEventSchemaV1_0** ，请参阅[文档](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)了解详细信息。

### <a name="error-response"></a>错误响应
所有 Api 都将返回具有以下有效负载的错误：

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>管理主题

### <a name="put-topic-create--update"></a>Put 主题（创建/更新）

**请求**： ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**有效负载**：

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**响应**： HTTP 200

**有效负载**：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>获取主题

**请求**： ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200

**有效负载**：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>获取所有主题

**请求**： ``` GET /topics?api-version=2019-01-01-preview ```

**响应**： HTTP 200

**有效负载**：
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>删除主题

**请求**： ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200，空有效负载

## <a name="manage-event-subscriptions"></a>管理事件订阅
本部分中的示例使用 `EndpointType=Webhook;`。 下一节介绍 `EndpointType=EdgeHub / EndpointType=EventGrid` 的 json 示例。 

### <a name="put-event-subscription-create--update"></a>Put 事件订阅（创建/更新）

**请求**： ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**有效负载**：
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**响应**： HTTP 200

**有效负载**：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>获取事件订阅

**请求**： ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200

**有效负载**：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>获取事件订阅

**请求**： ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**响应**： HTTP 200

**有效负载**：
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>删除事件订阅

**请求**： ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200，无有效负载


## <a name="publish-events-api"></a>发布事件 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>发送事件批（在事件网格架构中）

**请求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**响应**： HTTP 200，空有效负载


**负载字段说明**
- ```Id``` 是必需的。 它可以是由调用方填充的任何字符串值。 事件网格不会对此字段进行任何重复检测或强制执行任何语义。
- ```Topic``` 是可选的，但如果指定必须与请求 URL 中的 topic_name 匹配
- ```Subject``` 是必需的，可以是任何字符串值
- ```EventType``` 是必需的，可以是任何字符串值
- ```EventTime``` 是必需的，它不会进行验证，但应为正确的日期时间。
- ```DataVersion``` 是必需的
- ```MetadataVersion``` 是可选的，如果指定，则它必须是值为 ```"1"``` 的字符串。
- ```Data``` 是可选的，可以是任何 JSON 标记（number、string、boolean、array、object）

### <a name="send-batch-of-events-in-custom-schema"></a>发送事件批（在自定义架构中）

**请求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**响应**： HTTP 200，空有效负载


**负载限制**
- 必须是事件的数组。
- 每个数组项必须是一个 JSON 对象。
- 无其他约束（负载大小除外）。

## <a name="examples"></a>示例

### <a name="set-up-topic-with-eventgrid-schema"></a>用 EventGrid 架构设置主题
设置一个主题，要求在**eventgridschema**中发布事件。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>用自定义架构设置主题
设置一个主题，要求在 `customschema`中发布事件。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>设置主题和云事件架构
设置一个主题，要求在 `cloudeventschema`中发布事件。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>将 WebHook 设置为目标，要在 eventgridschema 中传递的事件
使用此目标类型可将事件发送到网络/internet 上的任何其他模块（托管 HTTP 终结点）或任何 HTTP 可寻址终结点。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

对 `endpointUrl` 属性的约束：
- 它必须为非 null。
- 它必须是绝对 URL。
- 如果在 EventGridModule 设置中将 outbound__webhook__httpsOnly 设置为 true，则它必须仅为 HTTPS。
- 如果 outbound__webhook__httpsOnly 设置为 false，则它可以是 HTTP 或 HTTPS。

对 `eventDeliverySchema` 属性的约束：
- 它必须与订阅主题的输入架构匹配。
- 它可以为 null。 它默认为主题的输入架构。

### <a name="set-up-iot-edge-as-destination"></a>将 IoT Edge 设置为目标

使用此目标可将事件发送到 IoT Edge 中心，并将其传递到 Edge 中心的路由/筛选/转发子系统。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>设置事件网格云作为目标

使用此目标可将事件发送到云（Azure）中的事件网格。 在边缘上创建事件订阅之前，你需要先在云中设置要向其发送事件的用户主题。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- 它必须为非 null。
- 它必须是绝对 URL。
- 必须在请求 URL 路径中定义路径 `/api/events`。
- 它必须在查询字符串中有 `api-version=2018-01-01`。
- 如果在 EventGridModule 设置中将 outbound__eventgrid__httpsOnly 设置为 true （默认值为 true），则它必须仅为 HTTPS。
- 如果 outbound__eventgrid__httpsOnly 设置为 false，则它可以是 HTTP 或 HTTPS。
- 如果 outbound__eventgrid__allowInvalidHostnames 设置为 false （默认值为 false），则它必须面向以下终结点之一：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- 必须为非 null。

TopicName:
- 如果 EventDeliverySchema 设置为 EventGridSchema，则此字段的值将放入每个事件的 "主题" 字段，然后将其转发到云中的事件网格。
- 如果 EventDeliverySchema 设置为 CustomEventSchema，则将忽略此属性，并按接收到的原样转发自定义事件负载。
