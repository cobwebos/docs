---
title: Azure API for FHIR 访问令牌验证
description: 逐步介绍令牌验证，并提供有关如何排查访问问题的提示
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844654"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR 访问令牌验证

Azure API for FHIR 验证访问令牌的方式取决于实施方案和配置。 本文将逐步说明验证步骤，这些步骤可能有助于排查访问问题。

## <a name="validate-token-has-no-issues-with-identity-provider"></a>验证令牌是否存在标识提供者方面的问题

令牌验证的第一步是验证该令牌是否由正确的标识提供者颁发并且尚未修改。 FHIR 服务器将配置为使用称作颁发机构 `Authority` 的特定标识提供者。 FHIR 服务器将从 `/.well-known/openid-configuration` 终结点检索有关标识提供者的信息。 使用 Azure AD 时，完整的 URL 是：

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

其中，`<TENANT-ID>` 是特定的 Azure AD 租户（租户 ID 或域名）。

Azure AD 将向 FHIR 服务器返回如下所示的文档。

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
FHIR 服务器的重要属性为 `jwks_uri`（告知服务器要从何处提取用于验证令牌签名的加密密钥）和 `issuer`（告诉服务器要在此服务器颁发的令牌的颁发者声明 (`iss`) 中包含哪些内容）。 FHIR 服务器可以使用此文档来验证它收到的令牌是否真实可信。

## <a name="validate-claims-of-the-token"></a>验证令牌的声明

服务器验证令牌的真实性后，FHIR 服务器将继续验证客户端是否具有访问令牌所需的声明。

使用 Azure API for FHIR 时，服务器将会验证：

1. 令牌是否包含适当的 `Audience`（`aud` 声明）。
1. 允许为其颁发令牌的用户或主体可访问 FHIR 服务器数据平面。 `oid`标记的声明包含唯一标识用户或主体的标识对象 ID。

建议将 FHIR 服务 [配置为使用 AZURE RBAC](configure-azure-rbac.md) 来管理数据面角色分配。 但如果你的 FHIR 服务使用外部或辅助 Azure Active Directory 租户，则还可以 [配置本地 RBAC](configure-local-rbac.md) 。 

使用适用于 Azure 的 OSS Microsoft FHIR server 时，服务器将验证：

1. 令牌具有正确的 `Audience` (`aud` 声明) 。
1. 令牌在声明中具有一个角色 `roles` ，该角色允许访问 FHIR 服务器。

有关如何在 [FHIR 服务器上定义角色](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)的详细信息，请参阅。

FHIR 服务器还可以验证访问令牌是否具有范围 (in 令牌声明 `scp`) ，以访问客户端尝试访问的 FHIR API 部分。 目前，适用于 FHIR 的 Azure API 和适用于 Azure 的 FHIR 服务器不会验证令牌作用域。

## <a name="next-steps"></a>后续步骤
现在，你已了解如何进行令牌验证，接下来可以完成创建 JavaScript 应用程序并读取 FHIR 数据的教程。

>[!div class="nextstepaction"]
>[Web 应用程序教程](tutorial-web-app-fhir-server.md)