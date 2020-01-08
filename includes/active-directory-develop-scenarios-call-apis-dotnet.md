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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423757"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET 中的 AuthenticationResult 属性

获取令牌的方法将返回 `AuthenticationResult` （对于异步方法，则返回 `Task<AuthenticationResult>`。

在 MSAL.NET 中，`AuthenticationResult` 公开：

- 用于访问资源的 Web API `AccessToken`。 此参数是一个字符串，通常是 base64 编码的 JWT，但客户端不应在访问令牌中查看。 不保证格式稳定，对于资源可将令牌加密。 在客户端上依赖访问令牌内容编写代码是最大的错误来源之一，并且会违反客户端逻辑。 另请参阅[访问令牌](../articles/active-directory/develop/access-tokens.md)
- 用户的 `IdToken` （此参数是编码的 JWT）。 请参阅[ID 令牌](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` 指示令牌过期时的日期/时间
- `TenantId` 包含在其中找到用户的租户。 对于来宾用户（Azure AD B2B 方案），租户 ID 是来宾租户，而不是唯一租户。
为用户提供令牌时，`AuthenticationResult` 还包含有关此用户的信息。 对于在没有用户的情况下请求令牌的机密客户端流（对于应用程序），此用户信息为 null。
- 为其颁发令牌的 `Scopes`。
- 用户的唯一 Id。

### <a name="iaccount"></a>IAccount

MSAL.NET 定义帐户的概念（通过 `IAccount` 接口）。 这项中断性变更提供了正确的语义：同一用户可以在不同的 Azure AD 目录中拥有多个帐户这一事实。 此外，MSAL.NET 提供了有关来宾方案的更好信息，因为提供了 home 帐户信息。
下图显示了 `IAccount` 接口的结构：

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 类标识特定租户中的帐户。 它具有以下属性：

| 属性 | Description |
|----------|-------------|
| `TenantId` | GUID 的字符串表示形式，它是帐户所在的租户的 ID。 |
| `ObjectId` | GUID 的字符串表示形式，它是在租户中拥有帐户的用户的 ID。 |
| `Identifier` | 帐户的唯一标识符。 `Identifier` 是由逗号分隔的 `ObjectId` 和 `TenantId` 的串联，不是 base64 编码的。 |

`IAccount` 接口表示有关单个帐户的信息。 同一用户可以出现在不同的租户中，即用户可以拥有多个帐户。 其成员如下：

| 属性 | Description |
|----------|-------------|
| `Username` | 一个字符串，包含 UserPrincipalName （UPN）格式的可显示值，例如 john.doe@contoso.com。 此字符串可以为 null，而 HomeAccountId 和 HomeAccountId 不能为 null。 此属性替换 MSAL.NET 早期版本中 `IUser` 的 `DisplayableId` 属性。 |
| `Environment` | 一个包含此帐户的标识提供程序的字符串，例如 `login.microsoftonline.com`。 此属性将替换 `IUser`的 `IdentityProvider` 属性，但 `IdentityProvider` 也包含有关租户的信息（除了云环境），而此值仅是主机。 |
| `HomeAccountId` | 用户的主帐户的 AccountId。 此属性在 Azure AD 租户之间唯一标识用户。 |

### <a name="using-the-token-to-call-a-protected-api"></a>使用令牌调用受保护的 API

MSAL （在 `result`中）返回 `AuthenticationResult` 后，需要将其添加到 HTTP 授权标头中，然后再调用来访问受保护的 Web API。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```