---
title: Azure API for FHIR 的 Azure Active Directory 标识配置
description: 了解 Azure FHIR 服务器的标识、身份验证和授权原理。
services: healthcare-apis
author: caitlinv39
ms.reviewer: mihansen
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 53adf974a3af4a2cc3e5c89156fe4b50571c7b79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870907"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Azure API for FHIR 的 Azure Active Directory 标识配置

处理医疗保健数据时，一个重要目标是确保数据安全且数据无法由未经授权的用户或应用程序访问。 FHIR 服务器使用 [OAuth 2.0](https://oauth.net/2/) 来确保这些数据的安全。 [Azure API for FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) 通过 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)（OAuth 2.0 标识提供者的一个示例）受到保护。 本文概要地介绍了 FHIR 服务器授权，以及获取用于访问 FHIR 服务器的令牌所需执行的步骤。 这些步骤适用于任何 FHIR 服务器和任何标识提供者，不过，本文将会针对用作 FHIR 服务器的 Azure API for FHIR，以及用作标识提供者的 Azure AD 讲解这些步骤。

## <a name="access-control-overview"></a>访问控制概述

要使客户端应用程序能够访问 Azure API for FHIR，该应用程序必须提供一个访问令牌。 访问令牌是已签名的 [Base64](https://en.wikipedia.org/wiki/Base64) 编码属性（声明）集合，其中携带了有关客户端标识以及授予客户端的角色和特权的信息。

可通过多种方式获取令牌，但 Azure API for FHIR 并不关心令牌的获取方式，只要该令牌是使用正确声明以适当方式签名的令牌即可。 

使用[授权代码流](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)作为示例，访问 FHIR 服务器需要完成以下四个步骤：

![FHIR 授权](media/azure-ad-hcapi/fhir-authorization.png)

1. 客户端向 Azure AD 的 `/authorize` 终结点发送请求。 Azure AD 将客户端重定向到登录页，在此页中，用户将使用适当的凭据进行身份验证（例如，进行用户名和密码身份验证或双重身份验证）。 请参阅有关[获取授权代码](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#request-an-authorization-code)的详细信息。 成功完成身份验证后，会将一个授权代码返回给客户端。  Azure AD 只允许将此授权代码返回给客户端应用程序注册中配置的已注册回复 URL（请参阅下文）。
1. 客户端应用程序在 Azure AD 的 `/token` 终结点上，使用该授权代码来交换访问令牌。 请求令牌时，客户端应用程序可能需要提供客户端机密（应用程序密码）。 请参阅有关[获取访问令牌](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code#use-the-authorization-code-to-request-an-access-token)的详细信息。
1. 客户端向 Azure API for FHIR 发出请求，例如，发出 `GET /Patient` 来搜索所有患者。 发出请求时，客户端会在 HTTP 请求标头中包含该访问令牌，例如 `Authorization: Bearer eyJ0e...`，其中 `eyJ0e...` 表示 Base64 编码的访问令牌。
1. Azure API for FHIR 验证该令牌是否包含适当的声明（令牌中的属性）。 如果所有内容符合要求，则 Azure API for FHIR 将完成请求，并将包含结果的 FHIR 捆绑包返回给客户端。

必须注意，Azure API for FHIR 不涉及验证用户凭据，并且不颁发令牌。 身份验证和令牌创建由 Azure AD 来完成。 Azure API for FHIR 只是验证令牌是否已正确签名（令牌真实可信）并包含适当的声明。

## <a name="structure-of-an-access-token"></a>访问令牌的结构

FHIR 应用程序的开发通常涉及到调试访问问题。 如果客户端在访问 Azure API for FHIR 时遭到拒绝，则最好是了解访问令牌的结构及其解码方式，以便能够检查该令牌的内容（声明）。 

FHIR 服务器通常需要 [JSON Web 令牌](https://en.wikipedia.org/wiki/JSON_Web_Token)（JWT，有时念作“jot”）。 该令牌由三个部分组成：

1. 一个如下所示的标头：
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. 有效负载（声明），例如：
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. 一个签名，它通过以下方式计算出来：将标头的 Base64 编码内容和有效负载串联到一起，并基于标头中指定的算法 (`alg`) 计算这些串联内容的加密哈希。 服务器可从标识提供者获取公钥，然后验证此令牌是否由特定的标识提供者颁发且未遭篡改。

完整的令牌包含这三个段的 Base64 编码（实际上是 Base64 URL 编码）版本。 这三个段串联在一起并以 `.`（句点）分隔。

下面是一个示例令牌：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

可以使用 [https://jwt.ms](https://jwt.ms) 等工具对该令牌进行解码和检查。 令牌解码的结果是：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>获取访问令牌

如前所述，可通过多种方法从 Azure AD 获取令牌。 [Azure AD 开发人员文档](https://docs.microsoft.com/azure/active-directory/develop/)中详细介绍了这些方法。

Azure AD 使用 OAuth 2.0 终结点的两个不同版本，称为 `v1.0` 和 `v2.0`。 这两个版本都是 OAuth 2.0 终结点，名称 `v1.0` 和 `v2.0` 只是表示 Azure AD 实现该标准的方式的差异。 

使用 FHIR 服务器时，可以使用 `v1.0` 或 `v2.0` 终结点。 可以根据在客户端应用程序中使用的身份验证库来选择版本。

Azure AD 文档的相关部分如下：

* `v1.0` 终结点：
    * [授权代码流](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)。
    * [客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)。
* `v2.0` 终结点：
    * [授权代码流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)。
    * [客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)。

还可以通过其他方法（例如代理流）获取令牌。 有关详细信息，请查看 Azure AD 文档。 使用 Azure API for FHIR 时，还可以[使用 Azure CLI](get-healthcare-apis-access-token-cli.md) 通过一些捷径获取访问令牌（用于调试目的）。

## <a name="next-steps"></a>后续步骤

本文档介绍了在使用 Azure AD 保护对 Azure API for FHIR 的访问时涉及的一些基本概念。 若要了解如何部署 Azure API for FHIR 的实例，请继续阅读部署快速入门。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)