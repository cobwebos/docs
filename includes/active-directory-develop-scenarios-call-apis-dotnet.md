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
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173133"
---
### <a name="authenticationresult-properties-in-msalnet"></a>在 MSAL.NET AuthenticationResult 属性

若要获取令牌的方法将返回`AuthenticationResult`(或对于异步方法中， `Task<AuthenticationResult>`。

在 MSAL.NET，`AuthenticationResult`公开：

- `AccessToken` 访问资源的 Web api。 此参数是一个字符串，通常使用 base64 编码的 JWT，但客户端应永远不会访问令牌中所示。 不保证格式稳定，对于资源可将令牌加密。 在客户端上依赖访问令牌内容编写代码是最大的错误来源之一，并且会违反客户端逻辑。 另请参阅[访问令牌](../articles/active-directory/develop/access-tokens.md)
- `IdToken` （此参数为编码的 JWT） 的用户。 请参阅[ID 令牌](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` 指示令牌的过期日期/时间
- `TenantId` 包含在其中找到用户的租户。 对于来宾用户 （Azure AD B2B 方案），则租户 ID 是来宾租户，而不是唯一的租户。
当令牌传递用户，`AuthenticationResult`还包含有关此用户的信息。 对于令牌 （适用于应用程序） 没有用户的请求所在的机密客户端流，此用户信息为 null。
- `Scopes`为颁发的令牌。
- 用户的唯一 Id。

### <a name="iaccount"></a>IAccount

MSAL.NET 定义帐户的概念 (通过`IAccount`接口)。 这项中断性变更提供了正确的语义：同一用户可以在不同的 Azure AD 目录中拥有多个帐户这一事实。 此外 MSAL.NET 提供更好的信息，在来宾方案的情况下，可以提供主帐户信息。
下图显示了结构`IAccount`接口：

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`类标识特定租户中的帐户。 它具有以下属性：

| 属性 | 描述 |
|----------|-------------|
| `TenantId` | GUID，这是帐户所在的租户 ID 的字符串表示形式。 |
| `ObjectId` | GUID，它是拥有在租户中的帐户的用户的 ID 的字符串表示形式。 |
| `Identifier` | 该帐户的唯一标识符。 `Identifier` 是的串联`ObjectId`和`TenantId`分隔为逗号和是 base64 编码。 |

`IAccount`接口表示有关单个帐户的信息。 同一用户可能会包含不同的租户，也就是说，用户可以有多个帐户。 其成员是：

| 属性 | 描述 |
|----------|-------------|
| `Username` | 包含 UserPrincipalName (UPN) 格式，例如，可显示的值的字符串john.doe@contoso.com。 此字符串可以为 null，而 HomeAccountId 和 HomeAccountId.Identifier 不会为 null。 此属性将替换`DisplayableId`属性的`IUser`MSAL.NET 的早期版本中。 |
| `Environment` | 一个包含此帐户，例如，标识提供者的字符串`login.microsoftonline.com`。 此属性将替换`IdentityProvider`的属性`IUser`，只不过`IdentityProvider`而此处的值是仅在主机中也有有关 （除了云环境），租户的信息。 |
| `HomeAccountId` | AccountId 为用户的主帐户。 此属性唯一标识对 Azure AD 租户的用户。 |

### <a name="using-the-token-to-call-a-protected-api"></a>使用令牌来调用受保护的 API

一次`AuthenticationResult`已经返回了的 MSAL (在`result`)，您需要将其添加到 HTTP 授权标头，再访问受保护的 Web API 的调用。

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```