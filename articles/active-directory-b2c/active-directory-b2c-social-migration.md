---
title: Azure Active Directory B2C：迁移具有社交标识的用户
description: 介绍使用图形 API 将具有社交标识的用户迁移到 Azure AD B2C 的核心概念
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: 76ed4dac40872bf6db07b26c5805a4db62dc9dfc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C：迁移具有社交标识的用户
计划将标识提供者迁移到 Azure AD B2C 时，可能还需要迁移具有社交标识的用户。 本文介绍如何将现有的社交标识帐户（例如 Facebook、LinkedIn、Microsoft 和 Google 帐户）迁移到 Azure AD B2C。 本文也适用于联合标识，但这种迁移不太常见。

## <a name="prerequisites"></a>先决条件
本文是“用户迁移”一文的延续，重点介绍社交标识迁移。 在开始之前，请阅读[用户迁移](active-directory-b2c-user-migration.md)。

## <a name="social-identities-migration-introduction"></a>社交标识迁移简介

* 在 Azure AD B2C 中，**本地帐户**登录名（用户名或电子邮件地址）存储在用户记录中的 `signInNames` 集合内。 `signInNames` 包含一个或多个指定用户登录名的 `signInName` 记录。 每个登录名必须在整个租户中保持唯一。

* **社交帐户**的标识存储在 `userIdentities` 集合中。 该条目指定 `issuer`（标识提供者名称，例如 facebook.com 和 `issuerUserId`），是颁发者的唯一用户标识符。 `userIdentities` 属性包含一个或多个 UserIdentity 记录，这些记录指定社交标识提供者中的社交帐户类型和唯一用户标识符。

* **将本地帐户与社交标识相结合**。 如前所述，本地帐户登录名和社交帐户标识存储在不同的属性中。 `signInNames` 用于本地帐户，`userIdentities` 用于社交帐户。 单个 Azure AD B2C 帐户可以只是本地帐户、只是社交帐户，也可以在一条用户记录中结合本地帐户和社交标识。 此行为允许你管理单个帐户，而用户可以使用本地帐户凭据或社交标识登录。

* `UserIdentity` 类型 - 包含 Azure AD B2C 租户中社交帐户用户的标识信息：
    * `issuer` - 颁发用户标识符的标识提供者的字符串表示形式，例如 facebook.com。
    * `issuerUserId` - 社交标识提供者使用的唯一用户标识符，采用 base64 格式。

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* 根据所用的标识提供者，**社交用户 ID** 是给定用户的 `per application` 唯一值，或者是开发帐户。 使用社交网络提供商以前分配的相同应用程序 ID 配置 Azure AD B2C 策略。 或使用另一应用程序 `within the same development account`。

## <a name="use-graph-api-to-migrate-users"></a>使用图形 API 迁移用户
通过[图形 API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 创建 Azure AD B2C 用户帐户。 若要与图形 API 通信，首先必须创建具有管理特权的服务帐户。 在 Azure AD 中，可以注册应用程序并向 Azure AD 进行身份验证。 应用程序凭据是应用程序 ID 和应用程序机密。 应用程序代表自身而不是用户来调用图形 API。 遵照[用户迁移](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users)一文步骤 1 中的说明。

## <a name="required-properties"></a>必需的属性
以下列表显示了在创建用户时必须指定的属性。
* **accountEnabled** - true
* **displayName** - 在用户的通讯簿中显示的名称。
* **passwordProfile** - 用户的密码配置文件。 

> [!NOTE]
> 仅适用于社交帐户（没有本地帐户凭据）。仍必须指定密码。 Azure AD B2C 将忽略你为社交帐户指定的密码。

* **userPrincipalName** - 用户主体名称 (someuser@contoso.com)。 用户主体名称必须包含租户的一个验证域。 若要指定 UPN，请生成新的 GUID 值，并将 `@` 与租户名称相连接。
* **mailNickname** - 用户的邮件别名。 此值可以是对 userPrincipalName 使用的同一个 ID。 
* **signInNames** - 指定用户登录名的一个或多个 SignInName 记录。 每个登录名必须在整个公司/租户中保持唯一。 仅适用于社交帐户。此属性可以留空。
* **userIdentities** - 一个或多个 UserIdentity 记录，这些记录指定社交标识提供者中的社交帐户类型和唯一用户标识符。
* [可选] **otherMails** - 仅适用于社交帐户，表示用户的电子邮件地址 

有关详细信息，请参阅[图形 API 参考](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>仅迁移社交帐户
只创建社交帐户，而不创建本地帐户凭据。 将 HTTPS POST 请求发送到图形 API。 请求正文包含要创建的社交帐户用户的属性。 最起码要指定必需的属性。 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

提交以下表单数据： 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>迁移社交帐户和本地帐户
创建结合社交标识的本地帐户。 将 HTTPS POST 请求发送到图形 API。 请求正文包含要创建的社交帐户用户的属性。 最起码要指定必需的属性。 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

提交以下表单数据： 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>常见问题
### <a name="how-can-i-know-the-issuer-name"></a>怎样知道颁发者名称？
颁发者名称（也称为标识提供者名称）在策略中配置。 如果不知道要在 `issuer` 中指定哪个值，请遵循以下过程：
1. 使用某个社交帐户登录
2. 复制 JWT 令牌中的 `sub` 值。 `sub` 通常包含用户在 Azure AD B2C 中的对象 ID。 或者在 Azure 门户中打开该用户的属性，并复制对象 ID。
3. 打开 [Azure AD 图形资源管理器](https://graphexplorer.azurewebsites.net)
4. 使用管理员帐户登录。 N
5. 运行以下 GET 请求。 将 userObjectId 替换为复制的用户 ID。 **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. 在从 Azure AD B2C 返回的 JSON 中找到 `userIdentities` 元素。
7. [可选] 可能还需要解码 `issuerUserId` 值。

> [!NOTE]
> 使用 B2C 租户本地的 B2C 租户管理员帐户。 帐户名的语法为 admin@tenant-name.onmicrosoft.com。

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>是否可将社交标识添加到现有的本地帐户？
是的。 创建本地帐户后，可以添加社交标识。 运行 HTTPS PATCH 请求。 将 userObjectId 替换为要更新的用户 ID。 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

提交以下表单数据： 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>是否可以添加多个社交标识？
是的。 可为单个 Azure AD B2C 帐户添加多个社交标识。 运行 HTTPS PATCH 请求。 将 userObjectId 替换为用户 ID。 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

提交以下表单数据： 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[可选] 用户迁移应用程序示例
[下载并运行示例应用 V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)。 示例应用 V2 使用包含虚构用户数据的 JSON 文件，这些数据包括单个帐户中的本地帐户、社交帐户，以及本地和社交标识。  若要编辑 JSON 文件，请打开 `AADB2C.UserMigration.sln` Visual Studio 解决方案。 在 `AADB2C.UserMigration` 项目中打开 `UsersData.json` 文件。 该文件包含用户实体的列表。 每个用户实体包含以下属性：
* **signInName** - 适用于本地帐户，表示用于登录的电子邮件地址
* **displayName** - 用户的显示名称
* **firstName** - 用户的名字
* **lastName** - 用户的姓氏
* **password** - 适用于本地帐户，表示用户的密码（可为空）
* **issuer** - 适用于社交帐户，表示标识提供者名称
* **issuerUserId** - 适用于社交帐户，表示社交标识提供者使用的唯一用户标识符。 该值应采用明文形式。 示例应用会将此值编码为 base64。
* **email** - 适用于社交帐户（非合并形式），表示用户的电子邮件地址

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> 如果未在示例中使用自己的数据更新 UsersData.json 文件，可以使用示例本地帐户凭据登录，但不能使用示例社交帐户登录。 若要迁移社交帐户，请提供真实数据。

有关如何使用示例应用的详细信息，请参阅 [Azure Active Directory B2C：用户迁移](active-directory-b2c-user-migration.md)
