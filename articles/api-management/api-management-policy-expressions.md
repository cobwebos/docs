---
title: Azure API 管理策略表达式 | Microsoft 文档
description: 了解 Azure API 管理中的策略表达式。 请参阅示例并查看其他可用资源。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: ab83344f779f93107b59ca28348da3a66f1efc1a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076855"
---
# <a name="api-management-policy-expressions"></a>API 管理策略表达式
本文讨论 C# 7 中的策略表达式语法。 每个表达式都可以访问隐式提供的[上下文](api-management-policy-expressions.md#ContextVariables)变量以及允许的 .NET Framework 类型[子集](api-management-policy-expressions.md#CLRTypes)。

更多相关信息：

- 了解如何向后端服务提供上下文信息。 使用[设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter)和[设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader)策略来提供此信息。
- 了解如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明预先授予操作访问权限。
- 了解如何使用 [API 检查器](./api-management-howto-api-inspector.md)跟踪查看策略求值方法和这些求值的结果。
- 了解如何对[从缓存获取](api-management-caching-policies.md#GetFromCache)和[存储到缓存](api-management-caching-policies.md#StoreToCache)策略使用表达式，以便配置 API 管理响应缓存。 设置持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。
- 了解如何进行内容筛选。 删除使用[控制流](api-management-advanced-policies.md#choose)和[设置正文](api-management-transformation-policies.md#SetBody)策略从后端接收的响应中的数据元素。
- 要下载策略语句，请参阅 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 存储库。


## <a name="syntax"></a><a name="Syntax"></a> 语法
单一语句表达式括在 `@(expression)` 中，其中 `expression` 是格式正确的 C# 表达式语句。

多语句表达式括在 `@{expression}` 中。 多语句表达式中的所有代码路径必须以 `return` 语句结尾。

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> 示例

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>用法
在任何 API 管理[策略](api-management-policies.md)中，表达式都可用作属性值或文本值（除非策略引用另行指定）。

> [!IMPORTANT]
> 使用策略表达式定义策略时，只能对策略表达式进行有限的验证。 在运行时，表达式由网关执行，策略表达式生成的任何异常将导致运行时错误。

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> 策略表达式中允许的 .NET Framework 类型
下表列出了策略表达式中允许的 .NET Framework 类型及其成员。

|类型|受支持的成员|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|全部|
|Newtonsoft.Json.JsonConvert|SerializeObject、DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|全部|
|Newtonsoft.Json.Linq.JArray|全部|
|Newtonsoft.Json.Linq.JConstructor|全部|
|Newtonsoft.Json.Linq.JContainer|全部|
|Newtonsoft.Json.Linq.JObject|全部|
|Newtonsoft.Json.Linq.JProperty|全部|
|Newtonsoft.Json.Linq.JRaw|全部|
|Newtonsoft.Json.Linq.JToken|全部|
|Newtonsoft.Json.Linq.JTokenType|全部|
|Newtonsoft.Json.Linq.JValue|全部|
|System.Array|全部|
|System.BitConverter|全部|
|System.Boolean|全部|
|System.Byte|全部|
|System.Char|全部|
|System.Collections.Generic.Dictionary<TKey, TValue>|全部|
|System.Collections.Generic.HashSet\<T>|全部|
|System.Collections.Generic.ICollection\<T>|全部|
|System.Collections.Generic.IDictionary<TKey, TValue>|全部|
|System.Collections.Generic.IEnumerable\<T>|全部|
|System.Collections.Generic.IEnumerator\<T>|全部|
|System.Collections.Generic.IList\<T>|全部|
|System.Collections.Generic.IReadOnlyCollection\<T>|全部|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|全部|
|System.Collections.Generic.ISet\<T>|全部|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|全部|
|System.Collections.Generic.List\<T>|全部|
|System.Collections.Generic.Queue\<T>|全部|
|System.Collections.Generic.Stack\<T>|全部|
|System.Convert|全部|
|System.DateTime|（构造函数）、Add、AddDays、AddHours、AddMilliseconds、AddMinutes、AddMonths、AddSeconds、AddTicks、AddYears、Date、Day、DayOfWeek、DayOfYear、DaysInMonth、Hour、IsDaylightSavingTime、IsLeapYear、MaxValue、Millisecond、Minute、MinValue、Month、Now、Parse、Second、Subtract、Ticks、TimeOfDay、Today、ToString、UtcNow、Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|全部|
|System.Decimal|全部|
|System.Double|全部|
|System.Exception|全部|
|System.Guid|全部|
|System.Int16|全部|
|System.Int32|全部|
|System.Int64|全部|
|System.IO.StringReader|全部|
|System.IO.StringWriter|全部|
|System.Linq.Enumerable|全部|
|System.Math|全部|
|System.MidpointRounding|全部|
|System.Net.WebUtility|全部|
|System.Nullable|全部|
|System.Random|全部|
|System.SByte|全部|
|System.Security.Cryptography.AsymmetricAlgorithm|全部|
|System.Security.Cryptography.CipherMode|全部|
|System.Security.Cryptography.HashAlgorithm|全部|
|System.Security.Cryptography.HashAlgorithmName|全部|
|System.Security.Cryptography.HMAC|全部|
|System.Security.Cryptography.HMACMD5|全部|
|System.Security.Cryptography.HMACSHA1|全部|
|System.Security.Cryptography.HMACSHA256|全部|
|System.Security.Cryptography.HMACSHA384|全部|
|System.Security.Cryptography.HMACSHA512|全部|
|System.Security.Cryptography.KeyedHashAlgorithm|全部|
|System.Security.Cryptography.MD5|全部|
|System.Security.Cryptography.Oid|全部|
|System.Security.Cryptography.PaddingMode|全部|
|System.Security.Cryptography.RNGCryptoServiceProvider|全部|
|System.Security.Cryptography.RSA|全部|
|System.Security.Cryptography.RSAEncryptionPadding|全部|
|System.Security.Cryptography.RSASignaturePadding|全部|
|System.Security.Cryptography.SHA1|全部|
|System.Security.Cryptography.SHA1Managed|全部|
|System.Security.Cryptography.SHA256|全部|
|System.Security.Cryptography.SHA256Managed|全部|
|System.Security.Cryptography.SHA384|全部|
|System.Security.Cryptography.SHA384Managed|全部|
|System.Security.Cryptography.SHA512|全部|
|System.Security.Cryptography.SHA512Managed|全部|
|System.Security.Cryptography.SymmetricAlgorithm|全部|
|System.Security.Cryptography.X509Certificates.PublicKey|全部|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|全部|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|名称|
|System.Security.Cryptography.X509Certificates.X509Certificate|全部|
|System.Security.Cryptography.X509Certificates.X509Certificate2|全部|
|System.Security.Cryptography.X509Certificates.X509ContentType|全部|
|System.Security.Cryptography.X509Certificates.X509NameType|全部|
|System.Single|全部|
|System.String|全部|
|System.StringComparer|全部|
|System.StringComparison|全部|
|System.StringSplitOptions|全部|
|System.Text.Encoding|全部|
|System.Text.RegularExpressions.Capture|Index、Length、Value|
|System.Text.RegularExpressions.CaptureCollection|Count、Item|
|System.Text.RegularExpressions.Group|Captures、Success|
|System.Text.RegularExpressions.GroupCollection|Count、Item|
|System.Text.RegularExpressions.Match|Empty、Groups、Result|
|System.Text.RegularExpressions.Regex|（构造函数）、IsMatch、Match、Matches、Replace、Unescape、Split|
|System.Text.RegularExpressions.RegexOptions|全部|
|System.Text.StringBuilder|全部|
|System.TimeSpan|全部|
|System.TimeZone|全部|
|System.TimeZoneInfo.AdjustmentRule|全部|
|System.TimeZoneInfo.TransitionTime|全部|
|System.TimeZoneInfo|全部|
|System.Tuple|全部|
|System.UInt16|全部|
|System.UInt32|全部|
|System.UInt64|全部|
|System.Uri|全部|
|System.UriPartial|全部|
|System.Xml.Linq.Extensions|全部|
|System.Xml.Linq.XAttribute|全部|
|System.Xml.Linq.XCData|全部|
|System.Xml.Linq.XComment|全部|
|System.Xml.Linq.XContainer|全部|
|System.Xml.Linq.XDeclaration|全部|
|System.Xml.Linq.XDocument|全部，除了：加载|
|System.Xml.Linq.XDocumentType|全部|
|System.Xml.Linq.XElement|全部|
|System.Xml.Linq.XName|全部|
|System.Xml.Linq.XNamespace|全部|
|System.Xml.Linq.XNode|全部|
|System.Xml.Linq.XNodeDocumentOrderComparer|全部|
|System.Xml.Linq.XNodeEqualityComparer|全部|
|System.Xml.Linq.XObject|全部|
|System.Xml.Linq.XProcessingInstruction|全部|
|System.Xml.Linq.XText|全部|
|System.Xml.XmlNodeType|全部|

## <a name="context-variable"></a><a name="ContextVariables"></a> 上下文变量
在每个策略[表达式](api-management-policy-expressions.md#Syntax)中均可隐式使用名为 `context` 的变量。 其成员提供与 `\request` 相关的信息。 所有 `context` 成员均为只读的。

|上下文变量|允许的方法、属性和参数值|
|----------------------|-------------------------------------------------------|
|上下文|[Api](#ref-context-api)：[IApi](#ref-iapi)<br /><br /> [部署](#ref-context-deployment)<br /><br /> Elapsed：TimeSpan - 时间戳值和当前时间之间的时间间隔<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [操作](#ref-context-operation)<br /><br /> [产品](#ref-context-product)<br /><br /> [请求](#ref-context-request)<br /><br /> RequestId：Guid - 唯一请求标识符<br /><br /> [响应](#ref-context-response)<br /><br /> [订阅](#ref-context-subscription)<br /><br /> 时间戳:DateTime - 接收到请求的时间点<br /><br /> Tracing: bool - 指示跟踪是为打开还是关闭 <br /><br /> [User](#ref-context-user)<br /><br /> [变量](#ref-context-variables)：IReadOnlyDictionary<string, object><br /><br /> void Trace(message：string)|
|<a id="ref-context-api"></a>context.Api|Id：string<br /><br /> IsCurrentRevision：bool<br /><br />  Name：string<br /><br /> Path：string<br /><br /> Revision: string<br /><br /> ServiceUrl：[IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region：string<br /><br /> ServiceName：string<br /><br /> 证书：IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source：string<br /><br /> Reason：string<br /><br /> Message：string<br /><br /> Scope：string<br /><br /> Section：string<br /><br /> Path：string<br /><br /> PolicyId：string<br /><br /> 有关 context.LastError 的详细信息，请参阅[错误处理](api-management-error-handling-policies.md)。|
|<a id="ref-context-operation"></a>context.Operation|Id：string<br /><br /> Method：string<br /><br /> Name：string<br /><br /> UrlTemplate：string|
|<a id="ref-context-product"></a>context.Product|Api：IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired：bool<br /><br /> 组：IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id：string<br /><br /> Name：string<br /><br /> State：enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit：int?<br /><br /> SubscriptionRequired：bool|
|<a id="ref-context-request"></a>context.Request|正文：[IMessageBody](#ref-imessagebody) 或 `null`（如果请求没有正文）。<br /><br /> 证书：System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [标头](#ref-context-request-headers)：IReadOnlyDictionary<string, string[]><br /><br /> IpAddress：string<br /><br /> MatchedParameters：IReadOnlyDictionary<string, string><br /><br /> Method：string<br /><br /> OriginalUrl：[IUrl](#ref-iurl)<br /><br /> URL：[IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的请求标头值或 `defaultValue`。|
|<a id="ref-context-response"></a>context.Response|正文：[IMessageBody](#ref-imessagebody)<br /><br /> [标头](#ref-context-response-headers)：IReadOnlyDictionary<string, string[]><br /><br /> StatusCode：int<br /><br /> StatusReason：string|
|<a id="ref-context-response-headers"></a>string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的响应标头值或 `defaultValue`。|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime：DateTime<br /><br /> EndDate:DateTime?<br /><br /> Id：string<br /><br /> Key：string<br /><br /> Name：string<br /><br /> PrimaryKey：string<br /><br /> SecondaryKey：string<br /><br /> StartDate:DateTime?|
|<a id="ref-context-user"></a>context.User|Email：string<br /><br /> FirstName：string<br /><br /> 组：IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id：string<br /><br /> 标识：IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName：string<br /><br /> Note：string<br /><br /> RegistrationDate：DateTime|
|<a id="ref-iapi"></a>IApi|Id：string<br /><br /> Name：string<br /><br /> Path：string<br /><br /> 协议：IEnumerable<string\><br /><br /> ServiceUrl：[IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames：[ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id：string<br /><br /> Name：string|
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false):其中 T 为 string、byte[]、JObject、JToken、JArray、XNode、XElement、XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Response.Body.As<T>` 方法用于以指定的类型 `T` 读取请求和响应消息正文。 该方法默认使用原始消息正文流，并在返回后将其呈现为不可用。 要通过让该方法在正文流的副本上执行操作而避免这种情况，请将 `preserveContent` 参数设置为 `true`。 请转到[此处](api-management-transformation-policies.md#SetBody)查看示例。|
|<a id="ref-iurl"></a>IUrl|Host：string<br /><br /> Path：string<br /><br /> Port：int<br /><br /> [查询](#ref-iurl-query)：IReadOnlyDictionary<string, string[]><br /><br /> QueryString：string<br /><br /> Scheme：string|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id：string<br /><br /> Provider：string|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header：string<br /><br /> Query：string|
|<a id="ref-iurl-query"></a>string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到参数，则会返回逗号分隔的查询参数值或 `defaultValue`。|
|<a id="ref-context-variables"></a>T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue:T)|variableName：string<br /><br /> defaultValue:T<br /><br /> 如果找不到变量，则会返回强制转换为 `T` 或 `defaultValue` 类型的变量值。<br /><br /> 如果指定的类型与已返回变量的实际类型不符，此方法会引发异常。|
|BasicAuthCredentials AsBasic(input：this string)|input：string<br /><br /> 如果输入参数包含有效的 HTTP Basic Authentication 授权请求标头值，此方法会返回类型为 `BasicAuthCredentials` 的对象；否则，此方法会返回 null。|
|bool TryParseBasic(input：this string, result：out BasicAuthCredentials)|input：string<br /><br /> result：out BasicAuthCredentials<br /><br /> 如果输入参数包含请求标头中的有效 HTTP Basic Authentication 授权值，此方法会返回 `true` 且结果参数会包含类型为 `BasicAuthCredentials` 的值；否则，此方法会返回 `false`。|
|BasicAuthCredentials|Password：string<br /><br /> UserId：string|
|Jwt AsJwt(input：this string)|input：string<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回类型为 `Jwt` 的对象；否则，此方法会返回 `null`。|
|bool TryParseJwt(input：this string, result：out Jwt)|input：string<br /><br /> result：out Jwt<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回 `true` 且结果参数包含类型为 `Jwt` 的值；否则，此方法会返回 `false`。|
|Jwt|Algorithm：string<br /><br /> 受众：IEnumerable<string\><br /><br /> 声明：IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime：DateTime?<br /><br /> Id：string<br /><br /> Issuer：string<br /><br /> IssuedAt：DateTime?<br /><br /> NotBefore：DateTime?<br /><br /> Subject：string<br /><br /> Type：string|
|string Jwt.Claims.GetValueOrDefault(claimName：string, defaultValue：string)|claimName：string<br /><br /> defaultValue：string<br /><br /> 如果找不到标头，则返回逗号分隔的声明值或 `defaultValue`。|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm)|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要解密的密文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm)|input - 要解密的密文<br /><br />alg - 加密算法<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg:System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 要解密的密文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|在不检查证书吊销状态的情况下执行 X.509 链验证。<br /><br />输入 - 证书对象<br /><br />如果验证成功，则返回 `true`；如果验证失败，则返回 `false`。|


## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](./api-management-policies.md)，获取策略语句及其设置的完整列表
+ [策略示例](./policy-reference.md)