---
title: V1.0 应用的作用域（MSAL） |Microsoft
description: 了解使用 Microsoft 身份验证库 (MSAL) 的 v1.0 应用程序的范围。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75838b655a2f4a10bce6c4cbd8310598a1fd9ed2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424070"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>接受 v1.0 令牌中的 Web API 的范围

OAuth2 权限是指开发人员（1.0） web API （资源）应用程序向客户端应用程序公开的 Azure Active Directory （Azure AD）权限范围。 在许可期间，可将这些权限范围授予客户端应用程序。 请参阅 [Azure Active Directory 应用程序清单参考](reference-app-manifest.md#manifest-reference)中有关 `oauth2Permissions` 的部分。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>将请求访问权限范围限定为 v1.0 应用程序的特定 OAuth2 权限

若要获取 v2.0 1.0 应用程序的特定作用域的令牌（例如 Azure AD 图，请 https://graph.windows.net) 将所需的资源标识符与该资源的所需 OAuth2 权限串联起来，以创建作用域。

例如，若要以用户的身份访问应用 ID URI 为 `ResourceId` 的 v1.0 Web API，请执行以下操作：

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

若要使用 MSAL.NET Azure AD Azure AD 图形 API （https：\//graph.windows.net/）进行读取和写入，需创建范围列表，如以下示例中所示：

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

若要写入与 Azure 资源管理器 API （https：\//management.core.windows.net/）相对应的作用域，需要请求以下作用域（请注意两个斜杠）：

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> 你需要使用两个斜杠，这是因为，Azure 资源管理器 API 要求在其受众声明 (aud) 中使用一个斜杠，然后使用一个斜杠来分隔 API 名称与范围。

下面是 Azure AD 使用的逻辑：

- 对于具有 v1.0 访问令牌的 ADAL （Azure AD v1.0）终结点（唯一可能的），aud = 资源
- 对于 MSAL （Microsoft 标识平台（v2.0））终结点，该终结点请求用于接受 v2.0 令牌的资源的访问令牌，`aud=resource.AppId`
- 对于 MSAL （v2.0 终结点），为接受 v1.0 访问令牌的资源（上述情况）请求访问令牌，Azure AD 通过将最后一个斜杠之前的所有内容并将其用作资源标识符，从请求的作用域分析所需的受众。 因此，如果 https：\//database.windows.net 需要 "https：\//database.windows.net/" 的受众，则需要请求 "https：\//database.windows.net//.default" 范围。 另请参阅 GitHub 问题[#747：省略了资源 url 的尾部反斜杠，导致了 sql 身份验证失败](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)。

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>将请求访问权限范围限定为 v1.0 应用程序的所有权限

例如，若要获取 v1.0 应用程序的所有静态范围的令牌，请将“.default”追加到 API 的应用 ID URI：

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>请求客户端凭据流/后台应用的范围

使用客户端凭据流时，要传递的范围也是 `/.default`。 这会让 Azure AD 知道管理员在应用程序注册中许可的所有应用级权限。
