---
title: "Azure API 管理策略表达式 | Microsoft 文档"
description: "了解 Azure API 管理中的策略表达式。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: ed28fcea134bfbeda25abff41a3163471cef4294

---
# <a name="api-management-policy-expressions"></a>API 管理策略表达式
策略表达式语法为 C# 6.0 版。 每个表达式都可以访问隐式提供的[上下文](api-management-policy-expressions.md#ContextVariables)变量以及允许的 .NET Framework 类型[子集](api-management-policy-expressions.md#CLRTypes)。  
  
> [!NOTE]
>  有关策略表达式的详细信息，请观看[策略表达式](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/)视频。  
>   
>  有关使用策略表达式配置策略的演示，请观看 [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)（Cloud Cover 第 177 集：Vlad Vinogradsky 讲述更多 API 管理功能）。 该视频包含以下策略表达式演示。  
>   
>  -   10:30 - 了解如何应用 API 级策略，以便使用[设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter)和[设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader)策略将上下文信息提供给后端服务。 在 12:10 处有一个演示，演示了如何在开发人员门户中调用操作，以便在其中查看这些生效的策略。  
> -   13:50 - 了解如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明预先授予操作访问权限。 快进到 15:00，观看在策略编辑器中配置的策略，然后快进到 18:50，观看对使用和不使用所需授权令牌从开发人员门户调用操作的演示。  
> -   21:00 - 了解如何使用 [API 检查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)跟踪查看策略求值方法和求值结果。  
> -   25:25 - 了解如何针对[从缓存获取](api-management-caching-policies.md#GetFromCache)和[存储到缓存](api-management-caching-policies.md#StoreToCache)策略使用策略表达式，以便配置 API 管理响应缓存持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。  
> -   34:30 - 了解如何进行内容筛选，所用方法为删除从后端服务接收的响应中的数据元素，所用策略为[控制流](api-management-advanced-policies.md#choose)和[设置正文](api-management-transformation-policies.md#SetBody)。 若要大致了解用于此演示的 [Dark Sky Forecast API](https://developer.forecast.io/)，请从 31:50 开始观看。  
> -   若要下载此视频中使用的策略语句，请参阅 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github 存储库。  
  
  
##  <a name="a-namesyntaxa-syntax"></a><a name="Syntax"></a> 语法  
 单一语句表达式括在 `@(expression)` 中，其中 `expression` 是格式正确的 C# 表达式语句。  
  
 多语句表达式括在 `@{expression}` 中。 多语句表达式中的所有代码路径必须以 `return` 语句结尾。  
  
##  <a name="a-namepolicyexpressionsexamplesa-examples"></a><a name="PolicyExpressionsExamples"></a> 示例  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge")?3600:int.Parse((string)context.Variables["maxAge"]))  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="a-namepolicyexpressionsusagea-usage"></a><a name="PolicyExpressionsUsage"></a> 用法  
 在任何 API 管理[策略](api-management-policies.md)中，表达式可以用作属性值或文本值，除非策略引用另行指定。  
  
> [!IMPORTANT]
>  请注意，在使用策略表达式对策略进行定义时，只能对策略表达式进行有限的验证。 由于表达式是在运行时的入站或出站管道中通过网关执行的，因此只要策略表达式生成了运行时异常，就会在 API 调用中出现运行时错误。  
  
##  <a name="a-nameclrtypesa-net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> 策略表达式中允许的 .NET Framework 类型  
 下表列出了策略表达式中允许的 .NET Framework 类型及其成员。  
  
|CLR 类型|支持的方法|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|支持所有方法|  
|Newtonsoft.Json.Linq.JArray|支持所有方法|  
|Newtonsoft.Json.Linq.JConstructor|支持所有方法|  
|Newtonsoft.Json.Linq.JContainer|支持所有方法|  
|Newtonsoft.Json.Linq.JObject|支持所有方法|  
|Newtonsoft.Json.Linq.JProperty|支持所有方法|  
|Newtonsoft.Json.Linq.JRaw|支持所有方法|  
|Newtonsoft.Json.Linq.JToken|支持所有方法|  
|Newtonsoft.Json.Linq.JTokenType|支持所有方法|  
|Newtonsoft.Json.Linq.JValue|支持所有方法|  
|System.Collections.Generic.IReadOnlyCollection<T\>|全部|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|全部|  
|System.Collections.Generic.ISet<TKey, TValue>|全部|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key、Value|  
|System.Collections.Generic.List<TKey, TValue>|全部|  
|System.Collections.Generic.Queue<TKey, TValue>|全部|  
|System.Collections.Generic.Stack<TKey, TValue>|全部|  
|System.Convert|全部|  
|System.DateTime|全部|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|全部|  
|System.Decimal|全部|  
|System.Double|全部|  
|System.Guid|全部|  
|System.IEnumerable<T\>|全部|  
|System.IEnumerator<T\>|全部|  
|System.Int16|全部|  
|System.Int32|全部|  
|System.Int64|全部|  
|System.Linq.Enumerable<T\>|支持所有方法|  
|System.Math|全部|  
|System.MidpointRounding|全部|  
|System.Nullable<T\>|全部|  
|System.Random|全部|  
|System.SByte|全部|  
|System.Security.Cryptography. HMACSHA384|全部|  
|System.Security.Cryptography. HMACSHA512|全部|  
|System.Security.Cryptography.HashAlgorithm|全部|  
|System.Security.Cryptography.HMAC|全部|  
|System.Security.Cryptography.HMACMD5|全部|  
|System.Security.Cryptography.HMACSHA1|全部|  
|System.Security.Cryptography.HMACSHA256|全部|  
|System.Security.Cryptography.KeyedHashAlgorithm|全部|  
|System.Security.Cryptography.MD5|全部|  
|System.Security.Cryptography.RNGCryptoServiceProvider|全部|  
|System.Security.Cryptography.SHA1|全部|  
|System.Security.Cryptography.SHA1Managed|全部|  
|System.Security.Cryptography.SHA256|全部|  
|System.Security.Cryptography.SHA256Managed|全部|  
|System.Security.Cryptography.SHA384|全部|  
|System.Security.Cryptography.SHA384Managed|全部|  
|System.Security.Cryptography.SHA512|全部|  
|System.Security.Cryptography.SHA512Managed|全部|  
|System.Single|全部|  
|System.String|全部|  
|System.StringSplitOptions|全部|  
|System.Text.Encoding|全部|  
|System.Text.RegularExpressions.Capture|Index、Length、Value|  
|System.Text.RegularExpressions.CaptureCollection|Count、Item|  
|System.Text.RegularExpressions.Group|Captures、Success|  
|System.Text.RegularExpressions.GroupCollection|Count、Item|  
|System.Text.RegularExpressions.Match|Empty、Groups、Result|  
|System.Text.RegularExpressions.Regex|.ctor、IsMatch、Match、Matches、Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled、IgnoreCase、IgnorePatternWhitespace、Multiline、None、RightToLeft、Singleline|  
|System.TimeSpan|全部|  
|System.Tuple|全部|  
|System.UInt16|全部|  
|System.UInt32|全部|  
|System.UInt64|全部|  
|System.Uri|全部|  
|System.Xml.Linq.Extensions|支持所有方法|  
|System.Xml.Linq.XAttribute|支持所有方法|  
|System.Xml.Linq.XCData|支持所有方法|  
|System.Xml.Linq.XComment|支持所有方法|  
|System.Xml.Linq.XContainer|支持所有方法|  
|System.Xml.Linq.XDeclaration|支持所有方法|  
|System.Xml.Linq.XDocument|支持所有方法|  
|System.Xml.Linq.XDocumentType|支持所有方法|  
|System.Xml.Linq.XElement|支持所有方法|  
|System.Xml.Linq.XName|支持所有方法|  
|System.Xml.Linq.XNamespace|支持所有方法|  
|System.Xml.Linq.XNode|支持所有方法|  
|System.Xml.Linq.XNodeDocumentOrderComparer|支持所有方法|  
|System.Xml.Linq.XNodeEqualityComparer|支持所有方法|  
|System.Xml.Linq.XObject|支持所有方法|  
|System.Xml.Linq.XProcessingInstruction|支持所有方法|  
|System.Xml.Linq.XText|支持所有方法|  
|System.Xml.XmlNodeType|全部|  
  
##  <a name="a-namecontextvariablesa-context-variable"></a><a name="ContextVariables"></a> 上下文变量  
 在每个策略[表达式](api-management-policy-expressions.md#Syntax)中均可隐式使用名为 `context` 的变量。 其成员提供与 `\request` 相关的信息。 所有 `context` 成员均为只读。  
  
|上下文变量|允许的方法、属性和参数值|  
|----------------------|-------------------------------------------------------|  
|上下文|Api: IApi<br /><br /> 部署<br /><br /> LastError<br /><br /> 操作<br /><br /> 产品<br /><br /> 请求<br /><br /> 响应<br /><br /> 订阅<br /><br /> Tracing：布尔值<br /><br /> 用户<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: 字符串<br /><br /> Name: 字符串<br /><br /> Path: 字符串<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: 字符串<br /><br /> ServiceName: 字符串|  
|context.LastError|Source: 字符串<br /><br /> Reason: 字符串<br /><br /> Message: 字符串<br /><br /> Scope: 字符串<br /><br /> Section: 字符串<br /><br /> Path: 字符串<br /><br /> PolicyId: 字符串<br /><br /> 有关 context.LastError 的详细信息，请参阅[错误处理](api-management-error-handling-policies.md)。|  
|context.Operation|Id: 字符串<br /><br /> Method: 字符串<br /><br /> Name: 字符串<br /><br /> UrlTemplate: 字符串|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: 布尔值<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: 字符串<br /><br /> Name: 字符串<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: 布尔值|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: 字符串<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: 字符串<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: 字符串, defaultValue: 字符串)|headerName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的请求标头值或 `defaultValue`。|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: 整数<br /><br /> StatusReason: 字符串|  
|string context.Response.Headers.GetValueOrDefault(headerName: 字符串, defaultValue: 字符串)|headerName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的响应标头值或 `defaultValue`。|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: 字符串<br /><br /> Key: 字符串<br /><br /> Name: 字符串<br /><br /> PrimaryKey: 字符串<br /><br /> SecondaryKey: 字符串<br /><br /> StartDate: DateTime?|  
|context.User|Email: 字符串<br /><br /> FirstName: 字符串<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: 字符串<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: 字符串<br /><br /> Note: 字符串<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: 字符串<br /><br /> Name: 字符串<br /><br /> Path: 字符串<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: 字符串<br /><br /> Name: 字符串|  
|IMessageBody|As<T\>(preserveContent: 布尔值 = false): Where T: 字符串, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Request.Body.As<T>` 方法用于读取采用指定格式之一的请求或响应消息正文。 请注意，默认情况下，当用户访问消息正文时，原始的消息正文会丢失，必须将正文返回到读取正文的表达式中，以便对其进行设置。 若要保留正文内容，请在访问消息时将 `preserveContent` 参数设置为 `true`。 `preserveContent` 的默认值为 `false`。 如果 `preserveContent` 设置为 `true`，而表达式返回了正文，则会使用返回的正文。 有关详细信息，请参阅[设置正文](api-management-transformation-policies.md#SetBody)策略。|  
|IUrl|Host: 字符串<br /><br /> Path: 字符串<br /><br /> Port: 整数<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: 字符串<br /><br /> Scheme: 字符串|  
|IUserIdentity|Id: 字符串<br /><br /> Provider: 字符串|  
|ISubscriptionKeyParameterNames|Header: 字符串<br /><br /> Query: 字符串|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: 字符串, defaultValue: 字符串)|queryParameterName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到参数，则会返回逗号分隔的查询参数值或 `defaultValue`。|  
|T context.Variables.GetValueOrDefault<T\>(variableName: 字符串, defaultValue: T)|variableName: 字符串<br /><br /> defaultValue: T<br /><br /> 如果找不到变量，则会返回强制转换为 `T` 或 `defaultValue` 类型的变量值。<br /><br /> 如果指定的类型与已返回变量的实际类型不符，此方法会引发异常。|  
|BasicAuthCredentials AsBasic(input: this string)|input: 字符串<br /><br /> 如果输入参数包含有效的 HTTP Basic Authentication 授权请求标头值，此方法会返回类型为 `BasicAuthCredentials` 的对象；否则，此方法会返回 null。|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: 字符串<br /><br /> result: out BasicAuthCredentials<br /><br /> 如果输入参数包含有效的 HTTP Basic Authentication 授权请求标头值，此方法会返回 `true` 且结果参数会包含类型为 `BasicAuthCredentials` 的值；否则，此方法会返回 `false`。|  
|BasicAuthCredentials|Password: 字符串<br /><br /> UserId: 字符串|  
|Jwt AsJwt(input: this string)|input: 字符串<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回类型为 `Jwt` 的对象；否则，此方法会返回 `null`。|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: 字符串<br /><br /> result: out Jwt<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回 `true` 且结果参数包含类型为 `Jwt` 的值；否则，此方法会返回 `false`。|  
|Jwt|Algorithm: 字符串<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: 字符串<br /><br /> Issuer: 字符串<br /><br /> NotBefore: DateTime?<br /><br /> Subject: 字符串<br /><br /> Type: 字符串|  
|string Jwt.Claims.GetValueOrDefault(claimName: 字符串, defaultValue: 字符串)|claimName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的声明值或 `defaultValue`。|

## <a name="next-steps"></a>后续步骤
有关如何使用策略的详细信息，请参阅 [API 管理中的策略](api-management-howto-policies.md)。  


<!--HONumber=Jan17_HO2-->


