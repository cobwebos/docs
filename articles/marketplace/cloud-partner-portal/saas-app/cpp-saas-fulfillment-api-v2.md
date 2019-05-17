---
title: SaaS 履行 API V2 |Azure Marketplace
description: 介绍如何创建使用相关联的履行 V2 Api 在 Azure marketplace SaaS 产品/服务。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 433059dc1b1567c5cbcb1091f2d616001d1dbf44
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762272"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行 Api 版本 2 

本文详细介绍了 Azure Marketplace 的 API，使独立软件供应商 (Isv)，将其 SaaS 应用程序进行集成。 此 API 使 ISV 应用程序可以参与所有启用的商务通道： 直接、 合作伙伴主导式 （经销商） 和字段导致。  此 API 是事务 SaaS 提供了在 Azure Marketplace 上列出的要求。


## <a name="managing-the-saas-subscription-lifecycle"></a>SaaS 订阅生命周期管理

Microsoft SaaS 服务管理 SaaS 订阅购买的整个生命周期，并使用执行 API，一种机制来驱动实际履行更改计划和订阅的 ISV 的删除。 客户计费基于 Microsoft 维护的 SaaS 订阅的状态。 下图描绘了状态和状态之间驱动器所做的更改的操作。

![SaaS 订阅生命周期状态](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 订阅的状态

下表列出了 SaaS 订阅，每个包括说明和序列关系图 （如果适用） 的预配状态。 

#### <a name="provisioning"></a>设置

当客户启动购买时，ISV 客户交互式网页上使用 URL 参数返回身份验证代码中收到此信息。 例如： `https://contoso.com/signup?token=..`，其中合作伙伴中心中的登录页 URL 提供程序是`https://contoso.com/signup`。 可以验证以及交换有关需要通过调用解决 API 将其预配的详细信息的身份验证代码。  完成 SaaS 服务预配后，它将发送的激活调用以指示执行已完成并可以按客户进行计费。  下图显示了预配方案的 API 调用的顺序。  

![API 调用为预配 SaaS 服务。](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已预配

此状态是预配服务的稳定状态。

#### <a name="provisioning-for-update"></a>更新预配
（从 marketplace) 

此状态表示更新到现有服务处于挂起状态。 可以启动此类更新，由客户在 marketplace 上或在 SaaS 服务 （仅适用于直接发送到客户的事务。）从 marketplace 启动更新时下, 图显示的操作。

![API 调用时从 marketplace 启动更新。](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>更新预配  
（从 SaaS 服务） 

下图显示的操作时由 SaaS 服务启动更新。 （webhook 调用替换为对由 SaaS 服务启动的订阅的更新。 

![API 调用时由 SaaS 服务启动更新。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>已挂起

此状态表明尚未收到的客户的付款。 通过策略，我们将提供客户之前 unfulfilling 订阅的宽限期。 当订阅处于此状态： 

- 作为 ISV，你可以选择降低或阻止对服务的用户的访问。 
- 订阅必须保存在可恢复状态，可以还原不会丢失任何数据或设置的全部功能。 
- 您有望在宽限期结束获取复原请求履行 API，通过此订阅或取消预配的请求。 

#### <a name="unsubscribed"></a>取消订阅 

订阅达到此状态，以响应到的显式客户请求，或者作为要归因未付款的响应。 Isv 预期结果是客户的数据是保留的最小 X 天的请求上进行恢复，然后删除。 


## <a name="api-reference"></a>API 参考

本部分介绍 SaaS*订阅 API*并*操作 API*。  值`api-version`参数在版本 2 Api 是`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>参数和实体定义

下表列出了常见参数和执行 Api 使用的实体的定义。

|     实体/参数     |     定义                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 资源的 GUID 标识符  |
| `name`                   | 此资源的客户提供的友好名称 |
| `publisherId`            | 对于每个发布服务器，例如"contoso"的唯一字符串标识符 |
| `offerId`                | 每个产品/服务，例如"offer1"的唯一字符串标识符  |
| `planId`                 | 每个计划和 sku，例如"silver"的唯一字符串标识符 |
| `operationId`            | 某一特定操作的 GUID 标识符  |
|  `action`                | 在资源上，可以执行的操作`subscribe`， `unsubscribe`， `suspend`， `reinstate`，或`changePlan`， `changeQuantity`， `transfer`  |
|   |   |

全局唯一标识符 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 通常会自动生成的 128 位 （32 十六进制） 号。 

#### <a name="resolve-a-subscription"></a>解决订阅 

解决终结点，发布服务器以解析 marketplace 令牌为永久性的资源 id。 资源 ID 是 SAAS 订阅的唯一标识符。  当用户重定向到 ISV 的网站时，URL 将在查询参数中包含一个令牌。 ISV 应当使用此令牌，并发出请求来解析它。 响应包含资源的唯一 SAAS 订阅 ID、名称、产品/服务 ID 和计划。 此令牌的有效期仅为一小时。 

**发布：<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用于此请求的操作版本  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  用于跟踪请求从客户端，最好是一个 GUID 唯一字符串值。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
|  x-ms-correlationid |  在客户端上的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  当用户重定向到 SaaS ISV 的网站与 Azure 在 URL 中的令牌查询参数 (对于例如： `https://contoso.com/signup?token=..`)。 *注意：* URL 解码然后再使用它从浏览器的令牌值。  |

*响应代码：*

代码：200<br>
解析为 SaaS 订阅的不透明的令牌。<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

代码：404<br>
未找到

代码：400<br>
请求错误。 x ms marketplace 令牌已丢失、 格式不正确或已过期。

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

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

**获取：<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*查询参数：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  用于跟踪请求从客户端，最好是一个 GUID 唯一字符串值。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
| x-ms-correlationid |  在客户端上的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*响应代码：*

代码：200 <br/>
根据身份验证令牌，获取发布服务器和发布服务器的所有产品/服务的相应订阅。<br> 响应有效负载：<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

继续标记才会显示是否存在的计划，以检索其他"页"。 

代码：403 <br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。 

代码：500 内部服务器错误

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

**获取：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   SaaS 解决通过解决 API 令牌后获取的订阅的唯一标识符   |
|  ApiVersion        |   要用于此请求的操作版本   |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  用于跟踪请求从客户端，最好是一个 GUID 唯一字符串值。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
|  x-ms-correlationid |  在客户端上的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*响应代码：*

代码：200<br>
从标识符获取 SaaS 订阅<br> 响应有效负载：<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

代码：404<br>
未找到<br> 

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>可用计划列表

使用此调用来找出是否有任何私钥/公钥产品/服务的当前发布服务器。

**获取：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   要用于此请求的操作版本  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   用于跟踪请求从客户端，最好是一个 GUID 唯一字符串值。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
|  x-ms-correlationid  | 在客户端上的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*响应代码：*

代码：200<br>
获取客户的可用计划列表。<br>

响应正文：

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
未找到<br> 

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。 <br> 

代码：500<br>
内部服务器错误<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>激活订阅

**发布：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用于此请求的操作版本  |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 用于跟踪请求从客户端，最好是一个 GUID 唯一字符串值。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  x-ms-correlationid  | 在客户端上的操作的唯一字符串值。 此字符串将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*请求：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*响应代码：*

代码：200<br>
激活订阅。<br>

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

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

**修补程序：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。    |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
| Operation-Location | 链接到资源以获取操作的状态。   |

*响应代码：*

代码：202<br>
若要更改计划的请求已被接受。 ISV 应轮询操作位置来确定成功/失败。 <br>

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败。

>[!Note]
>仅对计划或数量可以一次修补，而不是两者。 对与某一订阅编辑**更新**不在`allowedCustomerOperations`。

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>更改的订阅数量

更新订阅的数量。

**修补程序：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。    |
| authorization      |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
已接受。 更改数量的请求已被接受。 ISV 应轮询操作位置来确定成功/失败。 <br>

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败。

>[!Note]
>仅对计划或数量可以一次修补，而不是两者。 对与某一订阅编辑**更新**不在`allowedCustomerOperations`。

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>删除订阅

取消订阅，并删除指定的订阅。

**删除：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，其中一个将生成并在响应标头中提供。   |
|  x-ms-correlationid  |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。   |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*响应代码：*

代码：202<br>
ISV 发起调用，以指示取消订阅 SaaS 订阅。<br>

代码：404<br>
未找到

代码：400<br>
对与某一订阅删除**删除**不在`allowedCustomerOperations`。

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

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

**获取：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*查询参数：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   用于此请求的操作的版本。                |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符。  |

*请求标头：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*响应代码：*

代码：200<br> 获取挂起的订阅上的操作的列表。<br>
响应有效负载：

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

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：500<br>
内部服务器错误

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>获取操作状态

使发布服务器上，若要跟踪指定触发的异步操作状态 (订阅 / 取消订阅 / 更改计划 / 更改数量)。

**获取：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  用于此请求的操作的版本。  |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。  |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。  |
|  authorization     |[获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*响应代码：* 代码：200<br> 获取指定挂起 SaaS 操作<br>
响应有效负载：

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

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。
 
代码：500<br> 内部服务器错误

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新操作的状态

更新操作以指示成功/失败与提供的值的状态。

**修补程序：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*查询参数：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  用于此请求的操作的版本。  |
| subscriptionId     | 解决使用解决 API 的令牌后获取的 SaaS 订阅的唯一标识符。  |
|  operationId       | 为正在完成该操作。 |

*请求标头：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   唯一的字符串值，用于跟踪来自客户端的请求，最好是 GUID。 如果未提供此值，则系统会生成一个值，并在响应标头中提供该值。 |
|  x-ms-correlationid |  在客户端上执行的操作的唯一字符串值。 此参数将在服务器端上的事件从客户端操作的所有事件相关都联。 如果未提供此值，其中一个将生成并在响应标头中提供。 |
|  authorization     |  [获取 JSON web 令牌 (JWT) 持有者令牌。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*请求有效负载：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*响应代码：*

代码：200<br> 调用以通知的 ISV 端操作的完成。 例如，此响应可能表示席位/计划的更改。

代码：404<br>
未找到

代码：400<br>
错误的请求验证失败

代码：403<br>
未授权。 身份验证令牌未提供，是无效的或者请求正在尝试访问不属于当前发布服务器获取。

代码：409<br>
冲突。 例如，已满足更高版本的事务

代码：500<br> 内部服务器错误

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>在 SaaS 服务的 Webhook

发布服务器必须实现以主动通知用户其服务中的更改此 SaaS 服务中的 webhook。 API 应为未经身份验证，并将由 Microsoft SaaS 服务调用。 SaaS 服务需要调用 API 进行验证和授权对 webhook 通知采取操作之前的操作。

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}
```

其中操作可以是下列之一： 
- `Subscribe`  （当该资源已激活）
- `Unsubscribe` （当该资源已被删除）
- `ChangePlan` （当更改计划操作已完成）
- `ChangeQuantity` （当更改数量操作已完成）
- `Suspend` （当该资源已被挂起）
- `Reinstate` （当资源具有已恢复暂挂后）


## <a name="mock-api"></a>模拟 API

可以使用我们的模拟 Api 来帮助你开始进行开发，特别是原型制作和测试项目。 

主机终结点： `https://marketplaceapi.microsoft.com/api` <br/>
API 版本： `2018-09-15` <br/>
不要求进行验证 <br/>
示例 Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 终结点路径都是相同的模拟和实际 Api，但不同的 API 版本。 版本为 2018年-09-15 的模拟和 2018年-08-31 的生产版本。 

在本文中的 API 调用的任何可对模拟主机终结点。 您可能希望获取返回作为响应的模拟数据。 一般情况下，则可能要获取返回作为响应的模拟数据。 对模拟 API 上的更新订阅方法的调用始终返回 500。 

## <a name="next-steps"></a>后续步骤

开发人员可以以编程方式检索和操作工作负荷，产品/服务，并且发布者配置文件使用[云合作伙伴门户 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
