---
title: "Azure API 管理高级策略 | Microsoft 文档"
description: "了解可在 Azure API 管理中使用的高级策略。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6392a14f6bbc3c4708b36e3e1ab0b5b45a4d0671
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2017
---
# <a name="api-management-advanced-policies"></a>API 管理高级策略
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](http://go.microsoft.com/fwlink/?LinkID=398186)。  

##  <a name="AdvancedPolicies"></a> 高级策略  
  
-   [控制流](api-management-advanced-policies.md#choose) - 根据布尔[表达式](api-management-policy-expressions.md)的求值结果，有条件地应用策略语句。  
-   [转发请求](#ForwardRequest) - 将请求转发到后端服务。
-   [限制并发](#LimitConcurrency) - 阻止括住的策略一次执行超过指定数量的请求。
-   [记录到事件中心](#log-to-eventhub) - 将指定格式的消息发送到记录器实体定义的事件中心。 
-   [模拟响应](#mock-response) - 中止管道执行，将模拟的响应直接返回给调用方。
-   [重试](#Retry) - 重试执行括住的策略语句，直到符合条件为止。 系统会根据指定的时间间隔重复，直到执行指定的重试计数为止。  
-   [返回响应](#ReturnResponse) - 中止管道执行，将指定的响应直接返回给调用方。 
-   [发送单向请求](#SendOneWayRequest) - 将请求发送到指定的 URL，无需等待响应。  
-   [发送请求](#SendRequest) - 将请求发送到指定的 URL。  
-   [设置 HTTP 代理](#SetHttpProxy) - 允许通过 HTTP 代理路由转发请求。  
-   [设置请求方法](#SetRequestMethod) - 允许更改请求的 HTTP 方法。  
-   [设置状态代码](#SetStatus) - 将 HTTP 状态代码更改为指定的值。  
-   [设置变量](api-management-advanced-policies.md#set-variable) - 保存命名[上下文](api-management-policy-expressions.md#ContextVariables)变量中的值供以后访问。  
-   [跟踪](#Trace) - 将字符串添加到 [API 检查器](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/)输出中。  
-   [等待](#Wait) - 在继续下一步之前，等待括住的[发送请求](api-management-advanced-policies.md#SendRequest)、[从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey)或[控制流](api-management-advanced-policies.md#choose)策略完成。  
  
##  <a name="choose"></a> 控制流  
 `choose` 策略根据布尔表达式的求值结果应用括住的策略语句，类似于编程语言中的 if-then-else 或开关构造。  
  
###  <a name="ChoosePolicyStatement"></a> 策略语句  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 控制流策略必须包含至少一个 `<when/>` 元素。 `<otherwise/>` 元素是可选的。 `<when/>` 元素中的条件根据其在策略中的出现顺序求值。 将应用条件属性等于 `true` 的第一个 `<when/>` 元素中括住的策略语句。 在所有 `<when/>` 元素条件属性为 `false` 的情况下，将应用 `<otherwise/>` 元素中括住的策略（如果存在）。  
  
### <a name="examples"></a>示例  
  
####  <a name="ChooseExample"></a> 示例  
 以下示例演示 [set-variable](api-management-advanced-policies.md#set-variable) 策略和两项控制流策略。  
  
 此 set-variable 策略位于入站节，用于创建 `isMobile` 布尔[上下文](api-management-policy-expressions.md#ContextVariables)变量，该变量在 `User-Agent` 请求标头包含文本 `iPad` 或 `iPhone` 的情况下设置为 true。  
  
 第一项控制流策略也位于入站节，并会根据 `isMobile` 上下文变量的值有条件地应用两项[设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter)策略之一。  
  
 第二项控制流策略位于出站节，并会在 `isMobile` 设置为 `true` 时有条件地应用[将 XML 转换为 JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) 策略。  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>示例  
 以下示例演示了如何进行内容筛选，方法是：在使用 `Starter` 产品时删除从后端服务接收的响应中的数据元素。 有关配置和使用此策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能），快进到 34:30。 若要大致了解用于此演示的 [Dark Sky Forecast API](https://developer.forecast.io/)，请从 31:50 开始观看。  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|choose|根元素。|是|  
|when|条件，用于 `choose` 策略的 `if` 或 `ifelse` 部分。 如果 `choose` 策略包含多个 `when` 节，则按顺序对其求值。 一旦 when 元素的 `condition` 的求值结果为 `true`，不再对 `when` 条件求值。|是|  
|otherwise|包含策略代码片段，该片段在没有 `when` 条件的求值结果为 `true` 的情况下使用。|否|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|  
|---------------|-----------------|--------------|  
|condition="布尔表达式 &#124; 布尔常量"|对包含 `when` 的策略语句求值时需求值的布尔表达式或常量。|是|  
  
###  <a name="ChooseUsage"></a> 使用  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="ForwardRequest"></a> 转发请求  
 `forward-request` 策略将传入请求转发到请求[上下文](api-management-policy-expressions.md#ContextVariables)中指定的后端服务。 后端服务 URL 在 API [设置](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings)中指定，可以使用[设置后端服务](api-management-transformation-policies.md)策略进行更改。  
  
> [!NOTE]
>  删除此策略之后，请求就不会转发到后端服务。一旦成功完成入站节中的策略，就会立即对出站节中的策略求值。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>示例  
  
#### <a name="example"></a>示例  
 以下 API 级策略将所有请求都转发到后端服务，超时间隔设置为 60 秒。  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>示例  
 以下操作级策略使用 `base` 元素，从父 API 级范围继承后端策略。  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>示例  
 以下操作级策略显式将所有请求转发到后端服务，超时设置为 120 秒，不继承父 API 级后端策略。  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>示例  
 以下操作级策略不将请求转发到后端服务。  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|forward-request|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|timeout="整数"|以秒为单位的超时间隔，此时间过后对后端服务的调用会失败。|否|300 秒|  
|follow-redirects="true &#124; false"|指定是由网关执行从后端服务的重定向，还是将重定向返回到调用方。|否|false|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**后端  
-   **策略范围：**所有范围  
  
##  <a name="LimitConcurrency"></a> 限制并发  
 `limit-concurrency` 策略阻止括住的策略在给定时间执行超过指定数量的请求。 超过该数量后，新请求将立即失败并显示“429 请求过多”状态代码。
  
###  <a name="LimitConcurrencyStatement"></a> 策略语句  
  
```xml  
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>示例  
  
#### <a name="example"></a>示例  
 下例演示了如何基于上下文变量的值限制转发到后端的请求数。
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|    
|limit-concurrency|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|--------------|  
|key|一个字符串。 允许使用表达式。 指定并发作用域。 可以由多个策略共享。|是|不适用|  
|max-count|一个整数。 指定允许输入策略的最大请求数。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  

##  <a name="log-to-eventhub"></a> 记录到事件中心  
 `log-to-eventhub` 策略将指定格式的消息发送到记录器实体定义的事件中心。 从名称可以看出，此策略用于保存所选请求或响应上下文信息，以便进行联机或脱机分析。  
  
> [!NOTE]
>  如需配置事件中心和记录事件的分步指南，请参阅[如何在 Azure API 管理中将事件记录到 Azure 事件中心](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/)。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>示例  
 可以将任何字符串用作要记录到事件中心的值。 在以下示例中，所有入站调用的日期和时间、部署服务名称、请求 ID、IP 地址和操作名称都记录到以 `contoso-logger` ID 注册的事件中心记录器。  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|log-to-eventhub|根元素。 此元素的值是要记录到事件中心的字符串。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|  
|---------------|-----------------|--------------|  
|logger-id|注册到 API 管理服务的记录器的 ID。|是|  
|partition-id|指定在其中发送消息的分区的索引。|可选。 如果使用 `partition-key`，则不能使用此属性。|  
|partition-key|指定在发送消息时用于分区分配的值。|可选。 如果使用 `partition-id`，则不能使用此属性。|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  

##  <a name="mock-response"></a>模拟响应  
正如其名所示，`mock-response` 用于模拟 API 和操作。 它会中止正常的管道执行，并将模拟的响应返回给调用方。 该策略始终尝试返回保真度最高的响应。 它首选响应内容示例（若可用）。 若提供架构而不提供示例，它将从架构生成示例响应。 如果既找不到示例又找不到架构，则返回无内容的响应。
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>示例  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|mock-response|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|--------------|  
|status-code|指定响应状态代码并用于选择相应的示例或架构。|否|200|  
|content-type|指定 `Content-Type` 响应标头值，并用于选择相应的示例或架构。|否|无|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、错误时  
  
-   **策略范围：**所有范围

##  <a name="Retry"></a> 重试  
 `retry` 策略会执行其子策略一次，并重新尝试执行，直至重试 `condition` 变为 `false`，或者重试 `count` 为零。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>示例  
 在以下示例中，请求转发最高可使用指数重试算法重试 10 次。 由于 `first-fast-retry` 设置为 false，所有重试都必须遵循指数重试算法。  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|retry|根元素。 可能包含任何可充当其子元素的其他策略。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|条件|一个布尔文本或[表达式](api-management-policy-expressions.md)，指定是应停止重试 (`false`) 还是应继续重试 (`true`)。|是|不适用|  
|count|一个正数，指定进行尝试时的最大重试次数。|是|不适用|  
|interval|一个以秒为单位的正数，指定两次重试之间的等待时间。|是|不适用|  
|max-interval|一个以秒为单位的正数，指定两次重试之间的最长等待时间， 用于实现指数重试算法。|否|不适用|  
|delta|一个以秒为单位的正数，指定等待时间间隔增量， 用于实现线性和指数重试算法。|否|不适用|  
|first-fast-retry|如果设置为 `true`，则会立即执行首次重试。|否|`false`|  
  
> [!NOTE]
>  仅指定 `interval` 时，则会执行**固定**时间间隔的重试。  
>  仅指定 `interval` 和 `delta` 时，将使用**线性**时间间隔重试算法，其中，两次重试之间的等待时间按以下公式计算：`interval + (count - 1)*delta`。  
>  指定 `interval`、`max-interval`、`delta` 时，将应用**指数**时间间隔重试算法，其中，两次重试之间的等待时间的值按以下公式以指数方式从 `interval` 增长到 `max-interval`：`min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`。  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。 请注意，此策略会继承子策略使用限制。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="ReturnResponse"></a> 返回响应  
 `return-response` 策略会中止管道执行，为调用方返回默认响应或自定义响应。 默认响应为`200 OK`，无正文。 可以通过上下文变量或策略语句指定自定义响应。 二者都提供时，会通过策略语句对上下文变量中包含的响应进行修改，然后再将其返回给调用方。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>示例  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|return-response|根元素。|是|  
|set-header|[set-header](api-management-transformation-policies.md#SetHTTPheader) 策略语句。|否|  
|set-body|[set-body](api-management-transformation-policies.md#SetBody) 策略语句。|否|  
|set-status|[set-status](api-management-advanced-policies.md#SetStatus) 策略语句。|否|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|  
|---------------|-----------------|--------------|  
|response-variable-name|上下文变量的名称，该变量引用自特定的策略（例如上游 [send-request](api-management-advanced-policies.md#SendRequest) 策略）且包含 `Response` 对象|可选。|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="SendOneWayRequest"></a> 发送单向请求  
 `send-one-way-request` 策略将提供的请求发送到指定的 URL，无需等待响应。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>示例  
 以下示例策略以示例方式演示了在 HTTP 响应代码大于或等于 500 的情况下，如何使用 `send-one-way-request` 策略将消息发送到 Slack 聊天室。 有关此示例的详细信息，请参阅[通过 Azure API 管理服务使用外部服务](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)。  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|send-one-way-request|根元素。|是|  
|url|请求的 URL。|如果 mode=copy，则为否；否则为是。|  
|方法|用于请求的 HTTP 方法。|如果 mode=copy，则为否；否则为是。|  
|标头的值开始缓存响应|请求标头。 将多个标头元素用于多个请求标头。|否|  
|body|请求正文。|否|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|确定请求是新请求还是当前请求的副本。 在出站模式下，mode=copy 不会初始化请求正文。|否|新建|  
|name|指定要设置的标头的名称。|是|不适用|  
|exists-action|指定当标头已指定时要执行的操作。 此属性必须具有下列值之一。<br /><br /> -   override - 替换现有标头的值。<br />-   skip - 不替换现有标头值。<br />-   append - 将值追加到现有标头值。<br />-   delete - 从请求中删除标头。<br /><br /> 如果设置为 `override`，则登记多个同名的条目会导致根据所有条目（将多次列出）设置标头；结果中只会设置列出的值。|否|override|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="SendRequest"></a> 发送请求  
 `send-request` 策略将提供的请求发送至指定 URL，等待时间不超过设置的超时值。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>示例  
 以下示例演示了如何通过授权服务器验证引用令牌。 有关此示例的详细信息，请参阅[通过 Azure API 管理服务使用外部服务](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)。  
  
```xml  
<inbound>  
  <!-- Extract Token from Authorization header parameter -->  
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />  
  
  <!-- Send request to Token Server to validate token (see RFC 7662) -->  
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">  
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>  
    <set-method>POST</set-method>  
    <set-header name="Authorization" exists-action="override">  
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>  
    </set-header>  
    <set-header name="Content-Type" exists-action="override">  
      <value>application/x-www-form-urlencoded</value>  
    </set-header>  
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>  
  </send-request>  
  
  <choose>  
        <!-- Check active property in response -->  
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
            <!-- Return 401 Unauthorized with http-problem payload -->  
            <return-response response-variable-name="existing response variable">  
                <set-status code="401" reason="Unauthorized" />  
                <set-header name="WWW-Authenticate" exists-action="override">  
                    <value>Bearer error="invalid_token"</value>  
                </set-header>  
            </return-response>  
        </when>  
    </choose>  
  <base />  
</inbound>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|send-request|根元素。|是|  
|url|请求的 URL。|如果 mode=copy，则为否；否则为是。|  
|方法|用于请求的 HTTP 方法。|如果 mode=copy，则为否；否则为是。|  
|标头的值开始缓存响应|请求标头。 将多个标头元素用于多个请求标头。|否|  
|body|请求正文。|否|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|确定请求是新请求还是当前请求的副本。 在出站模式下，mode=copy 不会初始化请求正文。|否|新建|  
|response-variable-name="string"|如果不存在，则使用 `context.Response`。|否|不适用|  
|timeout="整数"|以秒为单位的超时间隔，此时间过后对 URL 的调用会失败。|否|60|  
|ignore-error|如果为 true，请求会导致错误：<br /><br /> -   如果 response-variable-name 已指定，则其中会包含 null 值。<br />-   如果 response-variable-name 未指定，则不会更新 context.Request。|否|false|  
|name|指定要设置的标头的名称。|是|不适用|  
|exists-action|指定当标头已指定时要执行的操作。 此属性必须具有下列值之一。<br /><br /> -   override - 替换现有标头的值。<br />-   skip - 不替换现有标头值。<br />-   append - 将值追加到现有标头值。<br />-   delete - 从请求中删除标头。<br /><br /> 如果设置为 `override`，则登记多个同名的条目会导致根据所有条目（将多次列出）设置标头；结果中只会设置列出的值。|否|override|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="SetHttpProxy"></a>设置 HTTP 代理  
 `proxy` 策略允许通过 HTTP 代理将转发的请求路由到后端。 网关和代理之间仅支持 HTTP（而不是 HTTPS）。 仅限基本和 NTLM 身份验证。
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>示例  
请注意使用[属性](api-management-howto-properties.md)作为用户名和密码的值，以避免将敏感信息存储在策略文档中。  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|proxy|Root 元素|是|  

### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|url="string"|http://host:port 形式的代理 URL。|是|不适用|  
|username="string"|要用于向代理进行身份验证的用户名。|否|不适用|  
|password="string"|要用于向代理进行身份验证的密码。|否|不适用|  

### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：**入站  
  
-   **策略范围：**所有范围  

##  <a name="SetRequestMethod"></a> 设置请求方法  
 `set-method` 策略用于更改请求的 HTTP 请求方法。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>示例  
 以下示例策略使用 `set-method` 策略，以示例方式演示了在 HTTP 响应代码大于或等于 500 的情况下，如何将消息发送到 Slack 聊天室。 有关此示例的详细信息，请参阅[通过 Azure API 管理服务使用外部服务](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)。  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|set-method|根元素。 此元素的值指定 HTTP 方法。|是|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="SetStatus"></a> 设置状态代码  
 `set-status` 策略将 HTTP 状态代码设置为指定的值。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>示例  
 以下示例演示如何在授权令牌无效的情况下返回 401 响应。 有关详细信息，请参阅[通过 Azure API 管理服务使用外部服务](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)  
  
```xml  
<choose>  
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
    <return-response response-variable-name="existing response variable">  
      <set-status code="401" reason="Unauthorized" />  
      <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
      </set-header>  
    </return-response>  
  </when>  
</choose>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|set-status|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|code="整数"|要返回的 HTTP 状态代码。|是|不适用|  
|reason="字符串"|说明返回状态代码的原因。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**出站、后端、错误时  
-   **策略范围：**所有范围  

##  <a name="set-variable"></a> 设置变量  
 `set-variable` 策略声明[上下文](api-management-policy-expressions.md#ContextVariables)变量，并为其分配通过[表达式](api-management-policy-expressions.md)或字符串文本指定的值。 如果表达式包含文本，则会将其转换为字符串，值的类型将为 `System.String`。  
  
###  <a name="set-variablePolicyStatement"></a> 策略语句  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a> 示例  
 以下示例演示入站节中的 set-variable 策略。 此 set-variable 策略用于创建 `isMobile` 布尔[上下文](api-management-policy-expressions.md#ContextVariables)变量，该变量在 `User-Agent` 请求标头包含文本 `iPad` 或 `iPhone` 的情况下设置为 true。  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|set-variable|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|  
|---------------|-----------------|--------------|  
|name|变量的名称。|是|  
|value|变量的值， 可以是表达式或文本值。|是|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
-   **策略范围：**所有范围  
  
###  <a name="set-variableAllowedTypes"></a> 允许的类型  
 在 `set-variable` 策略中使用的表达式必须返回以下基本类型之一。  
  
-   System.Boolean  
-   System.SByte  
-   System.Byte  
-   System.UInt16  
-   System.UInt32  
-   System.UInt64  
-   System.Int16  
-   System.Int32  
-   System.Int64  
-   System.Decimal  
-   System.Single  
-   System.Double  
-   System.Guid  
-   System.String  
-   System.Char  
-   System.DateTime  
-   System.TimeSpan  
-   System.Byte?  
-   System.UInt16?  
-   System.UInt32?  
-   System.UInt64?  
-   System.Int16?  
-   System.Int32?  
-   System.Int64?  
-   System.Decimal?  
-   System.Single?  
-   System.Double?  
-   System.Guid?  
-   System.String?  
-   System.Char?  
-   System.DateTime?  

##  <a name="Trace"></a>跟踪  
 `trace` 策略将字符串添加到 [API 检查器](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/)输出中。 此策略会执行的前提是触发跟踪，即 `Ocp-Apim-Trace` 请求标头存在且设置为 `true`，同时 `Ocp-Apim-Subscription-Key` 请求标头存在且包含与管理员帐户关联的有效密钥。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|trace|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|源|对跟踪查看器有意义的字符串文本，指定消息的源。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端、错误时  
  
-   **策略范围：**所有范围  
  
##  <a name="Wait"></a> 等待  
 `wait` 策略以并行方式执行其直接子策略，在完成之前会等待其直接子策略全部完成或其中一个完成。 等待策略可以将[发送请求](api-management-advanced-policies.md#SendRequest)、[从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey)和[控制流](api-management-advanced-policies.md#choose)策略设置为其直接子策略。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>示例  
 在以下示例中有两个 `choose` 策略，充当 `wait` 策略的直接子策略。 这些 `choose` 策略都可以并行执行。 每个 `choose` 策略都会尝试检索缓存的值。 如果缓存未命中，则会调用后端服务来提供值。 在此示例中，`wait` 策略在完成之前必须等待其所有直接子策略完成，因为 `for` 属性设置为 `all`。   在此示例中，上下文变量（`execute-branch-one`、`value-one`、`execute-branch-two`、`value-two`）是在此示例策略范围外声明的。  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>元素  
  
|元素|说明|必选|  
|-------------|-----------------|--------------|  
|wait|根元素。 可能只包含 `send-request`、`cache-lookup-value`、`choose` 策略作为子元素。|是|  
  
### <a name="attributes"></a>属性  
  
|属性|说明|必选|默认|  
|---------------|-----------------|--------------|-------------|  
|for|确定 `wait` 策略是等待所有直接子策略完成，还是只等待其中之一完成。 允许值包括：<br /><br /> -   `all` - 等待所有直接子策略完成<br />-   any - 等待任一直接子策略完成。 第一个直接子策略完成后，`wait` 策略即告完成，同时会终止执行任何其他直接子策略。|否|本应返回的所有记录的总数，|  
  
### <a name="usage"></a>使用情况  
 
此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略节：**入站、出站、后端  
-   **策略范围：**所有范围  
  
## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：
+ [API 管理中的策略](api-management-howto-policies.md) 
+ [策略表达式](api-management-policy-expressions.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
