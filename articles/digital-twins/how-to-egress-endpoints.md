---
title: Azure 数字孪生中的出口和终结点 | Microsoft Docs
description: 有关如何使用 Azure 数字孪生创建终结点的指南
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212244"
---
# <a name="egress-and-endpoints"></a>出口和终结点

Azure 数字孪生支持_终结点_的概念，其中每个终结点均代表用户的 Azure 订阅中的消息/事件代理。 可以将事件和消息发送到**事件中心**、**事件网格**和**服务总线主题**。

事件将根据预定义的路由首选项发送至终结点：用户可以指定应接收以下任一事件的终结点：**TopologyOperation**、**UdfCustom**、**SensorChange**、**SpaceChange** 或 **DeviceMessage**。

若要对事件路由和事件类型有基本了解，请参阅[路由事件和消息](concepts-events-routing.md)。

## <a name="event-types-description"></a>事件类型描述

以下是每个事件类型的事件格式：

- **TopologyOperation**

  适用于图形更改。 *subject* 属性指定受影响的对象类型。 可以触发此事件的对象类型有：**Device**、**DeviceBlobMetadata**、**DeviceExtendedProperty**、**ExtendedPropertyKey**、**ExtendedType**、**KeyStore**、**Report**、**RoleDefinition**、**Sensor**、**SensorBlobMetadata**、**SensorExtendedProperty**、**Space**、**SpaceBlobMetadata**、**SpaceExtendedProperty**、**SpaceResource**、**SpaceRoleAssignment**、**System**、**User**、**UserBlobMetadata**、**UserExtendedProperty**。

  示例：

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourTopicName* | 自定义主题的名称 |

- **UdfCustom**

  由用户定义的函数 (UDF) 发送的事件。 
  
  > [!IMPORTANT]
  > 必须从 UDF 本身显式发送此事件。

  示例：

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourTopicName* | 自定义主题的名称 |

- **SensorChange**

  以遥测更改为基础的传感器状态更新。

  示例：

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourTopicName* | 自定义主题的名称 |

- **SpaceChange**

  以遥测更改为基础的空间状态更新。

  示例：

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
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourTopicName* | 自定义主题的名称 |

- **DeviceMessage**

  允许指定 **EventHub** 连接，原始遥测事件也可从 Azure 数字孪生路由至此。

> [!NOTE]
> - **DeviceMessage** 只能与 **EventHub** 结合使用；不能将 **DeviceMessage** 与任何其他事件类型结合使用。
> - 只能指定一个 **EventHub** 或 **DeviceMessage** 组合类型的终结点。

## <a name="configuring-endpoints"></a>配置终结点

终结点管理通过终结点 API 执行。 下面是如何配置各种受支持终结点的一些示例。 请特别注意事件类型数组，因为此数组会定义终结点的路由：

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- 路由到**服务总线**事件类型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourNamespace* | 终结点的命名空间 |
    | *yourPrimaryKey* | 用于进行身份验证的主要连接字符串 |
    | *yourSecondaryKey* | 用于进行身份验证的次要连接字符串 |
    | *yourTopicName* | 自定义主题的名称 |

- 路由到**事件网格**事件类型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourPrimaryKey* | 用于进行身份验证的主要连接字符串|
    | *yourSecondaryKey* | 用于进行身份验证的次要连接字符串 |
    | *yourTopicName* | 自定义主题的名称 |

- 路由到**事件中心**事件类型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourNamespace* | 终结点的命名空间 |
    | *yourPrimaryKey* | 用于进行身份验证的主要连接字符串 |
    | *yourSecondaryKey* | 用于进行身份验证的次要连接字符串 |
    | *yourEventHubName* | 你的**事件中心**的名称 |

- 路由到**事件中心**事件类型：**DeviceMessage**。 必须在 **connectionString** 中包括 `EntityPath`。

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | 自定义属性名称 | 替换为 |
    | --- | --- |
    | *yourNamespace* | 终结点的命名空间 |
    | *yourPrimaryKey* | 用于进行身份验证的主要连接字符串 |
    | *yourSecondaryKey* | 用于进行身份验证的次要连接字符串 |
    | *yourEventHubName* | 你的**事件中心**的名称 |

> [!NOTE]
> 创建新的终结点时，可能需要 5 到 10 分钟的时间，才能开始在终结点上接收事件。

## <a name="primary-and-secondary-connection-keys"></a>主要和次要连接键

如果主要连接键变成未授权键，则系统会自动尝试次要连接键。 这可提供备份，并允许通过终结点 API 对主键进行正常的身份验证和更新。

如果主要和次要连接键都未经授权，系统将进入最长 30 分钟的指数退避等待时间。 每次触发退避等待时，都会删除事件。

每当系统处于退避等待状态时，通过终结点 API 进行的连接键更新可能需要 30 分钟才能生效。

## <a name="unreachable-endpoints"></a>无法访问的终结点

当终结点变得无法访问时，系统将进入最长 30 分钟的指数退避等待时间。 每次触发退避等待时，都会删除事件。

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Azure 数字孪生 Swagger，请阅读 [Azure 数字孪生 Swagger](how-to-use-swagger.md)。

若要详细了解如何在 Azure 数字孪生中路由事件和消息，请阅读[路由事件和消息](concepts-events-routing.md)。
