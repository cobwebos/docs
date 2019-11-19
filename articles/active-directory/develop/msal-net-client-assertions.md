---
title: 适用于 .NET 的 Microsoft 身份验证库中的客户端断言
titleSuffix: Microsoft identity platform
description: 了解针对适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 中的机密客户端应用程序的签名客户端断言支持。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ff02e4c95594f0155ab31e3c99a0eb269626d9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168122"
---
# <a name="confidential-client-assertions"></a>机密客户端断言

为了证明身份，机密客户端应用程序会与 Azure AD 交换机密。 机密可以是：
- 客户端机密（应用程序密码）。
- 证书，用于构建包含标准声明的签名断言。

此机密也可以直接是签名断言。

MSAL.NET 可以通过四种方法将凭据或断言提供给机密客户端应用：
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> 尽管可以使用 `WithClientAssertion()` API 获取机密客户端的令牌，但我们不建议在默认情况下使用它，因为它更高级，旨在处理并非常见情况的特定情况。 使用 `.WithCertificate()` API 将允许 MSAL.NET 为您处理此情况。 此 api 提供了根据需要自定义身份验证请求的功能，但 `.WithCertificate()` 创建的默认断言足以满足大多数身份验证方案。 在某些情况下，如果 MSAL.NET 无法在内部执行签名操作，也可以使用此 API 作为一种解决方法。

### <a name="signed-assertions"></a>签名断言

签名客户端断言采用签名 JWT 形式，其有效负载包含 Azure AD 强制要求的、Base64 编码的身份验证声明。 使用方式：

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD 预期的声明为：

声明类型 | 值 | 说明
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | “aud”（受众）声明标识 JWT 预期的接收者（在这里为 Azure AD）。请参阅 [RFC 7519 的 4.1.3 部分]
exp | 2019 年 6 月 27 日，周四 15:04:17 GMT+0200（罗马夏令时） | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请参阅 [RFC 7519 的 4.1.4 部分]
iss | {ClientID} | “iss”（颁发者）声明标识颁发了 JWT 的主体。 此声明的处理取决于应用程序。 “iss”值是一个区分大小写的字符串，其中包含 StringOrURI 值。 [RFC 7519 的 4.1.1 部分]
jti | （一个 GUID） | “jti”(JWT ID) 声明为 JWT 提供唯一标识符。 分配标识符值时，所用方式必须确保几乎不可能将同一值意外分配给不同的数据对象；如果应用程序使用多个颁发者，还必须防止在不同的颁发者生成的值之间发生冲突。 可以使用“jti”声明防止重播 JWT。 “jti”值是一个区分大小写的字符串。 [RFC 7519 的 4.1.7 部分]
nbf | 2019 年 6 月 27 日，周四 14:54:17 GMT+0200（罗马夏令时） | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 [RFC 7519 的 4.1.5 部分]
sub | {ClientID} | “sub”（使用者）声明标识 JWT 的使用者。 JWT 中的声明通常是有关使用者的语句。 使用者值必须本地唯一（局限于颁发者上下文）或全局唯一。 请参阅 [RFC 7519 的 4.1.2 部分]

下面是一个示例，演示如何创建这些声明：

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

下面演示如何创建签名客户端断言：

```CSharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>替代方法

你还可以选择使用[system.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/)为你创建断言。 此代码将更优雅，如以下示例中所示：

```CSharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

获得签名的客户端断言后，可以将其与 MSAL api 一起使用，如下所示。

```CSharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` 默认情况下会生成一个签名断言，其中包含 Azure AD 预期的声明，以及你想要发送的其他客户端声明。 下面是演示如何这样做的代码片段。

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果传入的目录中的某个声明与某个必需声明相同，则会考虑其他声明的值。 它会重写 MSAL.NET 计算的声明。

若要提供你自己的声明（包括 Azure AD 预期的必需声明），请针对 `false` 参数传入 `mergeWithDefaultClaims`。
