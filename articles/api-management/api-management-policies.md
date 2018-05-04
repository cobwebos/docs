---
title: Azure API 管理策略 | Microsoft Docs
description: 了解可在 Azure API 管理中使用的策略。
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7304ade9c47034b6cb76eb87126f09fb5af65c8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="api-management-policies"></a>API 管理策略
本部分提供以下 API 管理策略的参考。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](api-management-howto-policies.md)。  
  
 策略是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 流行的语句包括从 XML 到 JSON 的格式转换，并调用速率限制来限制从一名开发人员的传入调用。 许多策略开箱即用。  
  
 在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流](api-management-advanced-policies.md#choose)和[设置变量](api-management-advanced-policies.md#set-variable)策略）基于策略表达式。 有关详细信息，请参阅[高级策略](api-management-advanced-policies.md#AdvancedPolicies)和[策略表达式](api-management-policy-expressions.md)。  
  
##  <a name="ProxyPolicies"></a> 策略  
  
-   [访问限制策略](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [检查 HTTP 标头](api-management-access-restriction-policies.md#CheckHTTPHeader) - 必须存在 HTTP 标头和/或强制采用 HTTP 标头的值。  
    -   [按订阅限制调用速率](api-management-access-restriction-policies.md#LimitCallRate) - 根据订阅限制调用速率，避免 API 使用量暴增。  
    -   [按密钥限制调用率](api-management-access-restriction-policies.md#LimitCallRateByKey) - 根据密钥限制调用率以避免 API 使用量暴增。  
    -   [限制调用方 IP](api-management-access-restriction-policies.md#RestrictCallerIPs) - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。  
    -   [按订阅设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuota) - 允许根据订阅强制实施可续订或有生存期的调用量和/或带宽配额。  
    -   [按密钥设置使用量配额](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。  
    -   [验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。  
-   [高级策略](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [控制流](api-management-advanced-policies.md#choose) - 根据布尔表达式的求值，有条件地应用策略语句。  
    -   [转发请求](api-management-advanced-policies.md#ForwardRequest) - 将请求转发到后端服务。  
    -   [记录到事件中心](api-management-advanced-policies.md#log-to-eventhub) - 将指定格式的消息发送到记录器实体定义的消息目标。  
    -   [重试](api-management-advanced-policies.md#Retry) - 重试执行括住的策略语句，直到符合条件为止。 系统会根据指定的时间间隔重复，直到执行指定的重试计数为止。  
    -   [返回响应](api-management-advanced-policies.md#ReturnResponse) - 中止管道执行，将指定的响应直接返回给调用方。  
    -   [发送单向请求](api-management-advanced-policies.md#SendOneWayRequest) - 将请求发送到指定的 URL，无需等待响应。  
    -   [发送请求](api-management-advanced-policies.md#SendRequest) - 将请求发送到指定的 URL。  
    -   [设置变量](api-management-advanced-policies.md#set-variable) - 保存命名上下文变量中的值供以后访问。  
    -   [设置请求方法](api-management-advanced-policies.md#SetRequestMethod) - 允许更改请求的 HTTP 方法。  
    -   [设置状态代码](api-management-advanced-policies.md#SetStatus) - 将 HTTP 状态代码更改为指定的值。  
    -   [跟踪](api-management-advanced-policies.md#Trace) - 将字符串添加到 [API 检查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)输出中。  
    -   [等待](api-management-advanced-policies.md#Wait) - 继续之前，等待括住的[发送请求](api-management-advanced-policies.md#SendRequest)、[从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey)或[控制流](api-management-advanced-policies.md#choose)策略完成。  
-   [身份验证策略](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法对后端服务进行身份验证。  
    -   [使用客户端证书进行身份验证](api-management-authentication-policies.md#ClientCertificate) - 使用客户端证书对后端服务进行身份验证。  
-   [缓存策略](api-management-caching-policies.md#CachingPolicies)  
    -   [从缓存中获取](api-management-caching-policies.md#GetFromCache) - 执行缓存查找，并返回有效的缓存响应（如果有）。  
    -   [存储到缓存](api-management-caching-policies.md#StoreToCache) - 根据指定的缓存控制配置来缓存响应。  
    -   [从缓存中获取值](api-management-caching-policies.md#GetFromCacheByKey) - 根据密钥检索缓存的项。  
    -   [在缓存中存储值](api-management-caching-policies.md#StoreToCacheByKey) - 根据密钥在缓存中存储项。  
    -   [从缓存中删除值](api-management-caching-policies.md#RemoveCacheByKey) - 根据密钥在缓存中删除项。  
-   [跨域策略](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [允许跨域调用](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - 使 API 能够通过 Adobe Flash 和基于 Microsoft Silverlight 浏览器的客户端进行访问。  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - 向操作或 API 添加跨源资源共享 (CORS) 支持，允许从基于浏览器的客户端进行跨域调用。  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - 向操作或 API 添加填充型 JSON (JSONP) 支持，允许从基于 JavaScript 浏览器的客户端进行跨域调用。  
-   [转换策略](api-management-transformation-policies.md#TransformationPolicies)  
    -   [将 JSON 转换为 XML](api-management-transformation-policies.md#ConvertJSONtoXML) - 将请求或响应正文从 JSON 转换为 XML。  
    -   [将 XML 转换为 JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - 将请求或响应正文从 XML 转换为 JSON。  
    -   [查找并替换正文中的字符串](api-management-transformation-policies.md#Findandreplacestringinbody) - 查找请求或响应子字符串，并将其替换为不同的子字符串。  
    -   [在内容中屏蔽 URL](api-management-transformation-policies.md#MaskURLSContent) - 重写（屏蔽）响应正文中的链接，使其通过网关指向等效的链接。  
    -   [设置后端服务](api-management-transformation-policies.md#SetBackendService) - 更改传入请求的后端服务。  
    -   [设置正文](api-management-transformation-policies.md#SetBody) - 设置传入和传出请求的消息正文。  
    -   [设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader) - 向现有的响应和/或请求标头赋值，或者添加新的响应和/或请求标头。  
    -   [设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter) - 添加、删除请求查询字符串参数或替换其值。  
    -   [重写 URI](api-management-transformation-policies.md#RewriteURL) - 将请求 URL 从其公用格式转换为 Web 服务所需的格式。  
    -   [使用 XSLT 转换 XML](api-management-transformation-policies.md#XSLTransform) - 在请求或响应正文中的 XML 应用 XSL 转换。  



## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)   
