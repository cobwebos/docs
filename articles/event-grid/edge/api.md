---
title: REST API-Azure 事件网格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件网格 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171748"
---
# <a name="rest-api"></a>REST API
本文介绍 IoT Edge 上的 Azure 事件网格的 REST Api

## <a name="common-api-behavior"></a>常见 API 行为

### <a name="base-url"></a>基 URL
IoT Edge 上的事件网格通过 HTTP (端口 5888) 和 HTTPS (端口 4438) 公开了以下 Api。

* HTTP 的基 URL： http://eventgridmodule:5888
* HTTPS 的基本 URL： https://eventgridmodule:4438

### <a name="request-query-string"></a>请求查询字符串
所有 API 请求都需要以下查询字符串参数：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>请求内容类型
所有 API 请求必须具有 **Content 类型**。

对于 **EventGridSchema** 或 **CustomSchema**，content-type 的值可以是以下值之一：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

如果在结构化模式下 **CloudEventSchemaV1_0** ，content-type 的值可以是以下值之一：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

如果在二进制模式下 **CloudEventSchemaV1_0** ，请参阅 [文档](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) 以了解详细信息。

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

### <a name="put-topic-create--update"></a>将主题 (创建/更新) 

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
本部分中的示例使用 `EndpointType=Webhook;` 。 下一节中提供了的 json 示例 `EndpointType=EdgeHub / EndpointType=EventGrid` 。 

### <a name="put-event-subscription-create--update"></a>将事件订阅 (创建/更新) 

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

### <a name="send-batch-of-events-in-event-grid-schema"></a>在事件网格架构) 中发送事件批处理 (

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
- ```Topic``` 是可选的，但如果指定的必须与请求 URL 中的 topic_name 匹配
- ```Subject``` 是必需的，可以是任何字符串值
- ```EventType``` 是必需的，可以是任何字符串值
- ```EventTime``` 是必需的，它不会进行验证，但应为正确的日期时间。
- ```DataVersion``` 是必需的
- ```MetadataVersion``` 是可选的，如果指定，则它必须是值为的字符串 ```"1"```
- ```Data``` 是可选的，可以为任何 JSON 标记 (number、string、boolean、array、object) 

### <a name="send-batch-of-events-in-custom-schema"></a>在自定义架构) 中发送事件批处理 (

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
- 除了负载大小) 以外，没有其他约束 (。

## <a name="examples"></a>示例

### <a name="set-up-topic-with-eventgrid-schema"></a>用 EventGrid 架构设置主题
设置一个主题，要求在 **eventgridschema**中发布事件。

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
设置主题以要求在中发布事件 `customschema` 。

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
设置主题以要求在中发布事件 `cloudeventschema` 。

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
使用此目标类型可将事件发送到托管 HTTP 终结点) 或网络/internet 上任何 HTTP 可寻址终结点的任何其他模块 (。

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

对属性的约束 `endpointUrl` ：
- 它必须为非 null。
- 它必须是绝对 URL。
- 如果在 EventGridModule 设置中将 outbound__webhook__httpsOnly 设置为 true，则它必须仅为 HTTPS。
- 如果 outbound__webhook__httpsOnly 设置为 "false"，则它可以是 HTTP 或 HTTPS。

对属性的约束 `eventDeliverySchema` ：
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

使用此目标可将事件发送到云中 (Azure) 中的事件网格。 在边缘上创建事件订阅之前，你需要先在云中设置要向其发送事件的用户主题。

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
- `/api/events`必须在请求 URL 路径中定义路径。
- 它必须 `api-version=2018-01-01` 在查询字符串中。
- 如果在 EventGridModule 设置中 outbound__eventgrid__httpsOnly 设置为 true (默认情况下) 为 true，则它必须仅为 HTTPS。
- 如果 outbound__eventgrid__httpsOnly 设置为 "false"，则它可以是 HTTP 或 HTTPS。
- 如果 outbound__eventgrid__allowInvalidHostnames 默认设置为 false (false) ，则它必须面向以下终结点之一：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- 必须为非 null。

TopicName:
- 如果 EventDeliverySchema 设置为 EventGridSchema，则此字段的值将放入每个事件的 "主题" 字段，然后将其转发到云中的事件网格。
- 如果 EventDeliverySchema 设置为 CustomEventSchema，则将忽略此属性，并按接收到的原样转发自定义事件负载。

## <a name="set-up-event-hubs-as-a-destination"></a>将事件中心设置为目标

若要发布到事件中心，请将设置 `endpointType` 为 `eventHub` 并提供：

* connectionString：目标为通过共享访问策略生成的特定事件中心的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 可以通过导航到要在 Azure 门户中发布到的特定事件中心，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，来生成特定于实体的连接字符串。

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

若要发布到服务总线队列，请将设置 `endpointType` 为 `serviceBusQueue` 并提供：

* connectionString：目标为通过共享访问策略生成的特定服务总线队列的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线队列，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，生成特定于实体的连接字符串。

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

若要发布到服务总线主题，请将设置 `endpointType` 为 `serviceBusTopic` 并提供：

* connectionString：目标为通过共享访问策略生成的特定服务总线主题的连接字符串。

    >[!NOTE]
    > 连接字符串必须是实体特定的。 使用命名空间连接字符串将不起作用。 通过导航到要在 Azure 门户中发布到的特定服务总线主题，并单击 " **共享访问策略** " 生成新的特定于实体的 connecection 字符串，生成特定于实体的连接字符串。

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

若要发布到存储队列，请将设置  `endpointType` 为 `storageQueue` 并提供：

* queueName：要发布到的存储队列的名称。
* connectionString：存储队列所在的存储帐户的连接字符串。

    >[!NOTE]
    > Unline 事件中心、服务总线队列和服务总线主题，用于存储队列的连接字符串不特定于实体。 它必须是存储帐户的连接字符串。

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