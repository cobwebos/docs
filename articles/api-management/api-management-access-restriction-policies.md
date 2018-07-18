---
title: Azure API 管理访问限制策略 | Microsoft 文档
description: 了解可在 Azure API 管理中使用的访问限制策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 1b6aea5152e9eb5152b400d74d834e31eb883458
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110211"
---
# <a name="api-management-access-restriction-policies"></a>API 管理访问限制策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](http://go.microsoft.com/fwlink/?LinkID=398186)。  
  
##  <a name="AccessRestrictionPolicies"></a> 访问限制策略  
  
-   [检查 HTTP 标头](api-management-access-restriction-policies.md#CheckHTTPHeader) - 强制必须存在 HTTP 标头和/或强制采用 HTTP 标头的值。  
-   [按订阅限制调用速率](api-management-access-restriction-policies.md#LimitCallRate) - 根据订阅限制调用速率，避免 API 使用量暴增。  
-   [按密钥限制调用率](#LimitCallRateByKey) - 根据密钥限制调用率以避免 API 使用量暴增。  
-   [限制调用方 IP](api-management-access-restriction-policies.md#RestrictCallerIPs) - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。  
-   [按订阅设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuota) - 允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。  
-   [按密钥设置使用量配额](#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。  
-   [验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。  
  
##  <a name="CheckHTTPHeader"></a> 检查 HTTP 标头  
 使用 `check-header` 策略强制请求具有指定的 HTTP 标头。 可以选择性地查看标头是否具有特定值，或者检查是否存在一系列允许的值。 如果检查失败，此策略会终止请求处理，并返回其所指定的 HTTP 状态代码和错误消息。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|check-header|根元素。|是|  
|值|允许的 HTTP 标头值。 指定了多个值元素时，如果任何一个值匹配，则可认为检查成功。|否|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|在标头不存在或其值无效的情况下，需要在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。|是|不适用|  
|failed-check-httpcode|在标头不存在或其值无效时需返回的 HTTP 状态代码。|是|不适用|  
|header-name|要检查的 HTTP 标头的名称。|是|不适用|  
|ignore-case|可以设置为 True 或 False。 如果设置为 True，则在将标头值与一组可接受的值进行比较时，会忽略大小写。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：** 入站、出站  
  
-   **策略范围：** 全局、产品、API、操作  
  
##  <a name="LimitCallRate"></a> 按订阅限制调用速率  
 `rate-limit` 策略可以对调用速率进行限制，使指定时段的调用不超出指定的数目，避免单个订阅的 API 使用量暴增。 触发此策略时，调用方会收到`429 Too Many Requests`响应状态代码。  
  
> [!IMPORTANT]
>  每个策略文档只能使用此策略一次。  
>   
>  [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|set-limit|根元素。|是|  
|api|添加一个或多个此类元素，对产品中的 API 施加调用速率限制。 产品和 API 的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。|否|  
|operation|添加一个或多个此类元素，对 API 中的操作施加调用速率限制。 产品、API 和操作的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。|否|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|名称|要对其应用速率限制的 API 的名称。|是|不适用|  
|calls|在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。|是|不适用|  
|renewal-period|在重置配额之前等待的时间长度，以秒为单位。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** 产品  
  
##  <a name="LimitCallRateByKey"></a> 按密钥限制调用速率  
 `rate-limit-by-key` 策略可以对调用速率进行限制，使指定时段的调用不超出指定的数目，避免单个密钥的 API 使用量暴增。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定在决定是否到达限制值时应该进行计数的请求。 触发此策略时，调用方会收到`429 Too Many Requests`响应状态代码。  
  
 有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)。  
  
> [!IMPORTANT]
>  每个策略文档只能使用此策略一次。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>示例  
 在下面的示例中，可通过调用方 IP 地址对速率限制进行键控。  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|set-limit|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|calls|在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。|是|不适用|  
|counter-key|用于速率限制策略的密钥。|是|不适用|  
|increment-condition|一个布尔表达式，指定是否应将请求计入配额 (`true`)。|否|不适用|  
|renewal-period|在重置配额之前等待的时间长度，以秒为单位。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
  
-   **策略范围：** 全局、产品、API、操作  
  
##  <a name="RestrictCallerIPs"></a> 限制调用方 IP  
 `ip-filter` 策略筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|ip-filter|根元素。|是|  
|地址|指定要对其进行筛选的单个 IP 地址。|至少一个 `address` 或 `address-range` 元素是必需的。|  
|address-range from="address" to="address"|指定要对其进行筛选的 IP 地址范围。|至少一个 `address` 或 `address-range` 元素是必需的。|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|address-range from="address" to="address"|允许或拒绝其访问的某个 IP 地址范围。|使用 `address-range` 元素时必需。|不适用|  
|ip-filter action="allow &#124; forbid"|指定是否应允许指定的 IP 地址和范围执行调用。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** 全局、产品、API、操作  
  
##  <a name="SetUsageQuota"></a> 按订阅设置使用量配额  
 `quota` 策略允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。  
  
> [!IMPORTANT]
>  每个策略文档只能使用此策略一次。  
>   
>  [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|quota|根元素。|是|  
|api|添加一个或多个此类元素，对产品中的 API 设置调用配额。 产品和 API 的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。|否|  
|operation|添加一个或多个此类元素，对 API 中的操作设置调用配额。 产品、API 和操作的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。|否|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|名称|要向其应用配额的 API 或操作的名称。|是|不适用|  
|bandwidth|在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。|必须指定 `calls` 和/或 `bandwidth`。|不适用|  
|calls|在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。|必须指定 `calls` 和/或 `bandwidth`。|不适用|  
|renewal-period|在重置配额之前等待的时间长度，以秒为单位。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** 产品  
  
##  <a name="SetUsageQuotaByKey"></a> 按密钥设置使用量配额  
 `quota-by-key` 策略允许根据密钥强制实施可续订或有生存期的调用量和/或带宽配额。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定应在配额范围内的请求。  
  
 有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)。  
  
> [!IMPORTANT]
>  每个策略文档只能使用此策略一次。  
>   
>  [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>示例  
 在下面的示例中，可通过调用方 IP 地址对配额进行键控。  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>元素  
  
|名称|说明|必选|  
|----------|-----------------|--------------|  
|quota|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|bandwidth|在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。|必须指定 `calls` 和/或 `bandwidth`。|不适用|  
|calls|在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。|必须指定 `calls` 和/或 `bandwidth`。|不适用|  
|counter-key|用于配额策略的密钥。|是|不适用|  
|increment-condition|一个布尔表达式，指定是否应将请求计入配额 (`true`)|否|不适用|  
|renewal-period|在重置配额之前等待的时间长度，以秒为单位。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** 全局、产品、API、操作  
  
##  <a name="ValidateJWT"></a> 验证 JWT  
 `validate-jwt` 策略强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。  
  
> [!IMPORTANT]
>  `validate-jwt` 策略要求 `exp` 注册声明包括在 JWT 令牌中，除非 `require-expiration-time` 属性已指定并设置为 `false`。  
> `validate-jwt` 策略支持 HS256 和 RS256 签名算法。 对于 HS256，必须在策略中以 base64 编码形式提供内联方式的密钥。 对于 RS256，必须通过 Open ID 配置终结点提供密钥。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>示例  
  
#### <a name="azure-mobile-services-token-validation"></a>Azure 移动服务令牌验证  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory 令牌验证  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C 令牌验证  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>根据令牌声明授予操作访问权限  
 以下示例演示了如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明预先授予操作访问权限。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 13:50。 快进到 15:00，观看在策略编辑器中配置的策略，然后快进到 18:50，观看对使用和不使用所需授权令牌从开发人员门户调用操作的演示。  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|validate-jwt|根元素。|是|  
|audiences|包含一系列可接受且可存在于令牌上的受众声明。 如果存在多个受众值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。 必须指定至少一个受众。|否|  
|issuer-signing-keys|一系列 Base64 编码的安全密钥，用于验证签名的令牌。 如果存在多个安全密钥，则会对每个密钥进行尝试，直到有一个密钥成功，以便进行令牌滚动更新（如果所有密钥都试完却没有一个成功，则表明验证失败）。 密钥元素有一个可选的 `id` 属性，用于与 `kid` 声明进行比较。|否|  
|issuers|一系列可接受的、已颁发了令牌的主体。 如果存在多个颁发者值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。|否|  
|openid-config|一个元素，用于指定兼容的 Open ID 配置终结点，以便从该终结点获取签名密钥和颁发者。|否|  
|required-claims|包含一系列应存在于令牌上的声明，否则令牌会被视为无效。 将 `match` 属性设置为 `all` 时，策略中的每个声明值都必须存在于令牌中，这样验证才会成功。 将 `match` 属性设置为 `any` 时，至少一个声明必须存在于令牌中，这样验证才会成功。|否|  
|zumo-master-key|Azure 移动服务颁发的令牌的主密钥|否|  
  
### <a name="attributes"></a>属性  
  
|名称|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|clock-skew|时间跨度。 用于指定令牌颁发者的系统时钟与 API 管理实例之间的最大预期时间差。|否|0 秒|  
|failed-validation-error-message|JWT 未通过验证时会在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。|否|默认错误消息取决于验证问题，例如“JWT 不存在”。|  
|failed-validation-httpcode|JWT 未通过验证时会返回的 HTTP 状态代码。|否|401|  
|header-name|包含令牌的 HTTP 标头的名称。|必须指定 `header-name` 或 `query-parameter-name`，但不能二者都指定。|不适用|  
|id|使用 `key` 元素的 `id` 属性可以指定一个字符串，该字符串将与令牌中的 `kid` 声明（如果存在）进行比较，以便找出进行签名验证时需要使用的适当密钥。|否|不适用|  
|match|`claim` 元素的 `match` 属性用于指定：是否策略中的每个声明值都必须存在于令牌中验证才会成功。 可能的值包括：<br /><br /> -                          `all` - 策略中的每个声明值都必须存在于令牌中验证才会成功。<br /><br /> -                          `any` - 至少一个声明值必须存在于令牌中验证才会成功。|否|本应返回的所有记录的总数，|  
|query-paremeter-name|包含令牌的查询参数的名称。|必须指定 `header-name` 或 `query-paremeter-name`，但不能二者都指定。|不适用|  
|require-expiration-time|布尔值。 指定令牌中是否需要到期声明。|否|是|
|require-scheme|令牌方案的名称，例如“Bearer”。 设置了此属性时，策略将确保 Authorization 标头值中存在指定的方案。|否|不适用|
|require-signed-tokens|布尔值。 指定令牌是否需要签名。|否|是|  
|分隔符|字符串。 指定要用于从多值声明中提取一组值的分隔符（例如 ","）。|否|不适用| 
|url|Open ID 配置终结点 URL，可以从其获取 Open ID 配置元数据。 响应应符合以下 URL 中定义的规范：`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。  对于 Azure Active Directory，请使用以下 URL：`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`，代之以目录租户名称，例如 `contoso.onmicrosoft.com`。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** 全局、产品、API、操作  
  
## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
