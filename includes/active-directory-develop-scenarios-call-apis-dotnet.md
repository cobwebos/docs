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
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76309006"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET 中的 AuthenticationResult 属性

获取令牌的方法将返回 `AuthenticationResult`。 对于异步方法，`Task<AuthenticationResult>` 返回。

在 MSAL.NET 中，`AuthenticationResult` 公开：

- 用于访问资源的 web API `AccessToken`。 此参数是一个字符串，通常是一个64编码的 JWT。 客户端不应在访问令牌中查看。 不保证该格式保持稳定，并且可以为资源加密。 编写依赖于客户端上的访问令牌内容的代码是错误和客户端逻辑中断的最大源之一。 有关详细信息，请参阅[访问令牌](../articles/active-directory/develop/access-tokens.md)。
- 用户 `IdToken`。 此参数是编码的 JWT。 有关详细信息，请参阅[ID 标记](../articles/active-directory/develop/id-tokens.md)。
- `ExpiresOn` 指示令牌过期的日期和时间。
- `TenantId` 包含在其中找到用户的租户。 对于 Azure Active Directory （Azure AD） B2B 方案中的来宾用户，租户 ID 是来宾租户，而不是唯一租户。
为用户提供令牌时，`AuthenticationResult` 还包含有关此用户的信息。 对于在没有应用程序用户的情况下请求令牌的机密客户端流，此用户信息为 null。
- 为其颁发令牌的 `Scopes`。
- 用户的唯一 ID。

### <a name="iaccount"></a>IAccount

MSAL.NET 通过 `IAccount` 接口定义帐户的概念。 此重大更改提供正确的语义。 同一用户可以在不同 Azure AD 目录中拥有多个帐户。 此外，MSAL.NET 提供了有关来宾方案的更好信息，因为提供了 home 帐户信息。
下图显示了 `IAccount` 接口的结构。

![IAccount 接口结构](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 类使用下表中显示的属性标识特定租户中的帐户。

| 属性 | Description |
|----------|-------------|
| `TenantId` | GUID 的字符串表示形式，它是帐户所在的租户的 ID。 |
| `ObjectId` | GUID 的字符串表示形式，它是在租户中拥有帐户的用户的 ID。 |
| `Identifier` | 帐户的唯一标识符。 `Identifier` 是由逗号分隔的 `ObjectId` 和 `TenantId` 的串联。 它们不是编码基数64。 |

`IAccount` 接口表示有关单个帐户的信息。 同一用户可以出现在不同的租户中，这意味着用户可以拥有多个帐户。 下表显示了其成员。

| 属性 | Description |
|----------|-------------|
| `Username` | 一个字符串，其中包含 UserPrincipalName （UPN）格式的可显示值，例如 john.doe@contoso.com。 此字符串可以为 null，这与 HomeAccountId 和 HomeAccountId 不是 null。 此属性替换 MSAL.NET 早期版本中 `IUser` 的 `DisplayableId` 属性。 |
| `Environment` | 一个字符串，其中包含此帐户的标识提供程序，例如 `login.microsoftonline.com`。 此属性将替换 `IUser`的 `IdentityProvider` 属性，除了除了云环境以外，`IdentityProvider` 还包含有关该租户的信息。 此处，该值仅为主机。 |
| `HomeAccountId` | 用户的主帐户的帐户 ID。 此属性在 Azure AD 租户之间唯一标识用户。 |

### <a name="use-the-token-to-call-a-protected-api"></a>使用令牌调用受保护的 API

`AuthenticationResult` 在 `result`中 MSAL 返回后，请将其添加到 HTTP 授权标头中，然后调用来访问受保护的 web API。

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```