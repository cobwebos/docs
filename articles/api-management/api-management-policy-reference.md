---
title: "Azure API 管理策略参考"
description: "了解可用于配置 API 管理的策略。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 988e0cfd8329a89ba21ab2ea292785b144e08dab


---
# <a name="azure-api-management-policy-reference"></a>Azure API 管理策略参考
本部分提供 [API 管理策略参考][API Management policy reference]中策略的索引。 有关添加和配置策略的信息，请参阅 [API 管理中的策略][Policies in API Management]。

在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流][Control flow]和[设置变量][Set variable]策略）基于策略表达式。 有关详细信息，请参阅[高级策略][Advanced policies]和[策略表达式][Policy expressions]

## <a name="policy-reference-index"></a>策略参考索引
* [访问限制策略][Access restriction policies]
  * [检查 HTTP 标头][Check HTTP header] - 强制必须存在和/或强制采用 HTTP 标头的值。
  * [按订阅限制调用率][Limit call rate by subscription] - 根据订阅限制调用率以避免 API 使用量暴增。
  * [按密钥限制调用率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - 根据密钥限制调用率以避免 API 使用量暴增。
  * [限制调用方 IP][Restrict caller IPs] - 筛选（允许/拒绝）来自特定 IP 地址和/或地址范围的调用。
  * [按订阅设置使用量配额][Set usage quota by subscription] - 允许根据订阅强制消耗可续订或有生存期的调用量和/或带宽配额。
  * [按密钥设置使用量配额](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - 允许根据密钥强制消耗可续订或有生存期的调用量和/或带宽配额。
  * [验证 JWT][Validate JWT] - 强制从指定 HTTP 标头或指定查询参数提取的 JWT 必须存在且有效。
* [高级策略][Advanced policies]
  * [控制流][Control flow] - 根据布尔值[表达式][expressions]的评估结果，有条件地应用策略语句。
  * [转发请求][Forward request] - 将请求转发到后端服务。
  * [记录到事件中心][Log to Event Hub] - 将指定格式的消息发送到[记录器](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger)实体定义的消息目标。
  * [重试](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - 重试执行括住的策略语句，直到符合条件为止。 系统将根据指定的时间间隔重复，直到执行指定的重试计数为止。
  * [返回响应](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - 中止管道执行，将指定的响应直接返回给调用方。
  * [发送单向请求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - 将请求发送到指定的 URL，无需等待响应。
  * [发送请求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - 将请求发送到指定的 URL。
  * [设置请求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - 允许更改请求的 HTTP 方法。
  * [设置状态代码](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - 将 HTTP 状态代码更改为指定的值。
  * [设置变量][Set variable] - 保存命名[上下文][context]变量中的值供以后访问。
  * [跟踪](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - 将字符串添加到 [API 检查器](api-management-howto-api-inspector.md)输出中。
  * [等待](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - 在继续下一步之前，等待括住的 Send 请求、从缓存中获取值的过程或控制流策略完成。
* [身份验证策略][Authentication policies]
  * [使用基本方法进行身份验证][Authenticate with Basic] - 使用基本身份验证方法对后端服务进行身份验证。
  * [使用客户端证书进行身份验证][Authenticate with client certificate] - 使用客户端证书对后端服务进行身份验证。
* [缓存策略][Caching policies] 
  * [从缓存中获取][Get from cache] - 执行缓存查找，并返回有效的缓存响应（如果有）。
  * [存储到缓存][Store to cache] - 根据指定的缓存控制配置来缓存响应。
  * [从缓存中获取值](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - 根据密钥检索缓存的项。
  * [在缓存中存储值](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - 根据密钥在缓存中存储项。
  * [从缓存中删除值](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - 根据密钥在缓存中删除项。
* [跨域策略][Cross domain policies] 
  * [允许跨域调用][Allow cross-domain calls] - 使 API 能够通过 Adobe Flash 和基于 Microsoft Silverlight 浏览器的客户端进行访问。
  * [CORS][CORS] - 向操作或 API 添加跨源资源共享 (CORS) 支持，以便从基于浏览器的客户端执行跨域调用。
  * [JSONP][JSONP] - 向操作或 API 添加填充型 JSON (JSONP) 支持，以便从基于 JavaScript 浏览器的客户端执行跨域调用。
* [转换策略][Transformation policies] 
  * [将 JSON 转换为 XML][Convert JSON to XML] - 将请求或响应正文从 JSON 转换为 XML。
  * [将 XML 转换为 JSON][Convert XML to JSON] - 将请求或响应正文从 XML 转换为 JSON。
  * [查找并替换正文中的字符串][Find and replace string in body] - 查找请求或响应子字符串并将其替换为不同的子字符串。
  * [在内容中屏蔽 URL][Mask URLs in content] - 重写（屏蔽）响应正文的链接，使其通过网关指向等效的链接。
  * [设置后端服务][Set backend service] - 更改传入请求的后端服务。
  * [设置正文][Set body] - 设置传入和传出请求的消息正文。
  * [设置 HTTP 标头][Set HTTP header] - 向现有的响应和/或请求标头赋值，或者添加新的响应和/或请求标头。
  * [设置查询字符串参数][Set query string parameter] - 添加、删除请求查询字符串参数或替换其值。
  * [重写 URI][Rewrite URL] - 将请求 URL 从其公用格式转换为 Web 服务所需的格式。

## <a name="next-steps"></a>后续步骤
有关策略表达式的详细信息，请观看以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx





<!--HONumber=Dec16_HO2-->


