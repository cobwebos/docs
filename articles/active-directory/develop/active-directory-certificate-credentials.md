---
title: "Azure AD 中的证书凭据 |Microsoft 文档"
description: "本文讨论注册和使用证书凭据进行应用程序身份验证"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: 
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 9267a7d2b27930fbcd0aa70ec9e2ddc62b6b0b2a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="certificate-credentials-for-application-authentication"></a>应用程序身份验证的证书凭据

Azure Active Directory 允许应用程序使用自己的凭据进行身份验证，例如，在 OAuth 2.0 客户端凭据授予流程和 On-Behalf-Of 流中就是如此。
可以使用的凭据的一种形式是使用应用程序拥有的证书进行签名的 JSON Web 令牌 (JWT) 断言。

## <a name="format-of-the-assertion"></a>断言的格式
若要计算断言，需使用所选语言中的其中一个 [ Web 令牌](https://jwt.io/)库。 令牌附带的信息包括：

#### <a name="header"></a>标头

| 参数 |  备注 |
| --- | --- | --- |
| `alg` | 应为 **RS256** |
| `typ` | 应为 **JWT** |
| `x5t` | 应为 X.509 证书 SHA-1 指纹 |

#### <a name="claims-payload"></a>声明（有效负载）

| 参数 |  备注 |
| --- | --- | --- |
| `aud` | 受众：应为 **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | 到期日期：令牌的到期日期。 该时间表示为自 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 至令牌有效期到期的秒数。|
| `iss` | 颁发者：应为 client_id（客户端服务的应用程序 ID） |
| `jti` | GUID：JWT ID |
| `nbf` | 生效时间：此日期之前不能使用令牌。 该时间表示为自 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 至令牌颁发时间的秒数。 |
| `sub` | 使用者：`iss` 应为 client_id（客户端服务的应用程序 ID） |

#### <a name="signature"></a>签名
使用证书计算签名，如 [JSON Web 令牌 RFC7519 规范](https://tools.ietf.org/html/rfc7519)中所述

### <a name="example-of-a-decoded-jwt-assertion"></a>已解码的 JWT 断言示例
```
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

### <a name="example-of-an-encoded-jwt-assertion"></a>已编码的 JWT 断言示例
以下字符串是已编码的断言的示例。 仔细查看会发现由句点 (.) 分隔的三部分。
第一部分编码标头，第二部分编码有效负载，最后一部分是使用前两部分内容中的证书进行计算的签名。
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>使用 Azure AD 注册证书
要将 Azure AD 中的证书凭据与客户端应用程序相关联，则需要编辑应用程序清单。
拥有证书后需计算：
- `$base64Thumbprint`，证书哈希的 base64 编码
- `$base64Value`，证书原始数据的 base64 编码

还需要提供 GUID 来标识应用程序清单中的密钥 (`$keyId`)

在 Azure 应用注册客户端应用程序过程中，打开应用程序清单，并使用以下架构将 keyCredentials 属性替换为新的证书信息：
```
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

将所做的编辑保存到应用程序清单中并上传到 Azure AD。 keyCredentials 属性具有多个值，因此可上传多个证书实现更丰富的密钥管理。
