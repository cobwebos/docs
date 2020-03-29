---
title: REST API - Azure 事件网格 IoT 边缘 |微软文档
description: 在 IoT 边缘的事件网格上 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841823"
---
# <a name="rest-api"></a>REST API
本文介绍了 IoT 边缘上 Azure 事件网格的 REST API

## <a name="common-api-behavior"></a>常见 API 行为

### <a name="base-url"></a>基 URL
IoT Edge 上的事件网格具有通过 HTTP（端口 5888）和 HTTPS（端口 4438）公开的以下 API。

* 用于 HTTP 的基本 URL：http://eventgridmodule:5888
* 用于 HTTPS 的基本 URL：https://eventgridmodule:4438

### <a name="request-query-string"></a>请求查询字符串
所有 API 请求都需要以下查询字符串参数：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>请求内容类型
所有 API 请求都必须具有**内容类型**。

在**事件网格架构**或**自定义架构**中，内容类型的值可以是以下值之一：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

在结构化模式下**CloudEventSchemaV1_0**的情况下，内容类型的值可以是以下值之一：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

如果**CloudEventSchemaV1_0**在二进制模式下，请参阅[文档](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)以了解详细信息。

### <a name="error-response"></a>错误响应
所有 API 都返回具有以下有效负载的错误：

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

### <a name="put-topic-create--update"></a>放置主题（创建/更新）

**请求**：``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

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

**请求**：``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

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

**请求**：``` GET /topics?api-version=2019-01-01-preview ```

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

**请求**：``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200，空负载

## <a name="manage-event-subscriptions"></a>管理事件订阅
本节中的示例使用`EndpointType=Webhook;`。 下一节将为其`EndpointType=EdgeHub / EndpointType=EventGrid`的 json 示例。 

### <a name="put-event-subscription-create--update"></a>放置事件订阅（创建/更新）

**请求**：``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

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
        "persistencePolicy": "true",
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

**请求**：``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

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

**请求**：``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

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

**请求**：``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**响应**： HTTP 200，无有效负载


## <a name="publish-events-api"></a>发布事件 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>发送事件批处理（在事件网格架构中）

**请求**：``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

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

**响应**： HTTP 200，空负载


**有效负载字段描述**
- ```Id``` 是必需的。 它可以是调用方填充的任何字符串值。 事件网格不执行任何重复检测或强制执行此字段上的任何语义。
- ```Topic```是可选的，但如果指定，则必须与请求 URL 中topic_name匹配
- ```Subject```是必填项，可以是任意字符串值
- ```EventType```是必填项，可以是任意字符串值
- ```EventTime```是强制性的，它未经过验证，但应该是一个适当的日期时间。
- ```DataVersion``` 是必需的
- ```MetadataVersion```是可选的，如果指定，它必须是具有值的字符串```"1"```
- ```Data```是可选的，可以是任何 JSON 令牌（数字、字符串、布尔、数组、对象）

### <a name="send-batch-of-events-in-custom-schema"></a>发送一批事件（在自定义架构中）

**请求**：``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**响应**： HTTP 200，空负载


**有效负载限制**
- 必须是事件数组。
- 每个数组条目都必须是 JSON 对象。
- 没有其他约束（有效负载大小）。

## <a name="examples"></a>示例

### <a name="set-up-topic-with-eventgrid-schema"></a>使用事件网格架构设置主题
设置主题以要求在**事件网格架构**中发布事件。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>使用自定义架构设置主题
设置主题以要求在 中`customschema`发布事件。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>使用云事件架构设置主题
设置主题以要求在 中`cloudeventschema`发布事件。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>将 WebHook 设置为目标，在事件网格架构中传递的事件
使用此目标类型将事件发送到任何其他模块（承载 HTTP 终结点）或网络/Internet 上任何可寻址终结点。

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

属性的约束`endpointUrl`：
- 它必须是非 null 的。
- 它必须是绝对的 URL。
- 如果在事件网格模块设置中outbound__webhook__httpsOnly设置为 true，则它必须仅是 HTTPS。
- 如果outbound__webhook__httpsOnly设置为 false，则可以是 HTTP 或 HTTPS。

属性的约束`eventDeliverySchema`：
- 它必须与订阅主题的输入架构匹配。
- 它可以为空。 它默认为主题的输入架构。

### <a name="set-up-iot-edge-as-destination"></a>将 IoT 边缘设置为目标

使用此目标将事件发送到 IoT 边缘中心，并受边缘中心路由/筛选/转发子系统的影响。

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

### <a name="set-up-event-grid-cloud-as-destination"></a>将事件网格云设置为目标

使用此目标可以将事件发送到云中的事件网格 （Azure）。 您需要先在云中设置一个用户主题，然后在边缘创建事件订阅之前，应将事件发送到该主题。

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

端点Url：
- 它必须是非 null 的。
- 它必须是绝对的 URL。
- 路径`/api/events`必须在请求 URL 路径中定义。
- 它必须在`api-version=2018-01-01`查询字符串中。
- 如果在事件网格模块设置中将outbound__eventgrid__httpsOnly设置为 true（默认情况下为 true），则它必须仅为 HTTPS。
- 如果outbound__eventgrid__httpsOnly设置为 false，则可以是 HTTP 或 HTTPS。
- 如果outbound__eventgrid__allowInvalidHostnames设置为 false（默认情况下为 false），则必须针对以下终结点之一：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

萨斯基：
- 必须为非空。

主题名称：
- 如果订阅.事件交付架构设置为事件GridSchema，则此字段中的值将放入每个事件的主题字段中，然后再转发到云中的事件网格。
- 如果订阅.event 传递架构设置为自定义事件架构，则将忽略此属性，并且自定义事件负载将完全按照收到的格式转发。

## <a name="set-up-event-hubs-as-a-destination"></a>将事件中心设置为目标

要发布到事件中心，请设置为`endpointType`并提供： `eventHub`

* 连接字符串：通过共享访问策略生成的特定事件中心的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 可以通过导航到要在 Azure 门户中发布到的特定事件中心，然后单击**共享访问策略**来生成新的实体特定连接字符串，从而生成实体特定的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>将服务总线队列设置为目标

要发布到服务总线队列，请设置为`endpointType`和`serviceBusQueue`提供：

* 连接字符串：通过共享访问策略生成的特定服务总线队列的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线队列，然后单击**共享访问策略**以生成新的实体特定连接字符串，生成实体特定的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>将服务总线主题设置为目标

要发布到服务总线主题，请设置为`endpointType`和`serviceBusTopic`提供：

* 连接字符串：通过共享访问策略生成的特定服务总线主题的连接字符串。

    >[!NOTE]
    > 连接字符串必须是特定于实体的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线主题，然后单击**共享访问策略**以生成新的实体特定连接字符串，生成实体特定的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>将存储队列设置为目标

要发布到存储队列，请设置为`endpointType`和`storageQueue`提供：

* 队列名称：要发布到的存储队列的名称。
* 连接字符串：存储队列中的存储帐户的连接字符串。

    >[!NOTE]
    > 取消行线事件中心、服务总线队列和服务总线主题，用于存储队列的连接字符串不是特定于实体的。 相反，它必须，但存储帐户的连接字符串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```