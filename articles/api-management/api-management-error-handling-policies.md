---
title: Azure API 管理策略中的错误处理 | Microsoft 文档
description: 了解在 Azure API 管理中处理请求时，如何对可能发生的错误情况作出响应。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
ms.locfileid: "26128220"
---
# <a name="error-handling-in-api-management-policies"></a>API 管理策略中的错误处理
Azure API 管理提供 `ProxyError` 对象，允许发布服务器响应错误情况，后者可能发生在处理针对代理的请求时。 `ProxyError` 对象可通过 [context.LastError](api-management-policy-expressions.md#ContextVariables) 属性访问，并可被策略用在 `on-error` 策略节。 本主题提供的参考针对 Azure API 管理中的错误处理功能。  
  
## <a name="error-handling-in-api-management"></a>API 管理中的错误处理  
 Azure API 管理中的策略分为 `inbound`、`backend`、`outbound`、`on-error` 部分，如以下示例所示。  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 在处理请求期间，内置的步骤与请求范围内的策略一起执行。 如果发生错误，处理会立即跳转到 `on-error` 策略节。 `on-error` 策略节适用于任何范围，而 API 发布者则可配置自定义行为，例如将错误记录到事件中心，或者创建新的需要返回到调用方的响应。  
  
> [!NOTE]
>  默认情况下，`on-error` 节不存在于策略中。 要将 `on-error` 节添加到策略，请在策略编辑器中浏览到所需策略，然后将其添加进去。 有关配置策略的详细信息，请参阅 [API 管理中的策略](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)。  
>   
>  如果没有 `on-error` 节，则在出现错误情况时，调用方会收到 400 或 500 HTTP 响应消息。  
  
### <a name="policies-allowed-in-on-error"></a>在错误时中允许的策略  
 以下策略可以用在 `on-error` 策略节中。  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 当发生错误且控制跳转到 `on-error` 策略节时，错误会存储在 [context.LastError](api-management-policy-expressions.md#ContextVariables) 属性中，该属性可以通过 `on-error` 节中的策略进行访问，并且具有以下属性。  
  
|Name|类型|说明|必选|  
|----------|----------|-----------------|--------------|  
|源|字符串|指定在其中发生错误的元素。 可以是策略或内置管道步骤名称。|是|  
|原因|字符串|计算机友好错误代码，可以用在错误处理中。|否|  
|消息|字符串|用户可读的错误说明。|是|  
|范围|字符串|在其中发生错误的范围的名称，可以是以下值之一：“全局”、“产品”、“API”、“操作”|否|  
|部分|字符串|在其中发生错误的节名，可以是以下值之一：“入站”、“后端”、“出站”、“错误时”。|否|  
|路径|字符串|指定嵌套式策略，例如“choose[3]/when[2]”。|否|  
|PolicyId|字符串|在其中发生错误的策略的 `id` 属性（如果已由客户指定）的值|否|  
  
> [!NOTE]
>  所有策略都有一个可选的 `id` 属性，该属性可以添加到策略的根元素。 如果出现错误情况时该属性存在于策略中，则可使用 `context.LastError.PolicyId` 属性检索该属性的值。  
  
## <a name="predefined-errors-for-built-in-steps"></a>针对内置步骤的预定义错误  
 以下错误为预定义错误，其所针对的错误情况可能发生在对内置处理步骤进行评估的时候。  
  
|源|条件|原因|消息|  
|------------|---------------|------------|-------------|  
|配置|URI 与任何 API 或操作均不匹配|OperationNotFound|无法匹配操作的传入请求。|  
|authorization|未提供订阅密钥|SubscriptionKeyNotFound|由于缺少订阅密钥，访问被拒绝。 请确保在向此 API 发出请求时包括订阅密钥。|  
|authorization|订阅密钥值无效|SubscriptionKeyInvalid|由于订阅密钥无效，访问被拒绝。 请确保提供活动订阅的有效密钥。|  
  
## <a name="predefined-errors-for-policies"></a>针对策略的预定义错误  
 以下错误为预定义错误，其所针对的错误情况可能发生在策略评估期间。  
  
|源|条件|原因|消息|  
|------------|---------------|------------|-------------|  
|rate-limit|超出速率限制|RateLimitExceeded|超出速率限制|  
|quota|超出配额|QuotaExceeded|超出调用卷配额。 配额会在 xx:xx:xx 复原。 -或- 超出带宽配额。 配额会在 xx:xx:xx 复原。|  
|jsonp|回调参数值无效（包含错误字符）|CallbackParameterInvalid|回调参数 {callback-parameter-name} 的值不是有效的 JavaScript 标识符。|  
|ip-filter|无法分析请求中的调用方 IP|FailedToParseCallerIP|无法确定调用方的 IP 地址。 访问被拒绝。|  
|ip-filter|调用方 IP 不在允许列表中|CallerIpNotAllowed|不允许调用方 IP 地址 {ip-address}。 访问被拒绝。|  
|ip-filter|调用方 IP 位于阻止列表中|CallerIpBlocked|已阻止调用方 IP 地址。 访问被拒绝。|  
|check-header|必需的标头不存在或缺少值|HeaderNotFound|在请求中找不到标头 {header-name}。 访问被拒绝。|  
|check-header|必需的标头不存在或缺少值|HeaderValueNotAllowed|不允许标头 {header-name} 的值 {header-value}。 访问被拒绝。|  
|validate-jwt|请求中缺少 Jwt 令牌|TokenNotFound|在请求中找不到 JWT。 访问被拒绝。|  
|validate-jwt|签名验证失败|TokenSignatureInvalid|<jwt 库中的消息\>。 访问被拒绝。|  
|validate-jwt|受众无效|TokenAudienceNotAllowed|<jwt 库中的消息\>。 访问被拒绝。|  
|validate-jwt|颁发者无效|TokenIssuerNotAllowed|<jwt 库中的消息\>。 访问被拒绝。|  
|validate-jwt|令牌已到期|TokenExpired|<jwt 库中的消息\>。 访问被拒绝。|  
|validate-jwt|按 ID 无法解析签名密钥|TokenSignatureKeyNotFound|<jwt 库中的消息\>。 访问被拒绝。|  
|validate-jwt|令牌中缺少必需的声明|TokenClaimNotFound|JWT 令牌缺少以下声明: <c1\>、<c2\>、… 访问被拒绝。|  
|validate-jwt|声明值不匹配|TokenClaimValueNotAllowed|不允许声明 {claim-name} 的值 {claim-value}。 访问被拒绝。|  
|validate-jwt|其他验证失败|JwtInvalid|<jwt 库中的消息\>|

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   