---
title: Azure API 管理策略表达式 | Microsoft 文档
description: 了解 Azure API 管理中的策略表达式。
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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845271"
---
# <a name="api-management-policy-expressions"></a>API 管理策略表达式
本文介绍了7中的C#策略表达式语法。 每个表达式都可以访问隐式提供的[上下文](api-management-policy-expressions.md#ContextVariables)变量以及允许的 .NET Framework 类型[子集](api-management-policy-expressions.md#CLRTypes)。

有关详细信息：

- 了解如何向后端服务提供上下文信息。 使用[设置查询字符串参数](api-management-transformation-policies.md#SetQueryStringParameter)和[设置 HTTP 标头](api-management-transformation-policies.md#SetHTTPheader)策略来提供此信息。
- 了解如何使用[验证 JWT](api-management-access-restriction-policies.md#ValidateJWT) 策略根据令牌声明预先授予操作访问权限。
- 了解如何使用 [API 检查器](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)跟踪查看策略求值方法和这些求值的结果。
- 了解如何对[从缓存获取](api-management-caching-policies.md#GetFromCache)和[存储到缓存](api-management-caching-policies.md#StoreToCache)策略使用表达式，以便配置 API 管理响应缓存。 设置持续时间，使之匹配由后端服务的 `Cache-Control` 指令指定的后端服务响应缓存。
- 了解如何进行内容筛选。 删除使用[控制流](api-management-advanced-policies.md#choose)和[设置正文](api-management-transformation-policies.md#SetBody)策略从后端接收的响应中的数据元素。
- 要下载策略语句，请参阅 [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub 存储库。


## <a name="Syntax"></a> 语法
单一语句表达式括在 `@(expression)` 中，其中 `expression` 是格式正确的 C# 表达式语句。

多语句表达式括在 `@{expression}` 中。 多语句表达式中的所有代码路径必须以 `return` 语句结尾。

## <a name="PolicyExpressionsExamples"></a> 示例

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

## <a name="PolicyExpressionsUsage"></a>用法
在任何 API 管理[策略](api-management-policies.md)中，表达式都可用作属性值或文本值（除非策略引用另行指定）。

> [!IMPORTANT]
> 使用策略表达式定义策略时，只能对策略表达式进行有限的验证。 在运行时，表达式由网关执行，策略表达式生成的任何异常将导致运行时错误。

## <a name="CLRTypes"></a> 策略表达式中允许的 .NET Framework 类型
下表列出了策略表达式中允许的 .NET Framework 类型及其成员。

|类型|受支持的成员|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|所有|
|Newtonsoft.Json.JsonConvert|SerializeObject、DeserializeObject|
|Newtonsoft.Json.Linq.Extensions|所有|
|Newtonsoft.Json.Linq.JArray|所有|
|Newtonsoft.Json.Linq.JConstructor|所有|
|Newtonsoft.Json.Linq.JContainer|所有|
|Newtonsoft.Json.Linq.JObject|所有|
|Newtonsoft.Json.Linq.JProperty|所有|
|Newtonsoft.Json.Linq.JRaw|所有|
|Newtonsoft.Json.Linq.JToken|所有|
|Newtonsoft.Json.Linq.JTokenType|所有|
|Newtonsoft.Json.Linq.JValue|所有|
|System.Array|所有|
|System.BitConverter|所有|
|System.Boolean|所有|
|System.Byte|所有|
|System.Char|所有|
|System.Collections.Generic.Dictionary<TKey, TValue>|所有|
|System.Collections.Generic.HashSet\<T>|所有|
|System.Collections.Generic.ICollection\<T>|所有|
|System.Collections.Generic.IDictionary<TKey, TValue>|所有|
|System.Collections.Generic.IEnumerable\<T>|所有|
|System.Collections.Generic.IEnumerator\<T>|所有|
|System.Collections.Generic.IList\<T>|所有|
|System.Collections.Generic.IReadOnlyCollection\<T>|所有|
|System.collections.generic.ireadonlydictionary<tkey < TKey，TValue >|所有|
|System.Collections.Generic.ISet\<T>|所有|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|所有|
|System.Collections.Generic.List\<T>|所有|
|System.Collections.Generic.Queue\<T>|所有|
|System.Collections.Generic.Stack\<T>|所有|
|System.Convert|所有|
|System.DateTime|（构造函数）、Add、AddDays、AddHours、AddMilliseconds、AddMinutes、AddMonths、AddSeconds、AddTicks、AddYears、Date、Day、DayOfWeek、DayOfYear、DaysInMonth、Hour、IsDaylightSavingTime、IsLeapYear、，分析，Second，减，刻度，TimeOfDay，今天，ToString，UtcNow，Year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|所有|
|System.Decimal|所有|
|System.Double|所有|
|System.Exception|所有|
|System.Guid|所有|
|System.Int16|所有|
|System.Int32|所有|
|System.Int64|所有|
|System.IO.StringReader|所有|
|System.IO.StringWriter|所有|
|System.Linq.Enumerable|所有|
|System.Math|所有|
|System.MidpointRounding|所有|
|System.Net.WebUtility|所有|
|System.Nullable|所有|
|System.Random|所有|
|System.SByte|所有|
|System.Security.Cryptography.AsymmetricAlgorithm|所有|
|System.Security.Cryptography.CipherMode|所有|
|System.Security.Cryptography.HashAlgorithm|所有|
|System.Security.Cryptography.HashAlgorithmName|所有|
|System.Security.Cryptography.HMAC|所有|
|System.Security.Cryptography.HMACMD5|所有|
|System.Security.Cryptography.HMACSHA1|所有|
|System.Security.Cryptography.HMACSHA256|所有|
|System.Security.Cryptography.HMACSHA384|所有|
|System.Security.Cryptography.HMACSHA512|所有|
|System.Security.Cryptography.KeyedHashAlgorithm|所有|
|System.Security.Cryptography.MD5|所有|
|System.Security.Cryptography.Oid|所有|
|System.Security.Cryptography.PaddingMode|所有|
|System.Security.Cryptography.RNGCryptoServiceProvider|所有|
|System.Security.Cryptography.RSA|所有|
|System.Security.Cryptography.RSAEncryptionPadding|所有|
|System.Security.Cryptography.RSASignaturePadding|所有|
|System.Security.Cryptography.SHA1|所有|
|System.Security.Cryptography.SHA1Managed|所有|
|System.Security.Cryptography.SHA256|所有|
|System.Security.Cryptography.SHA256Managed|所有|
|System.Security.Cryptography.SHA384|所有|
|System.Security.Cryptography.SHA384Managed|所有|
|System.Security.Cryptography.SHA512|所有|
|System.Security.Cryptography.SHA512Managed|所有|
|System.Security.Cryptography.SymmetricAlgorithm|所有|
|System.Security.Cryptography.X509Certificates.PublicKey|所有|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|所有|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|名称|
|System.Security.Cryptography.X509Certificates.X509Certificate|所有|
|System.Security.Cryptography.X509Certificates.X509Certificate2|所有|
|System.Security.Cryptography.X509Certificates.X509ContentType|所有|
|System.Security.Cryptography.X509Certificates.X509NameType|所有|
|System.Single|所有|
|System.String|所有|
|System.StringComparer|所有|
|System.StringComparison|所有|
|System.StringSplitOptions|所有|
|System.Text.Encoding|所有|
|System.Text.RegularExpressions.Capture|Index、Length、Value|
|System.Text.RegularExpressions.CaptureCollection|Count、Item|
|System.Text.RegularExpressions.Group|Captures、Success|
|System.Text.RegularExpressions.GroupCollection|Count、Item|
|System.Text.RegularExpressions.Match|Empty、Groups、Result|
|System.Text.RegularExpressions.Regex|（构造函数），IsMatch，匹配，匹配，Replace，Unescape，Split|
|System.Text.RegularExpressions.RegexOptions|所有|
|System.Text.StringBuilder|所有|
|System.TimeSpan|所有|
|System.TimeZone|所有|
|System.TimeZoneInfo.AdjustmentRule|所有|
|System.TimeZoneInfo.TransitionTime|所有|
|System.TimeZoneInfo|所有|
|System.Tuple|所有|
|System.UInt16|所有|
|System.UInt32|所有|
|System.UInt64|所有|
|System.Uri|所有|
|System.UriPartial|所有|
|System.Xml.Linq.Extensions|所有|
|System.Xml.Linq.XAttribute|所有|
|System.Xml.Linq.XCData|所有|
|System.Xml.Linq.XComment|所有|
|System.Xml.Linq.XContainer|所有|
|System.Xml.Linq.XDeclaration|所有|
|System.Xml.Linq.XDocument|全部，除外： Load|
|System.Xml.Linq.XDocumentType|所有|
|System.Xml.Linq.XElement|所有|
|System.Xml.Linq.XName|所有|
|System.Xml.Linq.XNamespace|所有|
|System.Xml.Linq.XNode|所有|
|System.Xml.Linq.XNodeDocumentOrderComparer|所有|
|System.Xml.Linq.XNodeEqualityComparer|所有|
|System.Xml.Linq.XObject|所有|
|System.Xml.Linq.XProcessingInstruction|所有|
|System.Xml.Linq.XText|所有|
|System.Xml.XmlNodeType|所有|

## <a name="ContextVariables"></a> 上下文变量
在每个策略[表达式](api-management-policy-expressions.md#Syntax)中均可隐式使用名为 `context` 的变量。 其成员提供与 `\request` 相关的信息。 所有 `context` 成员均为只读。

|上下文变量|允许的方法、属性和参数值|
|----------------------|-------------------------------------------------------|
|上下文|[Api](#ref-context-api)： [ienumerable<iapi](#ref-iapi)<br /><br /> [部署](#ref-context-deployment)<br /><br /> Elapsed: TimeSpan - 时间戳值和当前时间之间的时间间隔<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [操作](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [请求](#ref-context-request)<br /><br /> RequestId: Guid - 唯一请求标识符<br /><br /> [响应](#ref-context-response)<br /><br /> [订阅](#ref-context-subscription)<br /><br /> Timestamp: DateTime - 接收到请求的时间点<br /><br /> Tracing: bool - 指示跟踪是为打开还是关闭 <br /><br /> [用户](#ref-context-user)<br /><br /> [变量](#ref-context-variables)： system.collections.generic.ireadonlydictionary<tkey < 字符串，对象 ><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|Id: 字符串<br /><br /> IsCurrentRevision：bool<br /><br />  Name: 字符串<br /><br /> Path: 字符串<br /><br /> Revision: string<br /><br /> ServiceUrl： [IUrl](#ref-iurl)<br /><br /> Version: string |
|<a id="ref-context-deployment"></a>context.Deployment|Region: 字符串<br /><br /> ServiceName: 字符串<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>context.LastError|Source: 字符串<br /><br /> Reason: 字符串<br /><br /> Message: 字符串<br /><br /> Scope: 字符串<br /><br /> Section: 字符串<br /><br /> Path: 字符串<br /><br /> PolicyId: 字符串<br /><br /> 有关 context.LastError 的详细信息，请参阅[错误处理](api-management-error-handling-policies.md)。|
|<a id="ref-context-operation"></a>context.Operation|Id: 字符串<br /><br /> Method: 字符串<br /><br /> Name: 字符串<br /><br /> UrlTemplate: 字符串|
|<a id="ref-context-product"></a>context.Product|Api： IEnumerable <[ienumerable<iapi](#ref-iapi)\><br /><br /> ApprovalRequired: 布尔值<br /><br /> 组： IEnumerable <[ienumerable<igroup](#ref-igroup)\><br /><br /> Id: 字符串<br /><br /> Name: 字符串<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: 布尔值|
|<a id="ref-context-request"></a>context.Request|Body： [IMessageBody](#ref-imessagebody)或 `null` 如果请求没有正文。<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [标头](#ref-context-request-headers)： system.collections.generic.ireadonlydictionary<tkey < string，string [] ><br /><br /> IpAddress: 字符串<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: 字符串<br /><br /> O： [IUrl](#ref-iurl)<br /><br /> Url： [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>字符串上下文。Context.request.headers.getvalueordefault （headerName： string，defaultValue： string）|headerName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的请求标头值或 `defaultValue`。|
|<a id="ref-context-response"></a>context.Response|正文： [IMessageBody](#ref-imessagebody)<br /><br /> [标头](#ref-context-response-headers)： system.collections.generic.ireadonlydictionary<tkey < string，string [] ><br /><br /> StatusCode: 整数<br /><br /> StatusReason: 字符串|
|<a id="ref-context-response-headers"></a>字符串上下文。Context.request.headers.getvalueordefault （headerName： string，defaultValue： string）|headerName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的响应标头值或 `defaultValue`。|
|<a id="ref-context-subscription"></a>context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: 字符串<br /><br /> Key: 字符串<br /><br /> Name: 字符串<br /><br /> PrimaryKey: 字符串<br /><br /> SecondaryKey: 字符串<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|Email: 字符串<br /><br /> FirstName: 字符串<br /><br /> 组： IEnumerable <[ienumerable<igroup](#ref-igroup)\><br /><br /> Id: 字符串<br /><br /> 标识： IEnumerable <[ienumerable<iuseridentity](#ref-iuseridentity)\><br /><br /> LastName: 字符串<br /><br /> Note: 字符串<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>Ienumerable<iapi|Id: 字符串<br /><br /> Name: 字符串<br /><br /> Path: 字符串<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl： [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames： [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>Ienumerable<igroup|Id: 字符串<br /><br /> Name: 字符串|
|<a id="ref-imessagebody"></a>IMessageBody|作为 < T\>（preserveContent： bool = false）：其中 T： string、byte []、JObject、JToken、JArray、System.xml.linq.xnode>、System.xml.linq.xelement>、XDocument<br /><br /> `context.Request.Body.As<T>` 和 `context.Response.Body.As<T>` 方法用于读取请求和响应采用指定类型 `T` 的消息正文。 该方法默认使用原始消息正文流，并在返回后将其呈现为不可用。 要通过让该方法在正文流的副本上执行操作而避免这种情况，请将 `preserveContent` 参数设置为 `true`。 请转到[此处](api-management-transformation-policies.md#SetBody)查看示例。|
|<a id="ref-iurl"></a>IUrl|Host: 字符串<br /><br /> Path: 字符串<br /><br /> Port: 整数<br /><br /> [Query](#ref-iurl-query)： system.collections.generic.ireadonlydictionary<tkey < string，string [] ><br /><br /> QueryString: 字符串<br /><br /> Scheme: 字符串|
|<a id="ref-iuseridentity"></a>Ienumerable<iuseridentity|Id: 字符串<br /><br /> Provider: 字符串|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: 字符串<br /><br /> Query: 字符串|
|<a id="ref-iurl-query"></a>string IUrl. Context.request.headers.getvalueordefault （queryParameterName： string，defaultValue： string）|queryParameterName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到参数，则会返回逗号分隔的查询参数值或 `defaultValue`。|
|<a id="ref-context-variables"></a>T 上下文。Context.request.headers.getvalueordefault < T\>（variableName： string，defaultValue： T）|variableName: 字符串<br /><br /> defaultValue: T<br /><br /> 如果找不到变量，则会返回强制转换为 `T` 或 `defaultValue` 类型的变量值。<br /><br /> 如果指定的类型与已返回变量的实际类型不符，此方法会引发异常。|
|BasicAuthCredentials AsBasic(input: this string)|input: 字符串<br /><br /> 如果输入参数包含有效的 HTTP Basic Authentication 授权请求标头值，此方法会返回类型为 `BasicAuthCredentials` 的对象；否则，此方法会返回 null。|
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: 字符串<br /><br /> result: out BasicAuthCredentials<br /><br /> 如果输入参数包含请求标头中的有效 HTTP Basic Authentication 授权值，此方法会返回 `true` 且结果参数会包含类型为 `BasicAuthCredentials` 的值；否则，此方法会返回 `false`。|
|BasicAuthCredentials|Password: 字符串<br /><br /> UserId: 字符串|
|Jwt AsJwt(input: this string)|input: 字符串<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回类型为 `Jwt` 的对象；否则，此方法会返回 `null`。|
|bool TryParseJwt(input: this string, result: out Jwt)|input: 字符串<br /><br /> result: out Jwt<br /><br /> 如果输入参数包含有效的 JWT 令牌值，此方法会返回 `true` 且结果参数包含类型为 `Jwt` 的值；否则，此方法会返回 `false`。|
|Jwt|Algorithm: 字符串<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: 字符串<br /><br /> Issuer: 字符串<br /><br /> IssuedAt： DateTime？<br /><br /> NotBefore: DateTime?<br /><br /> Subject: 字符串<br /><br /> Type: 字符串|
|string Jwt.Claims.GetValueOrDefault(claimName: 字符串, defaultValue: 字符串)|claimName: 字符串<br /><br /> defaultValue: 字符串<br /><br /> 如果找不到标头，则返回逗号分隔的声明值或 `defaultValue`。|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />返回已加密的明文。|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 要加密的明文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回已加密的明文。|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - 要解密的密文<br /><br />alg - 对称加密算法的名称<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - 要解密的密文<br /><br />alg - 加密算法<br /><br />返回明文。|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - 要解密的密文<br /><br />alg - 加密算法<br /><br />key - 加密密钥<br /><br />iv - 初始化矢量<br /><br />返回明文。|
|bool VerifyNoRevocation （input： System.security.cryptography.x509certificates.x509certificate2. X509Certificate2）|执行 x.509 链验证，而不检查证书吊销状态。<br /><br />输入证书对象<br /><br />如果验证成功，则返回 `true`;如果验证失败，则 `false`。|


## <a name="next-steps"></a>后续步骤

有关如何使用策略的详细信息，请参阅：

+ [API 管理中的策略](api-management-howto-policies.md)
+ [转换 API](transform-api.md)
+ [策略参考](api-management-policy-reference.md)，获取策略语句及其设置的完整列表
+ [策略示例](policy-samples.md)
