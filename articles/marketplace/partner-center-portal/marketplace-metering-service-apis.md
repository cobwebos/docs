---
title: 计量服务 API - Microsoft 商业市场
description: 通过使用事件 API，可以发出 Microsoft AppSource 和 Azure 市场中 SaaS 产品/服务的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725459"
---
# <a name="marketplace-metering-service-apis"></a>Azure 市场计量服务 API

通过使用事件 API，可以发出特定已购实体的使用事件。 使用事件请求引用由发布者在发布产品时定义的计量服务维度。

## <a name="usage-event"></a>使用事件

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为 2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`   | [获取 JSON Web 令牌（JWT）持有者令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：发出 HTTP 请求时，向由引用的链接获取的令牌添加前缀 `Bearer`。 |

>[!Note]
>对于 Azure 应用程序托管应用计划，`resourceId` 是在托管应用元数据对象的 `billingDetails` 下找到的 `resourceUsageId`。  可在[使用 Azure 管理的标识令牌](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到用于提取它的示例脚本。  对于 SaaS 产品/服务，`resourceId` 是 SaaS 订阅 ID。  有关 SaaS 订阅的更多详细信息，请参阅[列出订阅](./pc-saas-fulfillment-api-v2.md#list-subscriptions)。

*请求：*

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
错误的请求，未提供数据或提供了无效的数据，或者已过期

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
错误的请求，未提供数据或提供了无效的数据，或者已过期

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

代码：409<br>
接收对使用资源 ID 的使用量调用时与有效使用量存在冲突，有效使用量已存在。 响应将包含 `additionalInfo` 字段，其中包含有关已接受消息的信息。

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

## <a name="batch-usage-event"></a>批量使用事件

通过批量使用事件 API，可以同时发出多个已购实体的使用事件。 批量使用事件请求引用由发布者在发布产品时定义的计量服务维度。

>[!Note]
>可在 Microsoft 的商城中注册多个 SaaS 产品/服务。 每项已注册的 SaaS 产品/服务都有注册用于身份验证和授权的唯一 Azure AD 应用程序。 注册产品/服务时，批量发出的事件应属于具有相同 Azure AD 应用程序的产品/服务。

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为 2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `authorization`      | [获取 JSON Web 令牌（JWT）持有者令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：发出 HTTP 请求时，向由引用的链接获取的令牌添加前缀 `Bearer`。  |

*请求：*
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

`BatchUsageEvent` API 响应中引用状态代码的说明：

| 状态代码  | 说明 |
| ---------- | -------------------- |
| `Accepted` | 已接受代码。 |
| `Expired` | 使用已过期。 |
| `Duplicate` | 提供了重复的使用情况。 |
| `Error` | 错误代码。 |
| `ResourceNotFound` | 提供的使用资源无效。 |
| `ResourceNotAuthorized` | 你无权提供此资源的使用情况。 |
| `InvalidDimension` | 用于传递使用情况的维度对于此产品/服务或计划无效。 |
| `InvalidQuantity` | 传递的数量 < 0。 |
| `BadArgument` | 缺少输入，或输入格式不正确。 |

代码：400<br>
错误的请求，未提供数据或提供了无效的数据，或者已过期

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

有关详细信息，请参阅 [SaaS 按流量计费](./saas-metered-billing.md)。
