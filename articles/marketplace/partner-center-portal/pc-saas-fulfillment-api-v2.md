---
title: SaaS 履行 API v2 |Azure Marketplace
description: 此文章介绍了如何创建和管理 Azure Marketplace 和 AppSource 上的 SaaS 产品/服务使用关联的执行第 2 版 Api。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 476aaacbe6f1bf6d1920df0f12599976bfcc27b7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701138"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行 Api，版本 2 

本文详细介绍了使合作伙伴能够销售 SaaS 应用程序 AppSource marketplace 和 Azure Marketplace 中的 Api。 这些 Api 都是在 AppSource 和 Azure Marketplace 上提供了适用于事务的 SaaS 要求。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 订阅生命周期

Azure SaaS 管理整个生命周期的 SaaS 订阅购买。 它使用履行 Api 作为一种机制来驱动实际履行，更改为计划和订阅的合作伙伴的删除。 客户的帐单根据 Microsoft 维护的 SaaS 订阅的状态。 下图描绘了状态和状态之间驱动器所做的更改的操作。

![SaaS 订阅生命周期状态](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下表列出了 SaaS 订阅，每个包括说明和序列关系图 （如果适用） 的预配状态。 

#### <a name="provisioning"></a>设置

当客户启动购买时，合作伙伴接收此信息在授权代码在客户交互的网页上使用的 URL 参数。 例如， `https://contoso.com/signup?token=..`，而在合作伙伴中心登录页面的 URL 是`https://contoso.com/signup`。 可以验证并通过调用解决 API 用于预配服务的详细信息交换授权代码。  完成一种 SaaS 服务预配后，它将发送的激活调用以指示执行已完成并可以按客户进行计费。 

下图显示了预配方案的 API 调用的顺序。  

![为预配 SaaS 服务的 API 调用](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已预配

此状态是预配服务的稳定状态。

##### <a name="provisioning-for-update"></a>更新预配 

此状态表示对现有服务的更新处于挂起状态。 此类更新可由客户通过 marketplace 或 SaaS 服务 （仅适用于直接客户事务） 上启动。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>（如果它从 marketplace 启动的） 更新的预配

从 marketplace 启动更新时下, 图显示操作的序列。

![从 marketplace 启动更新时的 API 调用](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>（如果它从 SaaS 服务启动的） 更新的预配

更新从 SaaS 服务启动时下, 图显示的操作。 （webhook 调用将替换为对由 SaaS 服务启动的订阅的更新。） 

![API 调用更新从 SaaS 服务启动时](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

此状态表明尚未收到的客户的付款。 通过策略，我们将提供一个宽限期之前取消订阅的客户。 当订阅处于此状态： 

- 作为合作伙伴，你可以选择降低或阻止对服务的用户的访问。
- 订阅必须保存在可恢复状态，可以还原不会丢失任何数据或设置的全部功能。 
- 希望在宽限期结束时执行 Api 通过此订阅或取消其预配请求获取复原请求。 

#### <a name="unsubscribed"></a>已取消的订阅 

订阅达到此状态的显式客户请求或由于未付款 dues 的响应。 从合作伙伴的假定条件下是保留特定天数请求上进行恢复，然后删除客户的数据。 


## <a name="api-reference"></a>API 参考

本部分介绍 SaaS*订阅 API*并*操作 API*。  值`api-version`参数在版本 2 Api 是`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>参数和实体定义

下表列出了常见参数和执行 Api 使用实体的定义。

|     实体/参数     |     定义                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 资源的 GUID 标识符。  |
| `name`                   | 此资源的客户提供的友好名称。 |
| `publisherId`            | 每个发布服务器的唯一字符串标识符 (例如:"contoso")。 |
| `offerId`                | 每个产品/服务的唯一字符串标识符 (例如:"offer1")。  |
| `planId`                 | 每个计划和 SKU 的唯一字符串标识符 (例如:"silver")。 |
| `operationId`            | 特定操作的 GUID 标识符。  |
|  `action`                | 在资源上，可以执行的操作`unsubscribe`， `suspend`， `reinstate`，或`changePlan`， `changeQuantity`， `transfer`。  |
|   |   |

全局唯一标识符 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 通常会自动生成的 128 位 （32-十六进制） 号。 

#### <a name="resolve-a-subscription"></a>解决订阅 

解决终结点，发布服务器以解析 marketplace 令牌为持久资源 id。 资源 ID 是 SaaS 订阅的唯一标识符。 当用户重定向到合作伙伴的网站时，URL 将包含在查询参数中的令牌。 合作伙伴应使用此令牌并发出请求，以解决该问题。 响应包含唯一 SaaS 的订阅 ID、 名称、 产品/服务 ID 和资源的计划。 此令牌的有效期为一小时。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>发布<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |
|  x-ms-marketplace-token  |  当用户重定向到 SaaS 合作伙伴的网站从 Azure 的 URL 中的令牌的查询参数 (例如： `https://contoso.com/signup?token=..`)。 *注意：* URL 解码然后再使用它从浏览器的令牌值。  |

*响应代码：*

代码：200<br>
解析为 SaaS 订阅的不透明的令牌。 响应正文：
 

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
请求错误。 x ms marketplace 令牌已丢失、 格式不正确，或已过期。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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

订阅 API 支持 HTTPS 执行以下操作：**获取**， **Post**，**修补程序**，并且**删除**。


#### <a name="list-subscriptions"></a>列出订阅

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
| x-ms-requestid     |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
| x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*响应代码：*

代码：200 <br/>
获取发布服务器和发布服务器的所有产品/服务，基于身份验证令牌的相应订阅。
响应有效负载：<br>

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
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

继续标记将会显示仅在没有计划要检索的其他"页"。 

代码：403 <br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。 

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

获取指定的 SaaS 订阅。 使用此调用以获取许可证信息并计划信息。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   解决通过解决 API 令牌后获取的 SaaS 订阅的唯一标识符。   |
|  ApiVersion        |   用于此请求的操作的版本。   |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*响应代码：*

代码：200<br>
获取从标识符 SaaS 订阅。 响应有效负载：<br>

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
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M"
        },
}
```

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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

#### <a name="list-available-plans"></a>可用计划列表

使用此调用来找出是否存在当前发布服务器的任何私有或公共产品/服务。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*响应代码：*

代码：200<br>
获取客户的可用计划列表。 响应正文：

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
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。 <br> 

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

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>发布<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  x-ms-correlationid  | 在客户端上执行的操作的唯一字符串值。 此字符串将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*请求有效负载：*

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
错误的请求： 验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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

#### <a name="change-the-plan-on-the-subscription"></a>更改计划的订阅

更新的订阅计划。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。    |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

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
| Operation-Location | 若要获取操作的状态的资源链接。   |

*响应代码：*

代码：202<br>
若要更改计划的请求已被接受。 合作伙伴应轮询操作位置来确定成功或失败。 <br>

代码：400<br>
错误的请求： 验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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
>仅对计划或数量可以一次修补，而不是两者。 对与某一订阅编辑**更新**不在`allowedCustomerOperations`。

#### <a name="change-the-quantity-on-the-subscription"></a>更改的订阅数量

更新订阅的数量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修补程序：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。    |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

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

代码：202<br>
更改数量的请求已被接受。 合作伙伴应轮询操作位置来确定成功或失败。 <br>

代码：400<br>
错误的请求： 验证失败。


代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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
>仅对计划或数量可以一次修补，而不是两者。 对与某一订阅编辑**更新**不在`allowedCustomerOperations`。

#### <a name="delete-a-subscription"></a>删除订阅

取消订阅，并删除指定的订阅。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>DELETE<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。   |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。   |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*响应代码：*

代码：202<br>
合作伙伴启动取消订阅 SaaS 订阅的调用。<br>

代码：400<br>
对与某一订阅删除**删除**不在`allowedCustomerOperations`。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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

操作 API 支持以下修补程序和 Get 操作。

#### <a name="list-outstanding-operations"></a>列出未完成操作 

列出当前发布服务器的未完成操作。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查询参数：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   用于此请求的操作的版本。                |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*响应代码：*

代码：200<br> 获取挂起的订阅上的操作的列表。 响应有效负载：

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
错误的请求： 验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

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

使发布服务器上，若要跟踪指定触发的异步操作状态 (如`subscribe`， `unsubscribe`， `changePlan`，或`changeQuantity`)。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>获取<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*响应代码：*<br>

代码：200<br> 获取指定挂起 SaaS 操作。 响应有效负载：

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
错误的请求： 验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。
 
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

更新操作以指示成功或失败与提供的值的状态。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>修补程序<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  用于此请求的操作的版本。  |
| subscriptionId     | 通过使用解决 API 解析令牌后获得的 SaaS 订阅的唯一标识符。  |
|  operationId       | 为正在完成该操作。 |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*请求有效负载：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*响应代码：*

代码：200<br> 若要通知合作伙伴端操作的完成的调用。 例如，此响应可能表示席位或计划的更改。

代码：400<br>
错误的请求： 验证失败。

代码：403<br>
未授权。 身份验证令牌未提供或无效，或请求尝试访问不属于当前发布服务器获取。

代码：404<br>
未找到。

代码：409<br>
冲突。 例如，已满足更高版本的事务。

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

发布服务器必须实现以主动通知用户其服务中的更改此 SaaS 服务中的 webhook。 SaaS 服务需要调用 API 进行验证和授权对 webhook 通知采取操作之前的操作。

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
其中可以将下列任一操作： 
- `unsubscribe` （当该资源已被删除）
- `changePlan` （当更改计划操作已完成）
- `changeQuantity` （当更改数量操作已完成）
- `suspend` （当资源已被挂起）
- `reinstate` （当资源具有已恢复暂挂后）

其中状态可以是以下值之一： 
- **NotStarted** <br>
 - **InProgress** <br>
- 成功  <br>
- 失败  <br>
- **Conflict** <br>

在 webhook 通知中，可操作状态是任一**Succeeded**并**失败**。 操作的生命周期是从**NotStarted**终端状态等**Succeeded**，**失败**，或者**冲突**。 如果你收到**NotStarted**或**InProgress**，继续执行操作之前，该操作进入终止状态之前请求通过获取 API 的状态。 

## <a name="mock-apis"></a>模拟 Api

我们的模拟 Api 可用于帮助你开始进行开发，特别是原型设计也作为测试项目。 

主机终结点： `https://marketplaceapi.microsoft.com/api` （不要求进行验证）<br/>
API 版本： `2018-09-15`<br/>
示例 URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 终结点路径是相同的模拟和真实的 api，但不同的 API 版本。 版本是`2018-09-15`为 mock 版本和`2018-08-31`生产版本。 

在本文中的 API 调用的任何可对模拟主机终结点。 一般情况下，希望获取返回作为响应的模拟数据。 对模拟 API 上的更新订阅方法的调用始终返回 500。 

## <a name="next-steps"></a>后续步骤

开发人员可以以编程方式检索和使用操作工作负载、 产品/服务，以及发布服务器配置文件[云合作伙伴门户 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
