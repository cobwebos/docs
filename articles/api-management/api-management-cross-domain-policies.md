---
title: Azure API 管理跨域策略 | Microsoft 文档
description: 了解可在 Azure API 管理中使用的跨域策略。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129028"
---
# <a name="api-management-cross-domain-policies"></a>API Management cross domain policies（API 管理跨域策略）
本主题提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](http://go.microsoft.com/fwlink/?LinkID=398186)。  
  
##  <a name="CrossDomainPolicies"></a> 跨域策略  
  
-   [允许跨域调用](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - 使 API 能够通过 Adobe Flash 和基于 Microsoft Silverlight 浏览器的客户端进行访问。  
-   [CORS](api-management-cross-domain-policies.md#CORS) - 向操作或 API 添加跨源资源共享 (CORS) 支持，允许从基于浏览器的客户端进行跨域调用。  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) - 向操作或 API 添加填充型 JSON (JSONP) 支持，以便从基于 JavaScript 浏览器的客户端执行跨域调用。  
  
##  <a name="AllowCrossDomainCalls"></a> 允许跨域调用  
 使用 `cross-domain` 策略使 API 能够通过 Adobe Flash 和基于 Microsoft Silverlight 浏览器的客户端进行访问。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>示例  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|cross-domain|根元素。 子元素必须符合 [Adobe 跨域策略文件规范](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)。|是|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** 全局  
  
##  <a name="CORS"></a> CORS  
 `cors` 策略向操作或 API 添加跨源资源共享 (CORS) 支持，以便从基于浏览器的客户端执行跨域调用。  
  
 CORS 允许浏览器与服务器交互，并确定是否允许特定的跨源请求（例如，通过某个网页上的 JavaScript 对其他域执行 XMLHttpRequests 调用）。 与只允许同源请求相比，它的灵活性更高，而且比允许所有跨源请求更安全。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>示例  
 此示例演示如何支持预检请求，例如那些具有自定义标头或 GET 和 POST 之外的方法的预检请求。 若要支持自定义标头和其他 HTTP 谓词，请使用 `allowed-methods` 和 `allowed-headers` 部分，如以下示例所示。  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|cors|根元素。|是|不适用|  
|allowed-origins|包含的 `origin` 元素说明了跨域请求的允许来源。 `allowed-origins` 可能包含单个 `origin` 元素，该元素指定允许任何源的 `*`，或者包含一个或多个内含 URI 的 `origin` 元素。|是|不适用|  
|origin|值可以是允许所有源的 `*`，或者是用于指定单个源的 URI。 URI 必须包括方案、主机和端口。|是|如果 URI 中省略了端口，则端口 80 用于 HTTP，端口 443 用于 HTTPS。|  
|allowed-methods|如果允许 GET 或 POST 之外的方法，则此元素是必需的。 包含 `method` 元素，用于指定支持的 HTTP 谓词。|否|如果此部分不存在，则支持 GET 和 POST。|  
|方法|指定 HTTP 谓词。|如果 `allowed-methods` 部分存在，则至少一个 `method` 元素是必需。|不适用|  
|allowed-headers|此元素包含 `header` 元素，用于指定可以包括在请求中的标头的名称。|否|不适用|  
|expose-headers|此元素包含 `header` 元素，用于指定可以通过客户端访问的标头的名称。|否|不适用|  
|标头的值开始缓存响应|指定标头名称。|如果节存在，则 `allowed-headers` 或 `expose-headers` 中至少一个 `header` 元素是必需。|不适用|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|allow-credentials|预检响应中的 `Access-Control-Allow-Credentials` 标头将设置为此属性的值，并且会影响客户端在跨域请求中提交凭据的功能。|否|false|  
|preflight-result-max-age|预检响应中的 `Access-Control-Max-Age` 标头将设置为此属性的值，并且会影响用户代理缓存预检响应的功能。|否|0|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 入站  
-   **策略范围：** API、操作  
  
##  <a name="JSONP"></a> JSONP  
 `jsonp` 策略向操作或 API 添加填充型 JSON (JSONP) 支持，以便从基于 JavaScript 浏览器的客户端执行跨域调用。 JSONP 是 JavaScript 程序中使用的方法，用于从不同域中的服务器请求数据。 JSONP 规避了大多数 Web 浏览器强制实施的只能在同一域中访问网页的限制。  
  
### <a name="policy-statement"></a>策略语句  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>示例  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 如果调用此方法时没有回调参数 ?cb=XXX，该方法将返回无格式 JSON（不带函数调用包装）。  
  
 如果添加回调参数 `?cb=XXX`，它将返回 JSONP 结果，并使用原始 JSON 结果包装回调函数，例如 `XYZ('<json result goes here>');`  
  
### <a name="elements"></a>元素  
  
|Name|说明|必选|  
|----------|-----------------|--------------|  
|jsonp|根元素。|是|  
  
### <a name="attributes"></a>属性  
  
|Name|说明|必选|默认|  
|----------|-----------------|--------------|-------------|  
|callback-parameter-name|以函数所在的完全限定域名为前缀的跨域 JavaScript 函数调用。|是|不适用|  
  
### <a name="usage"></a>使用情况  
 此策略可在以下策略[节](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[范围](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)中使用。  
  
-   **策略段：** 出站  
-   **策略范围：** 全局、产品、API、操作  
  
## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   