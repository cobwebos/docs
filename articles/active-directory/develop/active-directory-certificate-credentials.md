---
title: Microsoft 标识平台证书凭据
titleSuffix: Microsoft identity platform
description: 本文讨论注册和使用证书凭据进行应用程序身份验证。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853378"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft 标识平台应用程序身份验证证书凭据

Microsoft 标识平台允许应用程序使用自己的凭据进行身份验证，例如，在 OAuth 2.0  [客户端凭据授予](v2-oauth2-client-creds-grant-flow.md) 流和 [代表](v2-oauth2-on-behalf-of-flow.md) (OBO) 流中。

应用程序可用于身份验证的一种形式的凭据是 (JWT) 断言，该 [令牌](./security-tokens.md#json-web-tokens-jwts-and-claims) 是使用应用程序拥有的证书进行签名的。

## <a name="assertion-format"></a>断言格式

若要计算断言，可以使用所选语言的多种 JWT 库之一。 此信息由标记在其 [标头](#header)、 [声明](#claims-payload)和 [签名](#signature)中传送。

### <a name="header"></a>标头

| 参数 |  备注 |
| --- | --- |
| `alg` | 应为 **RS256** |
| `typ` | 应为 **JWT** |
| `x5t` | X.509 证书哈希的 (也称为证书的 SHA-1 *指纹*) 十六进制表示形式编码为 Base64 字符串值。 例如，给定 (Hex) 的 x.509 证书哈希 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` ，则 `x5t` 声明将 `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64) 。 |

### <a name="claims-payload"></a>声明（有效负载）

| 参数 |  备注 |
| --- | --- |
| `aud` | 受众：应为 `https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | 到期日期：令牌的到期日期。 该时间表示为自 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 至令牌有效期到期的秒数。 建议使用较短的过期时间-10 分钟到一小时。|
| `iss` | 颁发者：应为客户端服务的 client_id (*应用程序 (客户端) id*)  |
| `jti` | GUID： JWT ID |
| `nbf` | 不早于：在其之前不能使用令牌的日期。 该时间表示为从1970年1月1日 (1970-01-01T0：0： 0Z) UTC 到创建断言之前的秒数。 |
| `sub` | 主题：对于 `iss` ，应为客户端服务的 client_id (*应用程序 (客户端) id*)  |

### <a name="signature"></a>签名

签名是通过应用证书来计算的，如 [JSON Web 令牌 RFC7519 规范](https://tools.ietf.org/html/rfc7519)中所述。

## <a name="example-of-a-decoded-jwt-assertion"></a>已解码的 JWT 断言示例

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>已编码的 JWT 断言示例

以下字符串是已编码的断言的示例。 如果你仔细查看，你会注意到三个以点分隔的部分 (`.`) ：

* 第一部分编码 *标头*
* 第二部分将 *声明* (负载编码) 
* 最后一节是通过前两部分内容中的证书进行计算的*签名*

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>向 Microsoft 标识平台注册证书

可以使用以下任意方法通过 Azure 门户将证书凭据与 Microsoft 标识平台中的客户端应用程序相关联：

### <a name="uploading-the-certificate-file"></a>上传证书文件

在客户端应用程序的 Azure 应用注册中：
1. 选择“证书和机密”。
2. 单击“上传证书”，然后选择要上传的证书文件。
3. 单击“添加”。
  上传证书后，将显示指纹、开始日期和到期日期值。

### <a name="updating-the-application-manifest"></a>更新应用程序清单

拥有证书后需计算：

- `$base64Thumbprint` -证书哈希的 Base64 编码值
- `$base64Value` -证书原始数据的 Base64 编码值

还需要提供 GUID 来标识应用程序清单中的密钥 (`$keyId`)。

在客户端应用程序的 Azure 应用注册中：
1. 选择“清单”以打开应用程序清单。
2. 使用以下架构将 *keyCredentials* 属性替换为新的证书信息。

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 将所做的编辑保存到应用程序清单，然后将清单上传到 Microsoft 标识平台。

   `keyCredentials` 属性具有多个值，因此可上传多个证书实现更丰富的密钥管理。

## <a name="next-steps"></a>后续步骤

GitHub 上的 [.Net Core 守护程序控制台应用程序使用 Microsoft 标识平台](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 代码示例显示应用程序如何使用其自己的凭据进行身份验证。 它还演示了如何使用 PowerShell cmdlet [创建自签名证书](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) `New-SelfSignedCertificate` 。 你还可以使用示例存储库中的 [应用创建脚本](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) 来创建证书、计算指纹等。
