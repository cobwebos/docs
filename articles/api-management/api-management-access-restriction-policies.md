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
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374259"
---
# <a name="api-management-access-restriction-policies"></a>API 管理访问限制策略

本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](https://go.microsoft.com/fwlink/?LinkID=398186)。

## <a name="AccessRestrictionPolicies"></a> 访问限制策略

-   [检查 HTTP 标头](api-management-access-restriction-policies.md#CheckHTTPHeader) - 强制必须存在 HTTP 标头和/或强制采用 HTTP 标头的值。
-   [按订阅限制调用速率](api-management-access-restriction-policies.md#LimitCallRate) - 根据订阅限制调用速率，避免 API 使用量暴增。
-   [按密钥限制调用率](#LimitCallRateByKey) - 根据密钥限制调用率以避免 API 使用量暴增。
-   [限制调用方 IP](api-management-access-restriction-policies.md#RestrictCallerIPs) - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。
-   [按订阅设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuota) - 允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。
-   [按密钥设置使用量配额](#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。
-   [验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。

> [!TIP]
> 可以出于不同目的使用不同范围中的访问限制策略。 例如，你可以通过在 API 级别应用 `validate-jwt` 策略来使用 AAD 身份验证来保护整个 API，也可以在 API 操作级别应用该策略，并使用 `claims` 进行更精细的控制。

## <a name="CheckHTTPHeader"></a> 检查 HTTP 标头

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

| 名称         | 说明                                                                                                                                   | 必选 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | 根元素。                                                                                                                                 | 是      |
| 值        | 允许的 HTTP 标头值。 指定了多个值元素时，如果任何一个值匹配，则可认为检查成功。 | 否       |

### <a name="attributes"></a>属性

| 名称                       | 说明                                                                                                                                                            | 必选 | 默认 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | 在标头不存在或其值无效的情况下，需要在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。 | 是      | 空值     |
| failed-check-httpcode      | 在标头不存在或其值无效时需返回的 HTTP 状态代码。                                                                                        | 是      | 空值     |
| header-name                | 要检查的 HTTP 标头的名称。                                                                                                                                  | 是      | 空值     |
| ignore-case                | 可以设置为 True 或 False。 如果设置为 True，则在将标头值与一组可接受的值进行比较时，会忽略大小写。                                    | 是      | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略节：** 入站、出站

-   **策略范围：** 所有范围

## <a name="LimitCallRate"></a> 按订阅限制调用速率

`rate-limit` 策略可以对调用速率进行限制，使指定时段的调用不超出指定的数目，避免单个订阅的 API 使用量暴增。 触发此策略时，调用方会收到`429 Too Many Requests`响应状态代码。

> [!IMPORTANT]
> 每个策略文档只能使用此策略一次。
>
> [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。

> [!CAUTION]
> 由于限制体系结构的分布式特性，速率限制决不会完全准确。 已配置和实际数量的允许请求之间的差异取决于请求量和速率、后端延迟和其他因素。

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

| 名称       | 说明                                                                                                                                                                                                                                                                                              | 必选 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | 根元素。                                                                                                                                                                                                                                                                                            | 是      |
| api        | 添加一个或多个此类元素，对产品中的 Api 施加调用速率限制。 产品和 API 的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。                    | 否       |
| operation  | 添加一个或多个此类元素，对 API 中的操作施加调用速率限制。 产品、API 和操作的调用速率限制是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。 | 否       |

### <a name="attributes"></a>属性

| 名称           | 说明                                                                                           | 必选 | 默认 |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | 要对其应用速率限制的 API 的名称。                                                | 是      | 空值     |
| calls          | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。 | 是      | 空值     |
| renewal-period | 在重置配额之前等待的时间长度，以秒为单位。                                              | 是      | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站

-   **策略范围：** 产品、api、操作

## <a name="LimitCallRateByKey"></a> 按密钥限制调用速率

> [!IMPORTANT]
> 此功能在 API 管理的“消耗”层中不可用。

`rate-limit-by-key` 策略可以对调用速率进行限制，使指定时段的调用不超出指定的数目，避免单个密钥的 API 使用量暴增。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定在决定是否到达限制值时应该进行计数的请求。 触发此策略时，调用方会收到`429 Too Many Requests`响应状态代码。

有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)。

> [!CAUTION]
> 由于限制体系结构的分布式特性，速率限制决不会完全准确。 已配置和实际数量的允许请求之间的差异取决于请求量和速率、后端延迟和其他因素。

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

| 名称              | 说明   | 必选 |
| ----------------- | ------------- | -------- |
| 速率-按键限制 | 根元素。 | 是      |

### <a name="attributes"></a>属性

| 名称                | 说明                                                                                           | 必选 | 默认 |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。 | 是      | 空值     |
| counter-key         | 用于速率限制策略的密钥。                                                             | 是      | 空值     |
| increment-condition | 一个布尔表达式，指定是否应将请求计入配额 (`true`)。        | 否       | 空值     |
| renewal-period      | 在重置配额之前等待的时间长度，以秒为单位。                                              | 是      | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站

-   **策略范围：** 所有范围

## <a name="RestrictCallerIPs"></a> 限制调用方 IP

`ip-filter` 策略筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。

### <a name="policy-statement"></a>策略语句

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>示例

在以下示例中，策略只允许来自指定的单个 IP 地址或 IP 地址范围的请求

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>元素

| 名称                                      | 说明                                         | 必选                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | 根元素。                                       | 是                                                            |
| address                                   | 指定要对其进行筛选的单个 IP 地址。   | 至少一个 `address` 或 `address-range` 元素是必需的。 |
| address-range from="address" to="address" | 指定要对其进行筛选的 IP 地址范围。 | 至少一个 `address` 或 `address-range` 元素是必需的。 |

### <a name="attributes"></a>属性

| 名称                                      | 说明                                                                                 | 必选                                           | 默认 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | 允许或拒绝其访问的某个 IP 地址范围。                                        | 使用 `address-range` 元素时必需。 | 空值     |
| ip-filter action="allow &#124; forbid"    | 指定是否应允许指定的 IP 地址和范围执行调用。 | 是                                                | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站
-   **策略范围：** 所有范围

## <a name="SetUsageQuota"></a> 按订阅设置使用量配额

`quota` 策略允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。

> [!IMPORTANT]
> 每个策略文档只能使用此策略一次。
>
> [策略表达式](api-management-policy-expressions.md)不能用于此策略的任何策略属性。

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

| 名称      | 说明                                                                                                                                                                                                                                                                                  | 必选 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | 根元素。                                                                                                                                                                                                                                                                                | 是      |
| api       | 添加一个或多个此类元素，以便对产品中的 Api 施加调用配额。 产品和 API 的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 API。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。                    | 否       |
| operation | 添加一个或多个此类元素，以便对 API 中的操作施加调用配额。 产品、API 和操作的调用配额是分别应用的。 可以通过 `name` 或 `id` 引用 Operation。 如果同时提供了这两个属性，则将使用 `id` 并忽略 `name`。 | 否       |

### <a name="attributes"></a>属性

| 名称           | 说明                                                                                               | 必选                                                         | 默认 |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | 要向其应用配额的 API 或操作的名称。                                             | 是                                                              | 空值     |
| bandwidth      | 在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。 | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| calls          | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。     | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| renewal-period | 在重置配额之前等待的时间长度，以秒为单位。                                                  | 是                                                              | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站
-   **策略范围：** 产品

## <a name="SetUsageQuotaByKey"></a> 按密钥设置使用量配额

> [!IMPORTANT]
> 此功能在 API 管理的“消耗”层中不可用。

`quota-by-key` 策略允许根据密钥强制实施可续订或有生存期的调用量和/或带宽配额。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。 可以添加可选增量条件，指定应在配额范围内的请求。 如果多个策略增加相同的键值，则每个请求的键值仅增加一次。 达到调用限制时，调用方会收到 `403 Forbidden` 响应状态代码。

有关此策略的详细信息和示例，请参阅[使用 Azure API 管理进行高级请求限制](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)。

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

| 名称  | 说明   | 必选 |
| ----- | ------------- | -------- |
| quota | 根元素。 | 是      |

### <a name="attributes"></a>属性

| 名称                | 说明                                                                                               | 必选                                                         | 默认 |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | 在 `renewal-period` 所指定的时间间隔内允许的最大总字节数（千字节）。 | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| calls               | 在 `renewal-period` 所指定的时间间隔内允许的最大总调用数。     | 必须指定 `calls` 和/或 `bandwidth`。 | 空值     |
| counter-key         | 用于配额策略的密钥。                                                                      | 是                                                              | 空值     |
| increment-condition | 一个布尔表达式，指定是否应将请求计入配额 (`true`)             | 否                                                               | 空值     |
| renewal-period      | 在重置配额之前等待的时间长度，以秒为单位。                                                  | 是                                                              | 空值     |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站
-   **策略范围：** 所有范围

## <a name="ValidateJWT"></a> 验证 JWT

`validate-jwt` 策略强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。

> [!IMPORTANT]
> `validate-jwt` 策略要求 `exp` 注册声明包括在 JWT 令牌中，除非 `require-expiration-time` 属性已指定并设置为 `false`。
> `validate-jwt` 策略支持 HS256 和 RS256 签名算法。 对于 HS256，必须在策略中以 base64 编码形式提供内联方式的密钥。 对于 RS256，必须通过 Open ID 配置终结点提供密钥。
> `validate-jwt` 策略通过加密算法 A128CBC-HS256、A192CBC-HS384、A256CBC-HS512 支持使用对称密钥加密的令牌。

### <a name="policy-statement"></a>策略语句

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
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
</validate-jwt>

```

### <a name="examples"></a>示例

#### <a name="simple-token-validation"></a>简单令牌验证

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
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

此示例演示如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT)策略根据令牌声明值授予对操作的访问权限。

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>元素

| 元素             | 说明                                                                                                                                                                                                                                                                                                                                           | 必选 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | 根元素。                                                                                                                                                                                                                                                                                                                                         | 是      |
| audiences           | 包含一系列可接受且可存在于令牌上的受众声明。 如果存在多个受众值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。 必须指定至少一个受众。                                                                     | 否       |
| issuer-signing-keys | 一系列 Base64 编码的安全密钥，用于验证签名的令牌。 如果存在多个安全密钥，则会对每个密钥进行尝试，直到有一个密钥成功，以便进行令牌滚动更新（如果所有密钥都试完却没有一个成功，则表明验证失败）。 密钥元素有一个可选的 `id` 属性，用于与 `kid` 声明进行比较。               | 否       |
| decryption-keys     | 用于解密令牌的 Base64 编码密钥列表。 如果存在多个安全密钥，则会对每个密钥进行尝试，直到所有密钥都试完（在这种情况下验证失败）或直到有一个密钥成功为止。 密钥元素有一个可选的 `id` 属性，用于与 `kid` 声明进行比较。                                                 | 否       |
| issuers             | 一系列可接受的、已颁发了令牌的主体。 如果存在多个颁发者值，则会对每个值进行尝试，直到有一个值成功（如果所有值都试完却没有一个成功，则表明验证失败）。                                                                                                                                         | 否       |
| openid-config       | 一个元素，用于指定兼容的 Open ID 配置终结点，以便从该终结点获取签名密钥和颁发者。                                                                                                                                                                                                                        | 否       |
| required-claims     | 包含一系列应存在于令牌上的声明，否则令牌会被视为无效。 将 `match` 属性设置为 `all` 时，策略中的每个声明值都必须存在于令牌中，这样验证才会成功。 将 `match` 属性设置为 `any` 时，至少一个声明必须存在于令牌中，这样验证才会成功。 | 否       |

### <a name="attributes"></a>属性

| 名称                            | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                            | 必选                                                                         | 默认                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | 时间跨度。 用于指定令牌颁发者的系统时钟与 API 管理实例之间的最大预期时间差。                                                                                                                                                                                                                                                                                                               | 否                                                                               | 0 秒                                                                         |
| failed-validation-error-message | JWT 未通过验证时会在 HTTP 响应正文中返回的错误消息。 此消息必须对任何特殊字符正确地进行转义。                                                                                                                                                                                                                                                                                                 | 否                                                                               | 默认错误消息取决于验证问题，例如“JWT 不存在”。 |
| failed-validation-httpcode      | JWT 未通过验证时会返回的 HTTP 状态代码。                                                                                                                                                                                                                                                                                                                                                                                         | 否                                                                               | 401                                                                               |
| header-name                     | 包含令牌的 HTTP 标头的名称。                                                                                                                                                                                                                                                                                                                                                                                                         | 必须指定 `header-name`、`query-parameter-name` 或 `token-value` 之一。 | 空值                                                                               |
| query-parameter-name            | 包含令牌的查询参数的名称。                                                                                                                                                                                                                                                                                                                                                                                                     | 必须指定 `header-name`、`query-parameter-name` 或 `token-value` 之一。 | 空值                                                                               |
| 标记-值                     | 返回包含 JWT 令牌的字符串的表达式                                                                                                                                                                                                                                                                                                                                                                                                     | 必须指定 `header-name`、`query-parameter-name` 或 `token-value` 之一。 | 空值                                                                               |
| id                              | 使用 `id` 元素的 `key` 属性可以指定一个字符串，该字符串将与令牌中的 `kid` 声明（如果存在）进行比较，以便找出进行签名验证时需要使用的适当密钥。                                                                                                                                                                                                                                           | 否                                                                               | 空值                                                                               |
| match                           | `match` 元素的 `claim` 属性用于指定：是否策略中的每个声明值都必须存在于令牌中验证才会成功。 可能的值包括：<br /><br /> - `all` - 策略中的每个声明值都必须存在于令牌中验证才会成功。<br /><br /> - `any` - 至少一个声明值必须存在于令牌中验证才会成功。                                                       | 否                                                                               | all                                                                               |
| require-expiration-time         | 布尔值。 指定令牌中是否需要到期声明。                                                                                                                                                                                                                                                                                                                                                                               | 否                                                                               | true                                                                              |
| require-scheme                  | 令牌方案的名称，例如 "持有者"。 设置了此属性时，策略将确保 Authorization 标头值中存在指定的方案。                                                                                                                                                                                                                                                                                    | 否                                                                               | 空值                                                                               |
| require-signed-tokens           | 布尔值。 指定令牌是否需要签名。                                                                                                                                                                                                                                                                                                                                                                                           | 否                                                                               | true                                                                              |
| separator                       | 字符串。 指定要用于从多值声明中提取一组值的分隔符（例如 ","）。                                                                                                                                                                                                                                                                                                                                          | 否                                                                               | 空值                                                                               |
| url                             | Open ID 配置终结点 URL，可以从其获取 Open ID 配置元数据。 响应应符合以下 URL 中定义的规范：`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。 对于 Azure Active Directory，请使用以下 URL：`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration`，代之以目录租户名称，例如 `contoso.onmicrosoft.com`。 | 是                                                                              | 空值                                                                               |
| output-token-variable-name      | 字符串。 成功令牌验证后，将接收令牌值作为类型[`Jwt`](api-management-policy-expressions.md)的对象的上下文变量的名称                                                                                                                                                                                                                                                                                     | 否                                                                               | 空值                                                                               |

### <a name="usage"></a>使用情况

此策略可在以下策略[段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。

-   **策略段：** 入站
-   **策略范围：** 所有范围

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

-   [API 管理中的策略](api-management-howto-policies.md)
-   [转换 API](transform-api.md)
-   [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
-   [策略示例](policy-samples.md)
