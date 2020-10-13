---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76309006"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET 中的 AuthenticationResult 属性

用于获取令牌的方法返回 `AuthenticationResult`。 对于异步方法，将返回 `Task<AuthenticationResult>`。

在 MSAL.NET 中，`AuthenticationResult` 会公开

- `AccessToken`，以便 Web API 访问资源。 此参数是一个字符串，通常是一个 Base-64 编码的 JWT。 客户端不应该查看访问令牌的内部。 不保证格式稳定，并且可以为资源加密令牌。 编写的代码依赖于客户端上的访问令牌内容是最大的错误来源之一，并且会违反客户端逻辑。 有关详细信息，请参阅[访问令牌](../articles/active-directory/develop/access-tokens.md)。
- 用户的 `IdToken`。 此参数是编码的 JWT。 有关详细信息，请参阅 [ID 令牌](../articles/active-directory/develop/id-tokens.md)。
- `ExpiresOn` 会告知令牌过期的日期和时间。
- `TenantId` 包含用户所在的租户。 对于 Azure Active Directory (Azure AD) B2B 方案中的来宾用户，租户 ID 是来宾租户，而不是唯一的租户。
为用户传送令牌时，`AuthenticationResult` 还包含有关此用户的信息。 对于在请求令牌时未提供应用用户的机密客户端流，此用户信息为 null。
- 令牌的颁发`Scopes`。
- 用户的唯一 ID。

### <a name="iaccount"></a>IAccount

MSAL.NET 通过 `IAccount` 接口定义了帐户的概念。 此中断性变更提供了正确的语义。 同一用户可以在不同的 Azure AD 目录中拥有多个帐户。 此外，由于会提供主帐户信息，MSAL.NET 可以在使用来宾方案的情况下提供更有用的信息。
下图显示了 `IAccount` 接口的结构。

![IAccount 接口结构](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 类使用下表中显示的属性标识特定租户中的帐户。

| properties | 说明 |
|----------|-------------|
| `TenantId` | GUID 的字符串表示形式，是帐户所在租户的 ID。 |
| `ObjectId` | GUID 的字符串表示形式，是拥有租户中的帐户的用户的 ID。 |
| `Identifier` | 帐户的唯一标识符。 `Identifier` 是 `ObjectId` 和 `TenantId` 的串联，由逗号分隔。 它们不是 Base 64 编码的。 |

`IAccount` 接口表示单个帐户的相关信息。 同一用户可以存在于不同的租户中，这意味着一个用户可以有多个帐户。 其成员显示在下表中。

| properties | 说明 |
|----------|-------------|
| `Username` | 一个字符串，包含 UserPrincipalName (UPN) 格式的可显示值，例如 john.doe@contoso.com。 此字符串可以为 null，这不同于 HomeAccountId 和 HomeAccountId.Identifier，后两者不会为 null。 此属性替换 MSAL.NET 旧版本中 `IUser` 的 `DisplayableId` 属性。 |
| `Environment` | 一个字符串，包含此帐户的标识提供者，例如 `login.microsoftonline.com`。 此属性替换 `IUser` 的 `IdentityProvider` 属性，不同之处是 `IdentityProvider` 还包含除云环境以外的租户信息。 而此处的该值仅仅是主机。 |
| `HomeAccountId` | 用户的主帐户的帐户 ID。 此属性唯一标识 Azure AD 租户的用户。 |

### <a name="use-the-token-to-call-a-protected-api"></a>使用令牌调用受保护的 API

在 MSAL 在 `result` 中返回 `AuthenticationResult` 后，将它添加到 HTTP 授权标头，然后再调用该令牌以访问受保护的 Web API。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```