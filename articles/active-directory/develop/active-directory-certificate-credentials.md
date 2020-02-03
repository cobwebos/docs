---
title: Microsoft 标识平台证书凭据
titleSuffix: Microsoft identity platform
description: 本文介绍了如何注册和使用证书凭据进行应用程序身份验证。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7b42676fa387914bc4825e2850b3d2f032827a79
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962112"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft 标识平台应用程序身份验证证书凭据

Microsoft 标识平台允许应用程序使用自己的凭据进行身份验证，例如，在[OAuth 2.0 客户端凭据 Grant flowv 2.0](v2-oauth2-client-creds-grant-flow.md)和代理[流](v2-oauth2-on-behalf-of-flow.md)中。

应用程序可用于身份验证的凭据的一种形式使用应用程序拥有的证书进行签名的 JSON Web 令牌 (JWT) 断言。

## <a name="assertion-format"></a>断言格式
用于计算断言的 Microsoft 标识平台，可以使用所选语言的多个[JSON Web 令牌](https://jwt.ms/)库之一。 令牌携带的信息如下所示：

### <a name="header"></a>标头

| 参数 |  备注 |
| --- | --- |
| `alg` | 应为 **RS256** |
| `typ` | 应为 **JWT** |
| `x5t` | 应为 X.509 证书 SHA-1 指纹 |

### <a name="claims-payload"></a>声明（有效负载）

| 参数 |  备注 |
| --- | --- |
| `aud` | 受众：应为 **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | 到期日期：令牌的到期日期。 该时间表示为自 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 至令牌有效期到期的秒数。|
| `iss` | 颁发者：应为 client_id（客户端服务的应用程序 ID） |
| `jti` | GUID：JWT ID |
| `nbf` | 生效时间：此日期之前不能使用令牌。 该时间表示为自 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 至令牌颁发时间的秒数。 |
| `sub` | 使用者：`iss` 应为 client_id（客户端服务的应用程序 ID） |

### <a name="signature"></a>签名

使用证书计算签名，如 [JSON Web 令牌 RFC7519 规范](https://tools.ietf.org/html/rfc7519)中所述

## <a name="example-of-a-decoded-jwt-assertion"></a>已解码的 JWT 断言示例

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

## <a name="example-of-an-encoded-jwt-assertion"></a>已编码的 JWT 断言示例

以下字符串是已编码的断言的示例。 如果仔细查看，会注意到由句点 (.) 分隔的三部分：
* 第一部分对标头进行编码
* 第二部分对有效负载进行编码
* 最后一部分是使用前两部分内容中的证书进行计算的签名

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>向 Microsoft 标识平台注册证书

可以通过使用以下任一方法，将证书凭据与 Microsoft 标识平台中的客户端应用程序相关联 Azure 门户：

### <a name="uploading-the-certificate-file"></a>上传证书文件

在客户端应用程序的 Azure 应用注册中：
1. 选择“证书和机密”。 
2. 单击 "上**传证书**"，然后选择要上传的证书文件。
3. 单击“添加”。
  上载证书后，将显示指纹、开始日期和过期值。 

### <a name="updating-the-application-manifest"></a>更新应用程序清单

拥有证书后需计算：

- `$base64Thumbprint`，此项为证书哈希的 base64 编码
- `$base64Value`，证书原始数据的 base64 编码

还需要提供 GUID 来标识应用程序清单中的密钥 (`$keyId`)。

在客户端应用程序的 Azure 应用注册中：
1. 选择 "**清单**" 打开应用程序清单。
2. 使用以下架构将 *keyCredentials* 属性替换为新的证书信息。

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
3. 将编辑保存到应用程序清单，然后将该清单上传到 Microsoft 标识平台。 

   `keyCredentials` 属性具有多个值，因此可上传多个证书实现更丰富的密钥管理。
   
## <a name="code-sample"></a>代码示例

> [!NOTE]
> 必须通过使用证书哈希将 X5T 标头转换为基本64字符串来计算该标头。 要在中C#执行此操作的代码是： `System.Convert.ToBase64String(cert.GetCertHash());`

[使用证书在守护程序应用中对 Microsoft 标识平台进行身份验证](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)的代码示例显示应用程序如何使用其自己的凭据进行身份验证。 它还演示了如何使用 `New-SelfSignedCertificate` Powershell命令[创建自签名证书](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate)。 还可以利用和使用[应用创建脚本](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md)执行创建证书、计算指纹等操作。
