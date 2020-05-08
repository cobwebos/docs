---
title: Microsoft 商业应用商店中的 SaaS 履单 Api v2
description: 了解如何通过使用履单版本2来创建和管理 Microsoft AppSource 和 Azure Marketplace 上的 SaaS 产品/服务。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: 8f452349c0d636bc74a3722e2526623c955093f7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854186"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 中的 SaaS 履单 Api 版本2

本文详细介绍了实现 Api，使合作伙伴能够在 Microsoft AppSource 和 Azure Marketplace 中销售其 SaaS 应用程序。 事务 SaaS 产品/服务需要这些 Api。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 订阅生命周期

Azure SaaS 管理 SaaS 订阅购买的整个生命周期。 它使用履单 Api 作为一种机制来推动实际执行、对计划的更改和删除合作伙伴的订阅。 客户的帐单基于 Microsoft 维护的 SaaS 订阅的状态。 下图描述了驱动状态之间的更改的状态和操作。

![SaaS 订阅生命周期状态](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下表列出了 SaaS 订阅的预配状态，包括每个订阅的说明和序列图（如果适用）。 

#### <a name="provisioning"></a>设置

当客户启动购买时，合作伙伴会在使用 URL 参数的客户交互式网页上的授权代码中收到此信息。 例如`https://contoso.com/signup?token=..`，伙伴中心中的登陆页 URL 是`https://contoso.com/signup`。 可以通过调用解析 API 来验证和交换授权代码，以获取预配服务的详细信息。  当 SaaS 服务完成预配后，它将发送一个激活呼叫，通知完成完成并且客户可计费。 

下图显示了预配方案的 API 调用序列。  

![用于预配 SaaS 服务的 API 调用](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已预配

此状态是预配服务的稳定状态。

##### <a name="provisioning-for-update"></a>用于更新的设置 

此状态表示对现有服务的更新处于挂起状态。 此类更新可以由客户启动，无论是从 marketplace 还是 SaaS 服务（仅适用于直接到客户的事务）。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>预配更新（从 marketplace 启动时）

下图显示了从 marketplace 启动更新时的操作序列。

![从 marketplace 启动更新时的 API 调用](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>预配更新（从 SaaS 服务启动时）

下图显示了从 SaaS 服务启动更新时的操作。 （Webhook 调用由 SaaS 服务启动的订阅的更新替代。） 

![从 SaaS 服务启动更新时的 API 调用](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>已挂起

此状态表示尚未收到客户的付款。 按照策略，我们将在取消订阅之前向客户提供宽限期。 当订阅处于此状态时： 

- 作为合作伙伴，你可以选择降级或阻止用户访问服务。
- 订阅必须保留为可恢复状态，该状态可以还原完整功能，而不会丢失任何数据或设置。 
- 在宽限期结束时，应通过履行 Api 或取消预配请求来获取对此订阅的复原请求。 

#### <a name="unsubscribed"></a>取消 

订阅达到此状态是为了响应明确的客户请求或由于未付款的情况。 合作伙伴的期望是保留客户的数据，以便在特定天数的请求中恢复，然后删除。 


## <a name="api-reference"></a>API 参考

本部分介绍 SaaS*订阅 api*和*操作 api*。  版本 2 Api 的`api-version`参数值为`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>参数和实体定义

下表列出了履行 Api 使用的通用参数和实体的定义。

|     实体/参数     |     定义                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 资源的 GUID 标识符。  |
| `name`                   | 客户为此资源提供的友好名称。 |
| `publisherId`            | 每个发布服务器的唯一字符串标识符（例如： "contoso"）。 |
| `offerId`                | 每个产品/服务的唯一字符串标识符（例如： "offer1"）。  |
| `planId`                 | 每个计划/SKU （例如： "白银"）的唯一字符串标识符。 |
| `operationId`            | 特定操作的 GUID 标识符。  |
|  `action`                | 对资源执行的操作，为`Unsubscribe`、 `Suspend` `Reinstate`、或。 `ChangePlan` `ChangeQuantity` `Transfer` |
|   |   |

全局唯一标识符（[guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)）是通常自动生成的128位（32-十六进制）数字。 

#### <a name="resolve-a-subscription"></a>解决订阅 

解析终结点使发布服务器能够将 marketplace 令牌解析为永久性资源 ID。 资源 ID 是 SaaS 订阅的唯一标识符。 将用户重定向到合作伙伴的网站时，URL 在查询参数中包含一个令牌。 合作伙伴应使用此令牌并发出请求来解决该问题。 响应包含唯一的 SaaS 订阅 ID、名称、服务 ID 和规划资源。 此令牌仅在一小时内有效。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Post<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。 |
|  x-ms-marketplace-token  |  当用户从 Azure 重定向到 SaaS 合作伙伴的网站时，URL 中的令牌查询参数（例如： `https://contoso.com/signup?token=..`）。 *注意：* URL 在使用之前对浏览器中的标记值进行解码。  |

*响应代码：*

代码：200<br>
将不透明标记解析为 SaaS 订阅。 响应正文：
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

代码：400<br>
请求错误。 x-ms-缺少令牌、格式不正确或已过期。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
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

订阅 API 支持以下 HTTPS 操作： **Get**、 **Post**、 **Patch**和**Delete**。


#### <a name="list-subscriptions"></a>列出订阅

列出发布服务器的所有 SaaS 订阅。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查询参数：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
| x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
| 授权      |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*响应代码：*

代码：200 <br/>
基于身份验证令牌获取发布服务器的所有发布服务器和相应订阅。

>[!Note]
>[模拟 api](#mock-apis)在你首次开发产品/服务时使用，而实际的 api 需要在实际发布产品/服务时使用。  实际 Api 和模拟 Api 与代码的第一行有所不同。  在实际 API 中有`subscription`部分，而模拟 api 不存在此部分。

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
对于实际 API： <br>

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
仅当存在要检索的计划的其他 "页" 时，继续标记才会出现。 

代码：403 <br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。 

代码：500<br>
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

获取指定的 SaaS 订阅。 使用此调用获取许可证信息和计划信息。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   在通过解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。   |
|  ApiVersion        |   用于此请求的操作的版本。   |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*响应代码：*

代码：200<br>
从标识符获取 SaaS 订阅。 响应负载：<br>

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

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。<br> 

代码：500<br>
内部服务器错误。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>列出可用计划

使用此调用可了解当前发布服务器是否有任何私有或公共产品/服务。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。 |

*响应代码：*

代码：200<br>
获取客户的可用计划的列表。 响应正文：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

代码：404<br>
未找到。<br> 

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。 <br> 

代码：500<br>
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
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此字符串将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。 |

*请求负载：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*响应代码：*

代码：200<br>
激活订阅。<br>

代码：400<br>
错误的请求：验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>更改订阅的计划

更新订阅的计划。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。    |
| 授权      |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*请求负载：*

```json
Request Body:
{
    "planId": "gold"
}
```

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 用于获取操作状态的资源链接。   |

*响应代码：*

代码：202<br>
已接受更改计划的请求。 伙伴应轮询操作-位置以确定成功或失败。 <br>

代码：400<br>
错误的请求：验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
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
>一次只能修补一个计划或数量，而不是同时进行修补。 对包含**更新**的订阅的编辑内容`allowedCustomerOperations`不在中。

#### <a name="change-the-quantity-on-the-subscription"></a>更改订阅的数量

更新订阅上的数量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>跳<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。    |
| 授权      |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*请求负载：*

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

代码：202<br>
已接受更改数量的请求。 伙伴应轮询操作-位置以确定成功或失败。 <br>

代码：400<br>
错误的请求：验证失败。


代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
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
>一次只能修补一个计划或数量，而不是同时进行修补。 对包含**更新**的订阅的编辑内容`allowedCustomerOperations`不在中。

#### <a name="delete-a-subscription"></a>删除订阅

取消订阅并删除指定的订阅。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>删除<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。   |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。   |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*响应代码：*

代码：202<br>
合作伙伴发起了对 SaaS 订阅的取消订阅调用。<br>

代码：400<br>
**删除订阅，删除不在**中。 `allowedCustomerOperations`

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
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

操作 API 支持以下修补和获取操作。

#### <a name="list-outstanding-operations"></a>列出未完成的操作 

列出当前发布服务器的未完成操作。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查询参数：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   用于此请求的操作的版本。                |
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*响应代码：*

代码：200<br> 获取订阅上挂起的操作的列表。 响应负载：

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


代码：400<br>
错误的请求：验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：500<br>
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

允许发布服务器跟踪指定的触发异步操作的状态（ `Subscribe`例如、 `Unsubscribe` `ChangePlan`、或`ChangeQuantity`）。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。  |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*响应代码：*<br>

代码：200<br> 获取指定的挂起 SaaS 操作。 响应负载：

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

代码：400<br>
错误的请求：验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。
 
代码：404<br>
未找到。

代码：500<br> 内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新操作的状态

使用提供的值更新操作的状态，以指示成功或失败。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  用于此请求的操作的版本。  |
| subscriptionId     | 使用解析 API 解析令牌后获取的 SaaS 订阅的唯一标识符。  |
|  operationId       | 要完成的操作。 |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将客户端操作的所有事件与服务器端上的事件关联起来。 如果未提供此值，将在响应标头中生成并提供一个值。 |
|  授权     |  [获取 JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*请求负载：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*响应代码：*

代码：200<br> 对伙伴端的操作完成的通知。 例如，此响应可以通知座位或计划的更改。

代码：400<br>
错误的请求：验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或者请求尝试访问不属于当前发布服务器的获取。

代码：404<br>
未找到。

代码：409<br>
冲突。 例如，已经满足了较新的事务。

代码：500<br> 内部服务器错误。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服务上实现 webhook

发布者必须在此 SaaS 服务中实现 webhook，以主动向用户通知其服务中的更改。 SaaS 服务应在对 webhook 通知执行操作之前调用操作 API 进行验证和授权。


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
其中，操作可以是以下项之一： 
- `Unsubscribe`（删除资源时）
- `ChangePlan`（更改计划操作完成后）
- `ChangeQuantity`（更改数量操作完成后）
- `Suspend`（资源挂起时）
- `Reinstate`（在挂起后恢复资源时）

其中，状态可以是下列其中一项： 
- **NotStarted** <br>
 - **InProgress** <br>
- 成功  <br>
- **Failed** <br>
- **合并** <br>

在 webhook 通知中，可操作的状态为 "**成功**" 和 "**失败**"。 操作的生命周期是从**NotStarted**到终端状态，例如**成功**、**失败**或**冲突**。 如果收到**NotStarted**或**InProgress**，请继续通过 GET API 请求状态，直到操作在执行操作之前进入终端状态。 

## <a name="mock-apis"></a>模拟 Api

您可以使用模拟 Api 来帮助您开始开发、特别设计原型以及测试项目。 

主机终结点`https://marketplaceapi.microsoft.com/api` ：（不需要进行身份验证）<br/>
API 版本：`2018-09-15`<br/>
示例 URI：`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

模拟和真实 Api 中的 API 终结点路径是相同的，但 API 版本不同。 版本`2018-09-15`适用于模拟版本和`2018-08-31`生产版本。 

本文中的任何 API 调用均可与 mock 主机终结点建立。 通常情况下，应将模拟数据恢复为响应。 对模拟 API 的更新订阅方法的调用始终返回500。 

## <a name="next-steps"></a>后续步骤

开发人员还可以使用[云合作伙伴门户 REST api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)以编程方式检索和处理工作负荷、产品/服务和发布者配置文件。
