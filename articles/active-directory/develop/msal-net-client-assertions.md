---
title: 适用于 .NET 的 Microsoft 身份验证库中的客户端断言 |Microsoft
description: 了解适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 中的机密客户端应用程序的签名客户端断言支持。
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442508"
---
# <a name="confidential-client-assertions"></a>机密客户端断言
为了证明其身份, 机密客户端应用程序使用 Azure AD 交换密码。 机密可以是:
- 客户端密钥 (应用程序密码)。
- 证书, 用于生成包含标准声明的签名断言。

此机密还可以是直接的签名断言。

MSAL.NET 有四种方法可向机密客户端应用提供凭据或断言:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

### <a name="signed-assertions"></a>签名断言

已签名的客户端断言采用带签名的 JWT 的格式, 其中包含 Azure AD、Base64 编码的所需的身份验证声明。 使用方式：

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Azure AD 所需的声明如下:

声明类型 | ReplTest1 | 描述
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | "Aud" (受众) 声明标识 JWT 适用的收件人 (此处 Azure AD), 请参阅 [RFC 7519, Section 4.1.3]
exp | Thu 六月 27 2019 15:04:17 GMT + 0200 (罗马夏令时) | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请参阅 [RFC 7519, Section 4.1.4]
iss | ClientID | "Iss" (颁发者) 声明标识颁发 JWT 的主体。 此声明的处理是特定于应用程序的。 "Iss" 值是包含 StringOrURI 值的区分大小写的字符串。 [RFC 7519, Section 4.1.1]
jti | (Guid) | "Jti" (JWT ID) 声明为 JWT 提供唯一的标识符。 必须以确保将同一值意外分配给不同数据对象的概率的方式来分配标识符值;如果应用程序使用多个颁发者, 则必须阻止不同颁发者生成的值之间的冲突。 可以使用 "jti" 声明来防止 JWT 被重播。 "Jti" 值是区分大小写的字符串。 [RFC 7519, Section 4.1.7]
nbf | Thu 六月 27 2019 14:54:17 GMT + 0200 (罗马夏令时) | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 [RFC 7519, Section 4.1.5]
sub | ClientID | "Sub" (subject) 声明标识 JWT 的使用者。 JWT 中的声明通常是有关主题的语句。 使用者值的范围必须在颁发者的上下文中是唯一的, 或者是全局唯一的。 请参阅 [RFC 7519, Section 4.1.2]

下面是如何创建这些声明的示例:

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

下面介绍如何创建签名的客户端断言:

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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`默认情况下, 将生成一个有符号的断言, 其中包含 Azure AD 所需的声明以及要发送的附加客户端声明。 下面是有关如何执行此操作的代码片段。

```CSharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

如果传入字典中的一个声明与某个必需的声明相同, 则将考虑附加声明的值。 它将覆盖由 MSAL.NET 计算的声明。

如果要提供自己`false` `mergeWithDefaultClaims`的声明, 包括 Azure AD 所需的必需声明, 请传入以获取参数。
