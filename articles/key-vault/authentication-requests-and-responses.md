---
title: 身份验证、请求和响应
description: 对 AD 进行身份验证以使用 Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: caa2d74ecafe0b0e2508bd97eb4dc21a18e58f51
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626414"
---
# <a name="authentication-requests-and-responses"></a>身份验证、请求和响应

Azure Key Vault 支持 JSON 格式的请求和响应。 Azure Key Vault 请求会与部分 URL 参数、JSON 编码的请求和响应正文一起定向到使用 HTTPS 的有效 Azure Key Vault URL。

本主题介绍 Azure Key Vault 服务的详细信息。 有关使用 Azure REST 接口的常规信息，包括身份验证/授权和如何获取访问令牌，请参阅 [Azure REST API 参考](https://docs.microsoft.com/rest/api/azure)。

## <a name="request-url"></a>请求 URL  
 密钥管理操作使用 HTTP DELETE、GET、PATCH、PUT 和 HTTP POST，而针对现有密钥对象的加密操作则使用 HTTP POST。 不支持特定 HTTP 谓词的客户端也可能使用 HTTP POST（使用 X-HTTP-REQUEST 标头）来指定所需谓词，通常不需要正文的请求在使用 HTTP POST 时应添加一个空的正文，比如使用 POST 而不是 DELETE 时。  

 若要使用 Azure Key Vault 中的对象，请参考以下示例 URL：  

-   若要在 Key Vault 中创建名为 TESTKEY 的密钥，请使用 - `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   若要在 Key Vault 中导入名为 IMPORTEDKEY 的密钥，请使用 - `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   若要获取 Key Vault 中名为 MYSECRET 的机密，请使用 - `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   若要在 Key Vault 中使用名为 TESTKEY 的密钥签名摘要，请使用 - `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 对 Key Vault 请求的授权始终如下所示：`https://{keyvault-name}.vault.azure.net/`  

 密钥始终存储在 /keys 路径下，机密始终存储在 /secrets 路径下。  

## <a name="api-version"></a>API 版本  
 Azure Key Vault 服务支持协议版本控制，可与下层客户端兼容，但并非所有功能都可供这些客户端使用。 客户端必须使用 `api-version` 查询字符串参数，指定无默认协议时支持的协议版本。  

 Azure Key Vault 协议版本遵循使用 {YYYY}.{MM}.{DD} 格式的日期编号方案。  

## <a name="request-body"></a>请求正文  
 根据 HTTP 规范，GET 操作不得包含请求正文，而 POST 和 PUT 操作必须包含请求正文。 DELETE 操作中的正文在 HTTP 中是可选的。  

 除非操作说明中另有说明，否则请求正文内容类型必须为 application/json，且必须包含与内容类型一致的序列化 JSON 对象。  

 除非操作说明中另有说明，否则接受请求标头必须包含 application/json 媒体类型。  

## <a name="response-body"></a>响应正文  
 除非操作说明中另有说明，否则成功和失败操作的响应正文内容类型需为 application/json 并包含详细的错误信息。  

## <a name="using-http-post"></a>使用 HTTP POST  
 部分客户端可能无法使用某些 HTTP 谓词，例如 PATCH 或 DELETE。 Azure Key Vault 支持使用 HTTP POST 作为这些客户端的替代方法，前提是客户端也包括“X-HTTP-METHOD”标头以指定原始 HTTP 谓词。 本文档中定义的每个 API 都记录了对 HTTP POST 的支持。  

## <a name="error-responses"></a>错误响应  
 错误处理将使用 HTTP 状态代码。 得到的结果通常为：  

-   2xx - 成功：用于常规操作。 响应正文包含预期的结果  

-   3xx - 重定向：可能返回 304“未修改”以满足条件性 GET。 未来可能会使用其他 3xx 代码，以指示 DNS 和路径更改。  

-   4xx - 客户端错误：用于错误请求、缺少密钥、语法错误、参数无效、身份验证错误等。响应正文包含详细的错误说明。  

-   5xx - 服务器错误：用于内部服务器错误。 响应正文包含汇总的错误信息。  

 该系统用于代理或防火墙后方。 因此，客户端可能会收到其他错误代码。  

 出现问题时，Azure Key Vault 也会在响应正文中返回错误信息。 响应正文为 JSON 格式，且采用以下形式：  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>身份验证  
 必须对所有 Azure Key Vault 请求进行身份验证。 Azure Key Vault 支持通过 OAuth2 [[RFC6749](http://tools.ietf.org/html/rfc6749)] 获得的 Azure Active Directory 访问令牌。 
 
 若要详细了解如何注册应用程序和进行身份验证以使用 Azure Key Vault，请参阅[通过 Azure AD 注册客户端应用程序](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad)。
 
 必须使用 HTTP 授权标头向服务发送访问令牌：  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 如果未提供访问令牌或服务未接受访问令牌，则会向客户端返回 HTTP 401 错误，其中包含WWW-Authenticate 标头，例如：  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 WWW-Authenticate 标头的参数为：  

-   authorization：可用于获取请求访问令牌的 OAuth2 授权服务的地址。  

-   resource：要在授权请求中使用的资源名称。  

## <a name="see-also"></a>另请参阅  
 [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
