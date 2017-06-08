---
title: "使用 OAuth2.0 进行 Azure AD 服务到服务身份验证 | Microsoft 文档"
description: "本文介绍如何使用 OAuth2.0 客户端凭据授权流通过 HTTP 消息实现服务到服务身份验证。"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: priyamo
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 9f8513c64cf605ab339dc3dfb8129a7fad123918
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>使用客户端凭据（共享密钥或证书）进行服务到服务调用
OAuth 2.0 客户端凭据授权流允许 Web 服务（机密客户端）在调用其他 Web 服务时使用它自己的凭据（而不是模拟用户）进行身份验证。 在这种情况下，客户端通常是中间层 Web 服务、后台程序服务或网站。 为了更高级别的保证，Azure AD 还允许调用服务以将证书（而不是共享密钥）用作凭据。

## <a name="client-credentials-grant-flow-diagram"></a>客户端凭据授权流关系图
下图说明了客户端凭据授权流在 Azure Active Directory (Azure AD) 中的工作原理。

![OAuth2.0 客户端凭据授权流](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 客户端应用程序向 Azure AD 令牌颁发终结点进行身份验证，并请求访问令牌。
2. Azure AD 令牌颁发终结点颁发访问令牌。
3. 使用访问令牌向受保护资源进行身份验证。
4. 受保护资源中的数据返回到 Web 应用程序。

## <a name="register-the-services-in-azure-ad"></a>在 Azure AD 中注册服务
在 Azure Active Directory (Azure AD) 中注册调用服务和接收服务。 有关详细说明，请参阅 [Integrating applications with Azure Active Directory](active-directory-integrating-applications.md)（将应用程序与 Azure Active Directory 集成）。

## <a name="request-an-access-token"></a>请求访问令牌
若要请求访问令牌，对特定于租户的 Azure AD 终结点使用 HTTP POST。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>服务到服务访问令牌请求
有两种情况，具体取决于客户端应用程序选择由共享密钥还是由证书保护。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享密钥访问令牌请求
使用共享密钥时，服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| grant_type |必填 |指定请求的授权类型。 在客户端凭据授权流中，该值必须是 **client_credentials**。 |
| client_id |必填 |指定调用 Web 服务的 Azure AD 客户端 ID。 若要查找调用应用程序的客户端 ID，请在 [Azure 门户](https://portal.azure.com)中，单击“Active Directory”，切换目录，然后单击应用程序。 client_id 是应用程序 ID |
| client_secret |必填 |在 Azure AD 中输入为调用 Web 服务或 daemon 应用程序注册的密钥。 若要创建密钥，请在 Azure 门户中，单击“Active Directory”，切换目录，然后依次单击应用程序、“设置”、“密钥”并添加密钥。|
| resource |必填 |输入接收 Web 服务的应用 ID URI。 若要查找应用 ID URI，请在 Azure 门户中，单击“Active Directory”，切换目录，然后依次单击服务应用程序、“设置”和“属性” |

#### <a name="example"></a>示例
以下 HTTP POST 请求 https://service.contoso.com/ Web 服务的访问令牌。 `client_id` 标识请求访问令牌的 Web 服务。

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求
使用证书的服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| grant_type |必填 |指定请求的响应类型。 在客户端凭据授权流中，该值必须是 **client_credentials**。 |
| client_id |必填 |指定调用 Web 服务的 Azure AD 客户端 ID。 若要查找调用应用程序的客户端 ID，请在 [Azure 门户](https://portal.azure.com)中，单击“Active Directory”，切换目录，然后单击应用程序。 client_id 是应用程序 ID |
| client_assertion_type |必填 |值必须是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |必填 | 断言（Jason Web 令牌），需使用作为凭据向应用程序注册的证书进行创建和签名。 有关如何注册证书以及断言的格式，请参阅以下详细信息。|
| resource | 必填 |输入接收 Web 服务的应用 ID URI。 若要查找应用 ID URI，请在 Azure 门户中，依次单击“Active Directory”、该目录、该应用程序和“配置”。 |

参数几乎与共享密钥请求的参数相同，只不过 client_secret 参数替换为两个参数：client_assertion_type 和 client_assertion。

#### <a name="format-of-the-assertion"></a>断言的格式
若要计算断言，需使用选择的语言中的其中一个 [Jason Web 令牌](https://jwt.io/)库。 令牌附带的信息包括：

##### <a name="header"></a>标头

| 参数 |  备注 |
| --- | --- | --- |
| `alg` | 应为 **RS256** |
| `typ` | 应为 **JWT** |
| `x5t` | 应为 X.509 证书 SHA-1 指纹 |

##### <a name="claims-payload"></a>声明（有效负载）

| 参数 |  备注 |
| --- | --- | --- |
| `aud` | 受众：应为 **https://login.microsoftonline/*tenant_Id*/oauth2/token** |
| `exp` | 到期日期 |
| `iss` | 颁发者：应为 client_id（客户端服务的应用程序 ID） |
| `jti` | GUID：JWT ID |
| `nbf` | 生效时间：此日期之前不能使用令牌 |
| `sub` | 使用者：`iss` 应为 client_id（客户端服务的应用程序 ID） |

##### <a name="signature"></a>签名
使用证书计算签名，如 [JSON Web 令牌 RFC7519 规范](https://tools.ietf.org/html/rfc7519)中所述

##### <a name="example-of-a-decoded-jwt-assertion"></a>已解码的 JWT 断言示例
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

##### <a name="example-of-encoded-jwt-assertion"></a>已编码的 JWT 断言示例
以下字符串是已编码的断言的示例。 仔细查看会发现由句点 (.) 分隔的三部分。 第一部分编码标头，第二部分编码有效负载，最后一部分是使用前两部分内容中的证书进行计算的签名。
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

#### <a name="how-to-register-your-certificate-with-azure-ad"></a>如何向 Azure AD 注册证书？
若要将 Azure AD 中的证书凭据与客户端应用程序相关联，则需要编辑应用程序清单。 拥有证书后需计算：
- `$base64Thumbprint`，证书哈希的 base64 编码
- `$base64Value`，证书原始数据的 base64 编码

还需要提供 GUID 来标识应用程序清单中的密钥 (`$keyId`)

在客户端应用程序的 Azure 门户应用注册中，依次单击“清单”和“下载”。
如果使用文本编辑器，请打开清单并使用以下架构将 *keyCredentials* 属性替换为新的证书信息：
```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
],
```
将所做编辑保存到应用程序清单，并依次单击“清单”和“上传”将其上传到 Azure AD。 keyCredentials 属性具有多个值，因此可上传多个证书实现更丰富的密钥管理。


#### <a name="example-of-request"></a>请求示例
以下 HTTP POST 使用证书请求 https://service.contoso.com/ Web 服务的访问令牌。 `client_id` 标识请求访问令牌的 Web 服务。

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>服务到服务访问令牌响应

成功响应包含具有以下参数的 JSON OAuth 2.0 响应：

| 参数 | 说明 |
| --- | --- |
| access_token |请求的访问令牌。 调用 Web 服务可以使用此令牌向接收 Web 服务进行身份验证。 |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 **Bearer**。 有关持有者令牌的详细信息，请参阅 [OAuth2.0 授权框架：持有者令牌用法 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| expires_in |访问令牌的有效期（以秒为单位）。 |
| expires_on |访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。 |
| not_before |访问令牌可用的时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至令牌有效时间的秒数。|
| resource |接收 Web 服务的应用 ID URI。 |

#### <a name="example-of-response"></a>响应示例
下面的示例演示对 Web 服务的访问令牌请求的成功响应。

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>另请参阅
* [Azure AD 中的 OAuth 2.0](active-directory-protocols-oauth-code.md)
* [使用共享密钥的服务到服务调用的 C# 示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon)和[使用证书的服务到服务调用的 C# 示例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

