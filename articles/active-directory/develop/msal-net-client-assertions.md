---
title: 客户端断言（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）中的机密客户端应用程序的签名客户端断言支持。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3d73e803a31867bedbd0ff069b8c9321257b78cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695562"
---
# <a name="confidential-client-assertions"></a>机密客户端断言

为了证明其身份，机密客户端应用程序使用 Azure AD 交换密码。 机密可以是：
- 客户端密钥（应用程序密码）。
- 证书，用于生成包含标准声明的签名断言。

此机密还可以是直接的签名断言。

MSAL.NET 有四种方法可向机密客户端应用提供凭据或断言：
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> 尽管可以使用 `WithClientAssertion()` API 获取机密客户端的令牌，但我们不建议在默认情况下使用它，因为它更高级，旨在处理并非常见情况的特定情况。 使用 `.WithCertificate()` API 将允许 MSAL.NET 为您处理此情况。 此 api 提供了根据需要自定义身份验证请求的功能，但 `.WithCertificate()` 创建的默认断言足以满足大多数身份验证方案。 在某些情况下，如果 MSAL.NET 无法在内部执行签名操作，也可以使用此 API 作为一种解决方法。

### <a name="signed-assertions"></a>签名断言

已签名的客户端断言采用带签名的 JWT 的格式，其中包含 Azure AD、Base64 编码的所需的身份验证声明。 使用方式：

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD 所需的声明如下：

声明类型 | 值 | Description
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | "Aud" （受众）声明标识 JWT 适用的收件人（此处 Azure AD），请参阅 [RFC 7519，Section 4.1.3]
exp | Thu 六月 27 2019 15:04:17 GMT + 0200 （罗马夏令时） | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请参阅 [RFC 7519，Section 4.1.4]
iss | ClientID | "Iss" （颁发者）声明标识颁发 JWT 的主体。 此声明的处理是特定于应用程序的。 "Iss" 值是包含 StringOrURI 值的区分大小写的字符串。 [RFC 7519，Section 4.1.1]
jti | （Guid） | "Jti" （JWT ID）声明为 JWT 提供唯一的标识符。 必须以确保将同一值意外分配给不同数据对象的概率的方式来分配标识符值;如果应用程序使用多个颁发者，则必须阻止不同颁发者生成的值之间的冲突。 可以使用 "jti" 声明来防止 JWT 被重播。 "Jti" 值是区分大小写的字符串。 [RFC 7519，Section 4.1.7]
nbf | Thu 六月 27 2019 14:54:17 GMT + 0200 （罗马夏令时） | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 [RFC 7519，Section 4.1.5]
sub | ClientID | "Sub" （subject）声明标识 JWT 的使用者。 JWT 中的声明通常是有关主题的语句。 使用者值的范围必须在颁发者的上下文中是唯一的，或者是全局唯一的。 请参阅 [RFC 7519，Section 4.1.2]

下面是如何创建这些声明的示例：

```csharp
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

下面介绍如何创建签名的客户端断言：

```csharp
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

```csharp
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

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

默认情况下，`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` 将生成一个包含 Azure AD 所需声明的签名断言以及要发送的其他客户端声明。 下面是有关如何执行此操作的代码片段。

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果传入字典中的一个声明与某个必需的声明相同，则将考虑附加声明的值。 它将覆盖由 MSAL.NET 计算的声明。

如果要提供自己的声明，包括 Azure AD 所需的必需声明，请传入 `mergeWithDefaultClaims` 参数 `false`。
