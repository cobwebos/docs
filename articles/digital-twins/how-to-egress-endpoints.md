---
title: Azure 数字孪生中的出口和终结点 | Microsoft Docs
description: 有关如何使用 Azure 数字孪生创建终结点的指南。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924799"
---
# <a name="egress-and-endpoints"></a>出口和终结点

Azure 数字孪生*终结点*在用户的 Azure 订阅中提供消息或事件代理。 可以将事件和消息发送到 Azure 事件中心、Azure 事件网格和 Azure 服务总线主题。

事件根据预定义的路由首选项传送到终结点。 用户指定每个终结点可以接收哪些*事件类型*。

若要详细了解事件、路由和事件类型，请参阅[在 Azure 数字孪生中路由事件和消息](./concepts-events-routing.md)。

## <a name="events"></a>活动

事件由 IoT 对象（例如设备和传感器）发送，由 Azure 消息和事件代理进行处理。 事件是通过以下 [Azure 事件网格事件架构参考](../event-grid/event-schema.md)定义的。

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 属性 | Type | 描述 |
| --- | --- | --- |
| id | string | 事件的唯一标识符。 |
| subject | string | 事件主题的发布者定义路径。 |
| data | 对象 | 特定于资源提供程序的事件数据。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| eventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |
| topic | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |

有关事件网格事件架构的详细信息：

- 请查看 [Azure 事件网格事件架构参考](../event-grid/event-schema.md)。
- 请阅读 [Azure 事件网格 Node.js SDK EventGridEvent 参考](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest)。

## <a name="event-types"></a>事件类型

事件类型对事件的性质进行分类并且是在 **eventType** 字段中设置的。 以下列表提供了可用的事件类型：

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

以下各个子部分将进一步介绍每个事件类型的事件格式。

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** 适用于图形更改。 **subject** 属性指定受影响的对象类型。 以下类型的对象可能会触发此事件：

- 设备
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- 报表
- RoleDefinition
- 传感器
- SensorBlobMetadata
- SensorExtendedProperty
- 空格
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- 系统
- 用户
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>示例

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_TOPIC_NAME | 自定义主题的名称 |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** 是用户定义的函数 (UDF) 发送的事件。
  
> [!IMPORTANT]  
> 必须从 UDF 本身显式发送此事件。

#### <a name="example"></a>示例

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_TOPIC_NAME | 自定义主题的名称 |

### <a name="sensorchange"></a>SensorChange

**SensorChange** 是基于遥测更改对传感器状态所做的更新。

#### <a name="example"></a>示例

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_TOPIC_NAME | 自定义主题的名称 |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** 是基于遥测更改对空间状态所做的更新。

#### <a name="example"></a>示例

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_TOPIC_NAME | 自定义主题的名称 |

### <a name="devicemessage"></a>DeviceMessage

使用 **DeviceMessage** 可以指定 **EventHub** 连接，原始遥测事件也可从 Azure 数字孪生路由至此。

> [!NOTE]
> - **DeviceMessage** 只能与 **EventHub** 组合。 不能将 **DeviceMessage** 与其他任何事件类型组合。
> - 只能指定一个 **EventHub** 或 **DeviceMessage** 组合类型的终结点。

## <a name="configure-endpoints"></a>配置终结点

终结点管理通过终结点 API 执行。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

以下示例演示了如何配置受支持的终结点。

>[!IMPORTANT]
> 请特别注意 **eventTypes** 属性。 它定义终结点处理哪些事件类型并确定其路由。

针对以下项发出的经过身份验证的 HTTP POST 请求

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- 路由到服务总线事件类型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_NAMESPACE | 终结点的命名空间 |
    | YOUR_PRIMARY_KEY | 用于进行身份验证的主要连接字符串 |
    | YOUR_SECONDARY_KEY | 用于进行身份验证的次要连接字符串 |
    | YOUR_TOPIC_NAME | 自定义主题的名称 |

- 路由到事件网格事件类型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_PRIMARY_KEY | 用于进行身份验证的主要连接字符串|
    | YOUR_SECONDARY_KEY | 用于进行身份验证的次要连接字符串 |
    | YOUR_TOPIC_NAME | 自定义主题的名称 |

- 路由到事件中心事件类型 **SensorChange**、**SpaceChange** 和 **TopologyOperation**：

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_NAMESPACE | 终结点的命名空间 |
    | YOUR_PRIMARY_KEY | 用于进行身份验证的主要连接字符串 |
    | YOUR_SECONDARY_KEY | 用于进行身份验证的次要连接字符串 |
    | YOUR_EVENT_HUB_NAME | 事件中心的名称 |

- 路由到事件中心事件类型 **DeviceMessage**。 必须在 **connectionString** 中包含 `EntityPath`：

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_NAMESPACE | 终结点的命名空间 |
    | YOUR_PRIMARY_KEY | 用于进行身份验证的主要连接字符串 |
    | YOUR_SECONDARY_KEY | 用于进行身份验证的次要连接字符串 |
    | YOUR_EVENT_HUB_NAME | 事件中心的名称 |

> [!NOTE]  
> 创建新的终结点时，可能需要 5 到 10 分钟的时间，才能开始在终结点上接收事件。

## <a name="primary-and-secondary-connection-keys"></a>主要和次要连接键

如果主要连接键变成未授权键，则系统会自动尝试次要连接键。 这可提供备份，并允许通过终结点 API 对主键进行正常的身份验证和更新。

如果主要和次要连接键都未经授权，系统将进入最长 30 分钟的指数退避等待时间。 每次触发退避等待时，都会删除事件。

每当系统处于退避等待状态时，通过终结点 API 进行的连接键更新可能需要 30 分钟才能生效。

## <a name="unreachable-endpoints"></a>无法访问的终结点

当终结点不可访问时，系统将进入最长 30 分钟的指数退避等待时间。 每次触发退避等待时，都会删除事件。

## <a name="next-steps"></a>后续步骤

- 了解[如何使用 Azure 数字孪生 Swagger](how-to-use-swagger.md)。

- 详细了解如何在 Azure 数字孪生中[路由事件和消息](concepts-events-routing.md)。
