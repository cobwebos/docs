---
title: 应用商店计量服务 API |Azure 应用商店
description: Azure 应用商店中 SaaS 提供的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275741"
---
# <a name="marketplace-metering-service-apis"></a>Azure 市场计量服务 API

使用事件 API 允许您为特定采购的实体发出使用事件。 使用事件请求引用发布者在发布产品/服务时定义的计量服务维度。

## <a name="usage-event"></a>使用事件

**帖子**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为 2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 用于客户端操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。 |
| `authorization`   | [获取 JSON Web 令牌 （JWT） 无记名令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：在发出 HTTP 请求时`Bearer`，前缀从引用的链接获取的令牌。 |

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

代码： 200<br>
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

代码： 400 <br>
请求错误、提供或无效的数据或过期

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

代码： 403<br>
请求错误、提供或无效的数据或过期

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

代码： 409<br>
冲突，当我们收到使用资源 ID 的使用调用时，以及已经存在的有效使用情况。 响应将包含`additionalInfo`包含有关接受邮件的信息的字段。

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

## <a name="batch-usage-event"></a>批处理使用事件

批处理使用事件 API 允许您一次为多个采购实体发出使用事件。 批处理使用情况事件请求引用发布者在发布产品/服务时定义的计量服务维度。

>[!Note]
>您可以在 Microsoft 的商业市场注册多个 SaaS 产品/ 每个已注册的 SaaS 产品/服务都有一个为身份验证和授权目的注册的唯一 Azure AD 应用程序。 在注册产品/服务时，批量发出的事件应属于具有相同 Azure AD 应用程序的优惠。

**帖子：**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查询参数：*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 用于此请求的操作的版本。 最新 API 版本为 2018-08-31。 |

*请求标头：*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，将生成一个值，并在响应标头中提供。 |
| `x-ms-correlationid` | 用于客户端操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将生成一个值，并在响应标头中提供。 |
| `authorization`      | [获取 JSON Web 令牌 （JWT） 无记名令牌。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：在发出 HTTP 请求时`Bearer`，前缀从引用的链接获取的令牌。  |

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

代码： 200<br>
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

| 状态代码  | 描述 |
| ---------- | -------------------- |
| `Accepted` | 已接受代码。 |
| `Expired` | 过期使用。 |
| `Duplicate` | 提供重复用法。 |
| `Error` | 错误代码。 |
| `ResourceNotFound` | 提供的使用资源无效。 |
| `ResourceNotAuthorized` | 您无权提供此资源的使用情况。 |
| `InvalidDimension` | 传递使用量的维度对于此产品/计划无效。 |
| `InvalidQuantity` | 通过的数量为 0 <。 |
| `BadArgument` | 输入丢失或格式不正确。 |

代码： 400<br>
错误请求、提供的数据丢失或无效或已过期

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
代码： 403<br>
用户未经授权进行此呼叫

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[SaaS 计量计费](./saas-metered-billing.md)。
