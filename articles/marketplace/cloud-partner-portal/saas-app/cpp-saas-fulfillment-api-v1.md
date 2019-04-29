---
title: SaaS 履行 API V1-Azure 应用商店 |Microsoft Docs
description: 介绍如何创建使用相关联的履行 V1 Api 在 Azure marketplace SaaS 产品/服务。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 03/28/2019
ms.author: pbutlerm
ROBOTS: NOINDEX
ms.openlocfilehash: 4908233280c69a37ea470eed2ef077cb220a7930
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101092"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>SaaS 履行 Api 版本 1 （已弃用）

本文介绍如何使用 API 创建 SaaS 套餐。 不需要允许对 SaaS 产品/服务的订阅，如果你已通过所选的 Azure 销售的产品的 Api，REST 方法和终结点，组成。  

> [!WARNING]
> 此初始版本的 SaaS 履行 API 已弃用;请改用[SaaS 履行 API V2](./cpp-saas-fulfillment-api-v2.md)。  当前正在维护此 API 仅用于现有发布服务器。 

以下 API 可帮助你将 SaaS 服务与 Azure 相集成：

-   解决
-   订阅
-   转换
-   取消订阅


## <a name="api-methods-and-endpoints"></a>API 方法和终结点

以下部分介绍可用于为 SaaS 套餐启用订阅的 API 方法和终结点。


### <a name="marketplace-api-endpoint-and-api-version"></a>市场 API 终结点和 API 版本

Azure 市场 API 的终结点为 `https://marketplaceapi.microsoft.com`。

当前 API 版本为 `api-version=2017-04-15`。


### <a name="resolve-subscription"></a>解析订阅

用户针对解析终结点执行 POST 操作可将市场令牌解析为永久性资源 ID。  资源 ID 是 SAAS 订阅的唯一标识符。 

当用户重定向到 ISV 的网站时，URL 将在查询参数中包含一个令牌。 ISV 应当使用此令牌，并发出请求来解析它。 响应包含资源的唯一 SAAS 订阅 ID、名称、产品/服务 ID 和计划。 此令牌的有效期仅为一小时。

*请求*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **参数名称** |     **说明**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  用于此请求的操作的版本。   |
|  |  |


*标头*

| **标头密钥**     | **必需** | **说明**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 否           | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
| x-ms-correlationid | 否           | 在客户端上执行的操作的唯一字符串值。 此值将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| Content-type       | 是          | `application/json`                                        |
| authorization      | 是          | JSON Web 令牌 (JWT) 持有者令牌。                    |
| x-ms-marketplace-token| 是| 将用户从 Azure 重定向到 SaaS ISV 网站时 URL 中的令牌查询参数。 **注意：** 此令牌仅在 1 小时内有效。 此外，URL 会对来自浏览器的令牌值进行解码，然后再使用它。|
|  |  |  |
  

响应正文

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **参数名称** | **数据类型** | **说明**                       |
|--------------------|---------------|---------------------------------------|
| id                 | String        | SaaS 订阅的 ID。          |
| subscriptionName| String| 用户订阅 SaaS 服务时在 Azure 中设置的 SaaS 订阅名称。|
| OfferId            | String        | 用户订阅的套餐 ID。 |
| planId             | String        | 用户订阅的计划 ID。  |
|  |  |  |


响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | 已成功解析令牌。                                                            |
| 400                  | `BadRequest`         | 缺少必需的标头，或指定了无效的 api-version。 无法解析令牌，因为其中任意一个令牌格式不正确或已过期（令牌仅在生成后的 1 小时内有效）。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                                 |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                                |
| 503                  | `ServiceUnavailable` | 服务暂时关闭，请稍后重试。                                        |
|  |  |  |


响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此 ID 用于任何对帐单。 |
| Retry-After        | 否           | 仅为 429 响应设置此值。                                                                   |
|  |  |  |


### <a name="subscribe"></a>订阅

订阅终结点允许用户开始订阅给定计划的 SaaS 服务，并在商务系统中启用计费。

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | 解决通过解决 API 令牌后获取的唯一 ID 的 SaaS 订阅。                              |
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

|  **标头密钥**        | **必需** |  **说明**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   否         | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| x-ms-correlationid     |   否         | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| If-Match/If-None-Match |   否         |   强验证程序 ETag 值。                                                          |
| content-type           |   是        |    `application/json`                                                                   |
|  authorization         |   是        |    JSON Web 令牌 (JWT) 持有者令牌。                                               |
| x-ms-marketplace-session-mode| 否 | 订阅 SaaS 套餐时用于启用试运行模式的标志。 如果已设置，则订阅是免费的。 此值可用于 ISV 测试方案。 请将其设置为 **‘dryrun’**|
|  |  |  |

*正文*

``` json
{
    "lanId": "",
}
```

| **元素名称** | **数据类型** | **说明**                      |
|------------------|---------------|--------------------------------------|
| planId           | （必需）字符串        | 用户订阅的 SaaS 服务的计划 ID。  |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 收到的给定计划的 SaaS 订阅激活码。                   |
| 400                  | `BadRequest`         | 缺少必需的标头，或 JSON 正文的格式不正确。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                   |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                  |
| 409                  | `Conflict`           | 订阅中正在执行另一项操作。                     |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                  |
| 503                  | `ServiceUnavailable` | 服务暂时关闭，请稍后重试。                          |
|  |  |  |

对于 202 响应，请跟踪“Operation-location”标头中的请求操作状态。 身份验证与其他市场 API 相同。

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 是          | 客户端检查状态的间隔。                                                       |
| Operation-Location | 是          | 链接到资源以获取操作状态。                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>更改计划终结点

用户可以使用更改终结点将其当前已订阅的计划转换为新计划。

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 订阅的 ID。                              |
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

| **标头密钥**          | **必需** | **说明**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | 否           | 唯一的字符串值，用于跟踪来自客户端的请求。 建议使用 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。   |
| x-ms-correlationid      | 否           | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| If-Match /If-None-Match | 否           | 强验证程序 ETag 值。                              |
| content-type            | 是          | `application/json`                                        |
| authorization           | 是          | JSON Web 令牌 (JWT) 持有者令牌。                    |
|  |  |  |

*正文*

```json
{
    "planId": ""
}
```

|  **元素名称** |  **数据类型**  | **说明**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  （必需）字符串         | 用户订阅的 SaaS 服务的计划 ID。          |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 收到的给定计划的 SaaS 订阅激活码。                   |
| 400                  | `BadRequest`         | 缺少必需的标头，或 JSON 正文的格式不正确。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                   |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                  |
| 409                  | `Conflict`           | 订阅中正在执行另一项操作。                     |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                  |
| 503                  | `ServiceUnavailable` | 服务暂时关闭，请稍后重试。                          |
|  |  |  |

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 是          | 客户端检查状态的间隔。                                                       |
| Operation-Location | 是          | 链接到资源以获取操作状态。                                                        |
|  |  |  |

### <a name="delete-subscription"></a>删除订阅

用户对订阅终结点执行 Delete 操作可以删除具有给定 ID 的订阅。

*请求*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 订阅的 ID。                              |
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

| **标头密钥**     | **必需** | **说明**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | 否           | 唯一的字符串值，用于跟踪来自客户端的请求。 建议使用 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。                                                           |
| x-ms-correlationid | 否           | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| authorization      | 是          | JSON Web 令牌 (JWT) 持有者令牌。                    |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 收到的给定计划的 SaaS 订阅激活码。                   |
| 400                  | `BadRequest`         | 缺少必需的标头，或 JSON 正文的格式不正确。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                   |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                  |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                  |
| 503                  | `ServiceUnavailable` | 服务暂时关闭。 请稍后重试。                          |
|  |  |  |

对于 202 响应，请跟踪“Operation-location”标头中的请求操作状态。 身份验证与其他市场 API 相同。

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 是          | 客户端检查状态的间隔。                                                       |
| Operation-Location | 是          | 链接到资源以获取操作状态。                                                        |
|   |  |  |

### <a name="get-operation-status"></a>获取操作状态

用户可以使用此终结点跟踪已触发的异步操作（订阅/取消订阅/更改计划）的状态。

*请求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | 触发的操作的唯一 ID。                |
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

| **标头密钥**     | **必需** | **说明**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 否           | 唯一的字符串值，用于跟踪来自客户端的请求。 建议使用 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。   |
| x-ms-correlationid | 否           | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
| authorization      | 是          | JSON Web 令牌 (JWT) 持有者令牌。                    |
|  |  |  | 

响应正文

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **参数名称** | **数据类型** | **说明**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | String        | 操作的 ID。                                                                      |
| status             | 枚举          | 操作状态，值为下列其中一项：`In Progress`、`Succeeded` 或 `Failed`。          |
| resourceLocation   | String        | 链接到已创建或修改的订阅。 这可以帮助客户端获取更新的状态发布操作。 不会对 `Unsubscribe` 操作设置此值。 |
| created            | DateTime      | 操作创建时间 (UTC)。                                                           |
| lastModified       | DateTime      | 上次更新操作的时间 (UTC)。                                                      |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 Get 请求，且正文包含响应。    |
| 400                  | `BadRequest`         | 缺少必需的标头，或指定了无效的 api-version。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                      |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                     |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                     |
| 503                  | `ServiceUnavailable` | 服务暂时关闭，请稍后重试。                             |
|  |  |  |

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 是          | 客户端检查状态的间隔。                                                       |
|  |  |  |

### <a name="get-subscription"></a>获取订阅

用户对订阅终结点执行 Get 操作可以检索具有给定资源标识符的订阅。

*请求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 订阅的 ID。                              |
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

| **标头密钥**     | **必需** | **说明**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 否           | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。                                                           |
| x-ms-correlationid | 否           | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| authorization      | 是          | JSON Web 令牌 (JWT) 持有者令牌。                                                                    |
|  |  |  |

响应正文

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **参数名称**     | **数据类型** | **说明**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Azure 中 SaaS 订阅资源的 ID。    |
| offerId                | String        | 用户订阅的套餐 ID。         |
| planId                 | String        | 用户订阅的计划 ID。          |
| saasSubscriptionName   | String        | SaaS 订阅的名称。                |
| saasSubscriptionStatus | 枚举          | 操作状态。  下列类型作之一：  <br/> - `Subscribed`：订阅处于活动状态。  <br/> - `Pending`：用户创建了资源，但 ISV 尚未激活该资源。   <br/> - `Unsubscribed`：用户已取消订阅。   <br/> - `Suspended`：用户已暂停订阅。   <br/> - `Deactivated`：Azure 订阅已暂停。  |
| created                | DateTime      | 订阅创建时间戳值 (UTC)。 |
| lastModified           | DateTime      | 订阅修改时间戳值 (UTC)。 |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 Get 请求，且正文包含响应。    |
| 400                  | `BadRequest`         | 缺少必需的标头，或指定了无效的 api-version。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                      |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                     |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                     |
| 503                  | `ServiceUnavailable` | 服务暂时关闭，请稍后重试。                             |
|  |  |  |

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 否           | 客户端检查状态的间隔。                                                       |
| eTag               | 是          | 链接到资源以获取操作状态。                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>获取订阅

用户对订阅终结点执行 Get 操作可以检索 ISV 提供的所有套餐的所有订阅。

*请求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **参数名称**  | **说明**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | 用于此请求的操作的版本。 |
|  |  |

*标头*

| **标头密钥**     | **必需** | **说明**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 否           | 唯一的字符串值，用于跟踪来自客户端的请求。 建议使用 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。             |
| x-ms-correlationid | 否           | 在客户端上执行的操作的唯一字符串值。 此值用于将客户端操作生成的所有事件与服务器端的事件相关联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| authorization      | 是          | JSON Web 令牌 (JWT) 持有者令牌。                    |
|  |  |  |

响应正文

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **参数名称**     | **数据类型** | **说明**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | Azure 中 SaaS 订阅资源的 ID。    |
| offerId                | String        | 用户订阅的套餐 ID。         |
| planId                 | String        | 用户订阅的计划 ID。          |
| saasSubscriptionName   | String        | SaaS 订阅的名称。                |
| saasSubscriptionStatus | 枚举          | 操作状态。  下列类型作之一：  <br/> - `Subscribed`：订阅处于活动状态。  <br/> - `Pending`：用户创建了资源，但 ISV 尚未激活该资源。   <br/> - `Unsubscribed`：用户已取消订阅。   <br/> - `Suspended`：用户已暂停订阅。   <br/> - `Deactivated`：Azure 订阅已暂停。  |
| created                | DateTime      | 订阅创建时间戳值 (UTC)。 |
| lastModified           | DateTime      | 订阅修改时间戳值 (UTC)。 |
|  |  |  |

响应代码

| **HTTP 状态代码** | **错误代码**     | **说明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 Get 请求，且正文包含响应。    |
| 400                  | `BadRequest`         | 缺少必需的标头，或指定了无效的 api-version。 |
| 403                  | `Forbidden`          | 调用方无权执行此操作。                      |
| 404                  | `NotFound`           | 找不到具有给定 ID 的订阅                                     |
| 429                  | `RequestThrottleId`  | 服务正忙于处理请求，请稍后重试。                     |
| 503                  | `ServiceUnavailable` | 服务暂时关闭。 请稍后重试。                             |
|  |  |  |

响应标头

| **标头密钥**     | **必需** | **说明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 从客户端收到的请求 ID。                                                                   |
| x-ms-correlationid | 是          | 如果由客户端传递，则为关联 ID，否则此值为服务器关联 ID。                   |
| x-ms-activityid    | 是          | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。 |
| Retry-After        | 否           | 客户端检查状态的间隔。                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

SaaS webhook 用于主动将更改通知给 SaaS 服务。 此 POST API 应当不进行身份验证并由 Microsoft 服务调用。 在对 webhook 通知采取操作之前，SaaS 服务应当调用操作 API 来进行验证和授权。 

*正文*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **参数名称**     | **数据类型** | **说明**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | 触发的操作的唯一 ID。                |
| activityId   | String        | 唯一的字符串值，用于跟踪来自服务的请求。 此值用于任何核对操作。               |
| subscriptionId                     | String        | Azure 中 SaaS 订阅资源的 ID。    |
| offerId                | String        | 用户订阅的套餐 ID。 仅随“更新”操作提供。        |
| publisherId                | String        | SaaS 产品/服务的发布者 ID         |
| planId                 | String        | 用户订阅的计划 ID。 仅随“Update”操作提供。          |
| action                 | String        | 触发此通知的操作。 可能的值 - Activate、Delete、Suspend、Reinstate、Update          |
| timeStamp                 | String        | 此通知的触发时间的时间戳值（采用 UTC 格式）。          |
|  |  |  |


## <a name="next-steps"></a>后续步骤

开发人员可以以编程方式检索和操作工作负荷，产品/服务，并且发布者配置文件使用[云合作伙伴门户 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
