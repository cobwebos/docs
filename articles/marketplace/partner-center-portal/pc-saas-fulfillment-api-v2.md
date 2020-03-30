---
title: SaaS 履行 API v2 |Azure 应用商店
description: 本文介绍如何使用关联的履行 v2 API 在 AppSource 和 Azure 应用商店上创建和管理 SaaS 产品/
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275758"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行 API 第 2 版 

本文详细介绍了使合作伙伴能够在 AppSource 市场和 Azure 应用商店中销售其 SaaS 应用程序的 API。 这些 API 是 AppSource 和 Azure 应用商店上可交易 SaaS 产品的要求。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 订阅生命周期

Azure SaaS 管理 SaaS 订阅购买的整个生命周期。 它使用履行 API 作为一种机制，以推动与合作伙伴的实际履行、计划更改和删除订阅。 客户的帐单基于 Microsoft 维护的 SaaS 订阅的状态。 下图描述了驱动状态之间更改状态和操作。

![SaaS 订阅生命周期状态](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下表列出了 SaaS 订阅的预配状态，包括每个订阅的说明和序列图（如果适用）。 

#### <a name="provisioning"></a>设置

当客户发起购买时，合作伙伴将在使用 URL 参数的客户交互式网页上的授权代码中接收此信息。 例如`https://contoso.com/signup?token=..`，合作伙伴中心的着陆页 URL 为`https://contoso.com/signup`。 可以通过调用 Resolve API 来验证和交换授权代码，以了解预配服务的详细信息。  当 SaaS 服务完成预配时，它会发送激活呼叫，以指示履行已完成，并且可以向客户收费。 

下图显示了预配方案的 API 调用顺序。  

![API 调用预配 SaaS 服务](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已预配

此状态是预配服务的稳定状态。

##### <a name="provisioning-for-update"></a>为更新预配 

此状态表示对现有服务的更新处于挂起状态。 此类更新可以由客户从市场或 SaaS 服务（仅适用于直接到客户交易）启动。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>更新的预配（从市场启动时）

下图显示了从市场启动更新时的操作顺序。

![从市场启动更新时调用 API](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>更新的预配（从 SaaS 服务启动时）

下图显示了从 SaaS 服务启动更新时的操作。 （Webhook 调用替换为 SaaS 服务启动的订阅的更新。 

![从 SaaS 服务启动更新时调用 API](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

此状态表示尚未收到客户的付款。 根据策略，我们将在取消订阅之前为客户提供宽限期。 当订阅处于此状态时： 

- 作为合作伙伴，您可以选择降级或阻止用户对服务的访问。
- 订阅必须保持可恢复状态，可以恢复完整功能，而不会丢失任何数据或设置。 
- 期望通过履行 API 或宽限期结束时的取消预配请求获得此订阅的恢复请求。 

#### <a name="unsubscribed"></a>取消订阅 

订阅达到此状态，以响应明确的客户请求或不支付会费。 合作伙伴的期望是，客户的数据将保留一定天数，然后被删除。 


## <a name="api-reference"></a>API 参考

本节记录 SaaS*订阅 API* *和操作 API*。  版本 2 `api-version` API 的参数值为`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>参数和实体定义

下表列出了履行 API 使用的常见参数和实体的定义。

|     实体/参数     |     定义                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 资源的 GUID 标识符。  |
| `name`                   | 客户为此资源提供的友好名称。 |
| `publisherId`            | 每个发布者的唯一字符串标识符（例如："contoso"）。 |
| `offerId`                | 每个产品/服务的唯一字符串标识符（例如："offer1"）。  |
| `planId`                 | 每个计划/SKU 的唯一字符串标识符（例如："银色"）。 |
| `operationId`            | 特定操作的 GUID 标识符。  |
|  `action`                | 对资源执行的操作`Unsubscribe`，、、`Suspend``Reinstate``ChangePlan``ChangeQuantity`或 。 `Transfer` |
|   |   |

全局唯一标识符 （[GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)） 是 128 位（32-十进制）的数字，通常自动生成。 

#### <a name="resolve-a-subscription"></a>解析订阅 

解析终结点使发布者能够将市场令牌解析为持久资源 ID。 资源 ID 是 SaaS 订阅的唯一标识符。 当用户重定向到合作伙伴的网站时，URL 在查询参数中包含令牌。 合作伙伴应使用此令牌并请求解析它。 响应包含资源的唯一 SaaS 订阅 ID、名称、优惠 ID 和计划。 此令牌仅有效一小时。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>` |
|  x-ms-marketplace-token  |  当用户从 Azure 重定向到 SaaS 合作伙伴的网站时，URL 中的令牌查询参数（例如： `https://contoso.com/signup?token=..` *注：* 在使用令牌值之前，URL 会从浏览器中解码令牌值。  |

*响应代码：*

代码： 200<br>
将不透明令牌解析为 SaaS 订阅。 响应机构：
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

代码： 400<br>
请求错误。 x-ms-市场令牌丢失、格式错误或过期。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>订阅 API

订阅 API 支持以下 HTTPS 操作：**获取**、**发布**、**修补**和**删除**。


#### <a name="list-subscriptions"></a>列表订阅

列出发布者的所有 SaaS 订阅。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>获取<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查询参数：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。 |
| x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
| authorization      |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*响应代码：*

代码： 200 <br/>
根据身份验证令牌获取所有发布者优惠的发布者和相应订阅。

>[!Note]
>当您首次开发产品/服务时，将使用[模拟 API，](#mock-apis)而实际发布产品/服务时需要使用真正的 API。  实际 API 和模拟 API 因代码的第一行而异。  在实际 API 中有`subscription`该部分，而此部分不存在模拟 API。

模拟 API 的响应负载：<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
对于真正的 API： <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
仅当有要检索的计划的其他"页面"时，延续令牌才会存在。 

代码： 403 <br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。 

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>获取订阅

获取指定的 SaaS 订阅。 使用此呼叫获取许可证信息和计划信息。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   通过 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。   |
|  ApiVersion        |   用于此请求的操作的版本。   |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*响应代码：*

代码： 200<br>
从标识符获取 SaaS 订阅。 响应有效负载：<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。<br> 

代码： 500<br>
内部服务器错误。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>列出可用计划

使用此调用可了解当前发布者是否有任何私人或公开优惠。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>` |

*响应代码：*

代码： 200<br>
获取客户的可用计划列表。 响应机构：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

代码： 404<br>
未找到。<br> 

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。 <br> 

代码： 500<br>
内部服务器错误。<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>激活订阅

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此字符串将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>` |

*请求有效负载：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*响应代码：*

代码： 200<br>
激活订阅。<br>

代码： 400<br>
错误请求：验证失败。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>更改订阅上的计划

更新订阅上的计划。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。    |
| authorization      |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*请求有效负载：*

```json
Request Body:
{
    "planId": "gold"
}
```

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 指向资源的链接，以获取操作的状态。   |

*响应代码：*

代码： 202<br>
已接受更改计划的请求。 合作伙伴应轮询操作位置以确定成功或失败。 <br>

代码： 400<br>
错误请求：验证失败。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一次只能修补计划或数量，不能同时修补两者。 使用 Update 的订阅进行**编辑不在**`allowedCustomerOperations`中。

#### <a name="change-the-quantity-on-the-subscription"></a>更改订阅上的数量

更新订阅上的数量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>补丁：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。    |
| authorization      |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*请求有效负载：*

```json
Request Body:
{
    "quantity": 5
}
```

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 链接到资源以获取操作的状态。   |

*响应代码：*

代码： 202<br>
已接受更改数量的请求。 合作伙伴应轮询操作位置以确定成功或失败。 <br>

代码： 400<br>
错误请求：验证失败。


代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一次只能修补计划或数量，不能同时修补两者。 使用 Update 的订阅进行**编辑不在**`allowedCustomerOperations`中。

#### <a name="delete-a-subscription"></a>删除订阅

取消订阅并删除指定的订阅。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>删除<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。   |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。   |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*响应代码：*

代码： 202<br>
合作伙伴发起取消订阅 SaaS 订阅的呼叫。<br>

代码： 400<br>
删除不在 中的`allowedCustomerOperations`**删除**订阅。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>操作 API

操作 API 支持以下修补程序和获取操作。

#### <a name="list-outstanding-operations"></a>列出未完成的操作 

列出当前发布者未完成的操作。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查询参数：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   用于此请求的操作的版本。                |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*响应代码：*

代码： 200<br> 获取订阅上的挂起操作的列表。 响应有效负载：

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


代码： 400<br>
错误请求：验证失败。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>获取操作状态

使发布`Subscribe`者能够跟踪指定触发的异步操作（如 、`Unsubscribe``ChangePlan`或`ChangeQuantity`） 的状态。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。  |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*响应代码：*<br>

代码： 200<br> 获取指定的挂起 SaaS 操作。 响应有效负载：

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

代码： 400<br>
错误请求：验证失败。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。
 
代码： 404<br>
未找到。

代码： 500<br> 内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新操作的状态

更新操作的状态以指示使用提供的值成功或失败。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  用于此请求的操作的版本。  |
| subscriptionId     | 使用 Resolve API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |
|  operationId       | 正在完成的操作。 |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作中的所有事件与服务器端的事件相关联。 如果未提供此值，将在响应标头中生成并提供此值。 |
|  authorization     |  [获取 JSON Web 令牌 （JWT） 无记名令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*请求有效负载：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*响应代码：*

代码： 200<br> 通知合作伙伴方完成操作的呼叫。 例如，此响应可能表示座位或计划发生变化。

代码： 400<br>
错误请求：验证失败。

代码： 403<br>
未授权。 身份验证令牌未提供或无效，或者请求正在尝试访问不属于当前发布者的获取。

代码： 404<br>
未找到。

代码： 409<br>
冲突。 例如，已完成较新的事务。

代码： 500<br> 内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服务上实现网络钩子

发布者必须在此 SaaS 服务中实现 Webhook，以便主动通知用户其服务中的更改。 SaaS 服务应调用操作 API 以验证和授权，然后再对 Webhook 通知执行操作。


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
其中操作可以是以下操作之一： 
- `Unsubscribe`（删除资源时）
- `ChangePlan`（更改计划操作完成后）
- `ChangeQuantity`（更改数量操作完成后）
- `Suspend`（资源已挂起时）
- `Reinstate`（暂停后恢复资源时）

状态可以是以下状态之一： 
- **NotStarted** <br>
 - **InProgress** <br>
- **成功** <br>
- **失败** <br>
- **冲突** <br>

在 Webhook 通知中，可操作状态为 **"成功"** 和 **"失败**"。 操作的生命周期是从 **"未启动"** 到终端状态，如 **"成功**"、"**失败**"或 **"冲突**"。 如果收到 **"未启动**"或 **"未前进"，** 请继续通过 GET API 请求状态，直到操作达到终端状态，然后再执行操作。 

## <a name="mock-apis"></a>模拟 API

您可以使用我们的模拟 API 帮助您开始开发，特别是原型设计以及测试项目。 

主机终结点：（`https://marketplaceapi.microsoft.com/api`无需身份验证）<br/>
API 版本：`2018-09-15`<br/>
示例 URI：`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

在模拟和实际 API 中，API 终结点路径相同，但 API 版本不同。 该版本适用于`2018-09-15`模拟版本和生产`2018-08-31`版本。 

本文中的任何 API 调用都可以对模拟主机终结点进行。 通常，期望将模拟数据作为响应返回。 对模拟 API 上的更新订阅方法的调用始终返回 500。 

## <a name="next-steps"></a>后续步骤

开发人员还可以使用[云合作伙伴门户 REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)以编程方式检索和操作工作负载、产品/服务以及发布者配置文件。
