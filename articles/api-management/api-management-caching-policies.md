---
title: Azure API 管理缓存策略 | Microsoft 文档
description: 了解可在 Azure API 管理中使用的缓存策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 488a4c4b7daf5c07ca5f6b6bb72464279658d372
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
ms.locfileid: "26344816"
---
# <a name="api-management-caching-policies"></a>API 管理缓存策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](http://go.microsoft.com/fwlink/?LinkID=398186)。  
  
##  <a name="CachingPolicies"></a> 缓存策略  
  
-   响应缓存策略  
  
    -   [从缓存中获取](api-management-caching-policies.md#GetFromCache) - 执行缓存查找，并返回有效的缓存响应（如果有）。  
    -   [存储到缓存](api-management-caching-policies.md#StoreToCache) - 根据指定的缓存控制配置来缓存响应。  
  
-   值缓存策略  

    -   [从缓存中获取值](#GetFromCacheByKey) - 根据密钥检索缓存的项。 
    -   [在缓存中存储值](#StoreToCacheByKey) - 根据密钥在缓存中存储项。 
    -   [从缓存中删除值](#RemoveCacheByKey) - 根据密钥在缓存中删除项。  
  
##  <a name="GetFromCache"></a> 从缓存中获取  
 使用 `cache-lookup` 策略执行缓存查找，并返回有效的缓存响应（如果有）。 当响应内容在某个时间段内保持静态时，即可应用该策略。 响应缓存可以降低后端 Web 服务器需要满足的带宽和处理能力要求，并可以减小 API 使用者能够察觉到的延迟。  
  
> [!NOTE]
>  此策略必须已设置相应的[存储到缓存](api-management-caching-policies.md#StoreToCache)策略。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-private-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>示例  
  
#### <a name="example"></a>示例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>策略表达式使用示例  
 此示例演示如何配置 API 管理响应缓存持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 25:25。  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 有关详细信息，请参阅[策略表达式](api-management-policy-expressions.md)和[上下文变量](api-management-policy-expressions.md#ContextVariables)。  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cache-lookup|根元素。|是|  
|vary-by-header|开始按指定标头（例如 Accept、Accept-Charset、Accept-Encoding、Accept-Language、Authorization、Expect、From、Host、If-Match）的值缓存响应。|否|  
|vary-by-query-parameter|根据指定查询参数的值开始缓存响应。 请输入一个或多个参数。 使用分号作为分隔符。 如果未指定任何参数，将使用所有查询参数。|否|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|allow-private-response-caching|设置为 `true` 即可缓存包含 Authorization 标头的请求。|否|false|  
|downstream-caching-type|此属性必须设置为以下值之一。<br /><br /> -   none - 不允许下游缓存。<br />-   private - 允许下游专用缓存。<br />-   public - 允许专用和共享下游缓存。|否|无|  
|must-revalidate|启用下游缓存时，此属性会启用或关闭网关响应中的 `must-revalidate` 缓存控制指令。|否|true|  
|vary-by-developer|设置为 `true` 即可按开发人员密钥缓存响应。|是||  
|vary-by-developer-groups|设置为 `true` 即可按用户角色缓存响应。|是||  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** API、操作、产品  
  
##  <a name="StoreToCache"></a> 存储到缓存  
 `cache-store` 策略根据指定的缓存设置缓存响应。 当响应内容在某个时间段内保持静态时，即可应用该策略。 响应缓存可以降低后端 Web 服务器需要满足的带宽和处理能力要求，并可以减小 API 使用者能够察觉到的延迟。  
  
> [!NOTE]
>  此策略必须已设置相应的[从缓存中获取](api-management-caching-policies.md#GetFromCache)策略。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>示例  
  
#### <a name="example"></a>示例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>策略表达式使用示例  
 此示例演示如何配置 API 管理响应缓存持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 25:25。  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 有关详细信息，请参阅[策略表达式](api-management-policy-expressions.md)和[上下文变量](api-management-policy-expressions.md#ContextVariables)。  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cache-store|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|duration|缓存条目的生存时间，以秒为单位指定。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 出站    
-   **策略范围：** API、操作、产品  
  
##  <a name="GetFromCacheByKey"></a> 从缓存中获取值  
 使用 `cache-lookup-value` 策略，可以通过密钥执行缓存查找，并返回缓存的值。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。  
  
> [!NOTE]
>  此策略必须已设置相应的[在缓存中存储值](#StoreToCacheByKey)策略。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>示例  
 有关此策略的详细信息和示例，请参阅 [Azure API 管理中的自定义缓存](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)。  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cache-lookup-value|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|default-value|在缓存密钥查找未命中的情况下，会分配给变量的值。 如果未指定此属性，则会分 `null`。|否|`null`|  
|key|要在查找中使用的缓存密钥值。|是|不适用|  
|variable-name|在查找成功的情况下，会向其分配查找值的[上下文变量](api-management-policy-expressions.md#ContextVariables)的名称。 如果查找未命中，则会为此变量分配 `default-value` 属性的值或 `null`（如果省略了 `default-value` 属性）。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：** 入站、出站、后端、错误时  
-   **策略范围：** 全局、API、操作、产品  
  
##  <a name="StoreToCacheByKey"></a> 在缓存中存储值  
 `cache-store-value` 按密钥执行缓存存储。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。  
  
> [!NOTE]
>  此策略必须已设置相应的[从缓存中获取值](#GetFromCacheByKey)策略。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>示例  
 有关此策略的详细信息和示例，请参阅 [Azure API 管理中的自定义缓存](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)。  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cache-store-value|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|duration|会根据提供的期间值（以秒为单位指定）将值缓存一段时间。|是|不适用|  
|key|缓存密钥，会在其下存储值。|是|不适用|  
|value|要缓存的值。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：** 入站、出站、后端、错误时  
-   **策略范围：** 全局、API、操作、产品  
  
###  <a name="RemoveCacheByKey"></a> 从缓存中删除值  
`cache-remove-value` 删除通过密钥标识的缓存项。 密钥的值可以是任意字符串，通常使用策略表达式来提供密钥。  
  
#### <a name="policy-statement"></a>策略语句  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>示例  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cache-remove-value|根元素。|是|  
  
#### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|key|以前所缓存的值（将从缓存中删除）的密钥。|是|不适用|  
  
#### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：** 入站、出站、后端、错误时  
-   **策略范围：** 全局、API、操作、产品  

## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
