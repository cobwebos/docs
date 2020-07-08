---
title: 计量服务 API - Microsoft 商业市场
description: 通过使用事件 API，可以发出 Microsoft AppSource 和 Azure 市场中 SaaS 产品/服务的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 7bc5dc0e21bc9218c0e67b4e8e96299d73628e97
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963166"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace 计费 Api

当发布者为要在合作伙伴中心发布的产品/服务创建自定义计量维度时，应使用按流量计费的 Api。 对于具有一个或多个具有自定义维度的计划，以发出使用情况事件，需要与按流量计费 Api 集成。

有关为 SaaS 创建自定义计量维度的详细信息，请参阅[saas](saas-metered-billing.md)计费。

有关使用托管应用计划为 Azure 应用程序提供创建自定义计量维度的详细信息，请参阅[创建新的 Azure 应用服务中的技术配置部分](create-new-azure-apps-offer.md#technical-configuration)。

## <a name="enforcing-tls-12-note"></a>强制执行 TLS 1.2 说明

TLS 版本1.2 版本作为 HTTPS 通信的最小版本强制执行。 请确保在代码中使用此 TLS 版本。 TLS 版本1.0 和1.1 已弃用，将拒绝连接尝试。

## <a name="metered-billing-single-usage-event"></a>计费单用量事件

使用情况事件 API 应由发布服务器调用，以针对特定客户购买的计划发出对活动资源（已订阅）的使用情况事件。 发布产品/服务时，将为发布者定义的计划的每个自定义维度单独发出使用情况事件。

对于一个日历日的每个小时，只能发出一个使用事件。 例如，在今天的8：15am，可以发出一个使用事件。 如果接受此事件，则将从今天上午9:00 接受下一次使用事件。 如果在今天的8:15 和8:59:59 之间发送额外事件，则会被拒绝为重复。 应该累计一小时内消耗的所有单位，然后在单个事件中发出。

每个资源的日历日每小时只能发出一个使用事件。 如果在一小时内消耗了多个单位，则累积在一小时内消耗的所有单位，然后在单个事件中发出。 只能在过去24小时内发出使用情况事件。 如果你在8:00 和8:59:59 之间的任何时间发出使用事件（并且它被接受）并在8:00 与8:59:59 之间为同一天发送额外事件，则该事件将被拒绝为重复。

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查询参数：*

| 参数 | 建议          |
| ---------- | ---------------------- |
| `ApiVersion` | 使用2018-08-31。 |
| | |

*请求标头：*

| Content-type       | 使用 `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| `authorization`   | 标识进行此 API 调用的 ISV 的唯一访问令牌。 `"Bearer <access_token>"`当发布服务器检索令牌值时，格式为，如中所述。 <br> <ul> <li> SaaS[使用 HTTP POST 获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [身份验证策略](./marketplace-metering-service-authentication.md)中的托管应用程序。 </li> </ul> |
| | |

*请求正文示例：*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId`对于 SaaS 应用和发出自定义计量的托管应用具有不同的含义。 

对于 Azure 应用程序托管应用计划，`resourceId` 是在托管应用元数据对象的 `billingDetails` 下找到的 `resourceUsageId`。 可在[使用 Azure 管理的标识令牌](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到用于提取它的示例脚本。 

对于 SaaS 产品/服务，`resourceId` 是 SaaS 订阅 ID。 有关 SaaS 订阅的更多详细信息，请参阅[列出订阅](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>响应

代码：200<br>
没问题。 已接受并记录在 Microsoft 端的使用情况，以便进行进一步的处理和计费。

响应负载示例： 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

代码：400 <br>
请求错误。

* 提供的请求数据丢失或无效。
* `effectiveStartTime`过去24小时以上。 事件已过期。
* SaaS 订阅未处于订阅状态。

响应负载示例： 

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

已禁止。 未提供授权令牌，该令牌无效或已过期。  或者，请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的订阅，该订阅与用于创建授权令牌的 ID 不同。

代码：409<br>
冲突。 已为指定的资源 ID （有效使用日期和小时）成功报告了使用事件。

响应负载示例： 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>按流量计费的批次使用事件

Batch 用量事件 API 允许一次为多个已购买的资源发出使用事件。 它还允许您为同一资源发出几个使用事件，前提是它们适用于不同的日历小时。 单个批处理中的最大事件数为25。

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查询参数：*

| 参数  | 建议     |
| ---------- | -------------------- |
| `ApiVersion` | 使用2018-08-31。 |

*请求标头：*

| Content-type       | 使用 `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用于跟踪来自客户端的请求的唯一字符串值，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `x-ms-correlationid` | 客户端上的操作的唯一字符串值。 此参数将来自客户端操作的所有事件与服务器端的事件关联起来。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| `authorization`      | 标识进行此 API 调用的 ISV 的唯一访问令牌。 `Bearer <access_token>`当发布服务器检索令牌值时，格式为，如中所述。 <br> <ul> <li> SaaS[使用 HTTP POST 获取令牌](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [身份验证策略](./marketplace-metering-service-authentication.md)中的托管应用程序。 </li> </ul> |
| | |


*请求正文示例：*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId`对于 SaaS 应用和发出自定义计量的托管应用具有不同的含义。 

对于 Azure 应用程序托管应用计划，`resourceId` 是在托管应用元数据对象的 `billingDetails` 下找到的 `resourceUsageId`。 可在[使用 Azure 管理的标识令牌](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到用于提取它的示例脚本。 

对于 SaaS 产品/服务，`resourceId` 是 SaaS 订阅 ID。 有关 SaaS 订阅的更多详细信息，请参阅[列出订阅](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>响应

代码：200<br>
没问题。 已接受并记录在 Microsoft 端的 batch 使用情况，以便进行进一步的处理和计费。 返回响应列表，其中包含批处理中每个单独事件的状态。 应循环访问响应负载，以了解在批处理事件中发送的每个单独使用事件的响应。

响应负载示例： 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

`BatchUsageEvent` API 响应中引用状态代码的说明：

| 状态代码  | 说明 |
| ---------- | -------------------- |
| `Accepted` | 已接受。 |
| `Expired` | 使用已过期。 |
| `Duplicate` | 提供了重复的使用情况。 |
| `Error` | 错误代码。 |
| `ResourceNotFound` | 提供的使用资源无效。 |
| `ResourceNotAuthorized` | 你无权提供此资源的使用情况。 |
| `InvalidDimension` | 用于传递使用情况的维度对于此产品/服务或计划无效。 |
| `InvalidQuantity` | 传递的数量低于或等于0。 |
| `BadArgument` | 缺少输入，或输入格式不正确。 |

代码：400<br>
请求错误。 批处理包含25个以上的使用情况事件。

代码：403<br>
已禁止。 未提供授权令牌，该令牌无效或已过期。  或者，请求尝试访问使用不同 Azure AD 应用 ID 发布的产品/服务的订阅，该订阅与用于创建授权令牌的 ID 不同。

## <a name="development-and-testing-best-practices"></a>开发和测试最佳做法

若要测试自定义计量器，请实现与计量 API 的集成，为已发布的 SaaS 产品/服务创建计划，并在其中定义自定义维度，每个单位的价格为零。 并发布此产品/服务作为预览，因此只有受限用户才能访问和测试集成。

你还可以将私有计划用于现有的实时服务，以限制在测试到有限受众的过程中对此计划的访问。

## <a name="get-support"></a>获取支持

按照对[合作伙伴中心中的商业市场计划的支持](./support.md)中的说明，了解发布者支持选项，并与 Microsoft 建立支持票证。

## <a name="next-steps"></a>后续步骤

有关计量服务 Api 的详细信息，请参阅[Marketplace 计量服务 API 常见问题解答](./marketplace-metering-service-apis-faq.md)。
