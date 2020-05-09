---
title: 计量服务 Api-Microsoft 商业市场
description: 使用情况事件 API 允许在 Microsoft AppSource 和 Azure Marketplace 中发出 SaaS 服务的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 159d2c60fc1fc5ad1f21f2b948208eaae0d06208
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857867"
---
# <a name="marketplace-metering-service-apis"></a>Azure 市场计量服务 API

使用情况事件 API 允许您为特定购买的实体发出使用事件。 使用情况事件请求引用发布产品时发布者定义的计数服务维度。

## <a name="usage-event"></a>使用情况事件

**POST**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`   | [获取 JSON web 令牌（JWT）持有者令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：发出 HTTP 请求时，将从`Bearer`引用的链接中获取令牌的前缀。 |

*需要*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>响应

代码：200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

代码：400 <br>
错误的请求、所提供或已过期的数据缺失或无效

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

代码：403<br>
错误的请求、所提供或已过期的数据缺失或无效

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

代码：409<br>
冲突，当我们接收到使用情况资源 ID 的使用情况，并且有效使用已存在的情况下。 响应将包含`additionalInfo`包含有关已接受消息的信息的字段。

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>批次使用事件

使用 batch 用量事件 API，可以一次为多个已购买的实体发出使用事件。 批次使用情况事件请求引用发布产品时发布者定义的计数服务维度。

>[!Note]
>你可以在 Microsoft 的商业应用商店中注册多个 SaaS 产品/服务。 每个注册的 SaaS 产品都有一个唯一的 Azure AD 应用程序，该应用程序注册用于身份验证和授权。 在批次中发出的事件应该属于具有相同 Azure AD 应用程序的产品/服务。

**POST：**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，则将生成一个，并在响应标头中提供。 |
| `authorization`      | [获取 JSON web 令牌（JWT）持有者令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：发出 HTTP 请求时，将从`Bearer`引用的链接中获取令牌的前缀。  |

*需要*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>响应

代码：200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

API 响应中`BatchUsageEvent`引用的状态代码的说明：

| 状态代码  | 说明 |
| ---------- | -------------------- |
| `Accepted` | 接受的代码。 |
| `Expired` | 使用已过期。 |
| `Duplicate` | 提供了重复的用法。 |
| `Error` | 错误代码。 |
| `ResourceNotFound` | 提供的使用资源无效。 |
| `ResourceNotAuthorized` | 你无权提供此资源的使用情况。 |
| `InvalidDimension` | 对于此产品/计划，使用的维度无效。 |
| `InvalidQuantity` | 传递的数量为 < 0。 |
| `BadArgument` | 输入缺少或格式不正确。 |

代码：400<br>
错误的请求、所提供或已过期的数据缺失或无效

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
代码：403<br>
用户未经授权，无法进行此调用

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[SaaS](./saas-metered-billing.md)计费。
