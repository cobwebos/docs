---
title: 身份验证和授权
description: 了解应用或服务可以对 Azure 空间锚点进行身份验证的各种方式，以及必须对 Azure 空间锚点进行访问的控制级别。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: c2800dc361eb274eeef706556e09731da079ccab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611749"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>对 Azure 空间锚点进行身份验证和授权

在本部分中，我们将介绍你可以从应用或 web 服务向 Azure 空间定位点进行身份验证的各种方式，以及使用 Azure 目录中基于角色的访问控制（Azure AD）来控制对空间锚定帐户的访问权限的方式。

## <a name="overview"></a>概述

![Azure 空间标记身份验证概述](./media/spatial-anchors-authentication-overview.png)

若要访问给定的 Azure 空间锚定帐户，客户端需要首先从 Azure Mixed Reality 安全令牌服务（STS）获取访问令牌。 从 STS 实时获取的令牌24小时，包含空间定位服务的信息以对帐户做出授权决策，并确保只有授权的主体可访问该帐户。

可以从帐户密钥或 Azure AD 颁发的令牌中获取访问令牌。

帐户密钥可让你快速开始使用 Azure 空间锚定服务;但是，在将应用程序部署到生产环境之前，建议你将应用程序更新为使用基于 Azure AD 的身份验证。

可以通过两种方式获取 Azure AD 身份验证令牌：

- 如果你正在构建企业应用程序，并且你的公司将 Azure AD 用作其标识系统，则可在应用中使用基于用户的 Azure AD 身份验证，并使用现有 Azure AD 安全组或直接向你组织中的用户授予对空间锚定帐户的访问权限。
- 否则，建议从支持您的应用程序的 web 服务获取 Azure AD 令牌。 对于生产应用程序，建议使用支持的 web 服务来进行身份验证，因为这样可以避免在客户端应用程序中嵌入凭据来访问 Azure 空间锚。

## <a name="account-keys"></a>帐户密钥

使用帐户密钥访问 Azure 空间锚定帐户是开始使用的最简单方法。 你会在 Azure 门户上找到你的帐户密钥。 导航到你的帐户，然后选择 "密钥" 选项卡。

![Azure 空间标记身份验证概述](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


提供两个密钥，它们同时对空间锚定帐户的访问权限都有效。 建议你定期更新用于访问该帐户的密钥;具有两个单独的有效密钥可启用此类更新而无需停机;只需更新主密钥和辅助密钥即可。

SDK 内置了对帐户密钥进行身份验证的支持;只需在 cloudSession 对象上设置 AccountKey 属性。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C + + NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C + + WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成此操作后，SDK 将处理访问令牌的帐户密钥的交换，并为应用提供必要的令牌缓存。

> [!WARNING]
> 建议快速使用帐户密钥，但仅限开发/原型开发期间使用。 强烈建议不要使用嵌入的帐户密钥将应用程序交付到生产，而是使用下一步中列出的基于用户或基于服务的 Azure AD 身份验证方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于以 Azure Active Directory 用户为目标的应用程序，建议的方法是将 Azure AD 令牌用于用户，可以使用[MSAL 库](../../active-directory/develop/msal-overview.md)获取该令牌。 你应按照[注册应用快速入门](../../active-directory/develop/quickstart-register-app.md)中列出的步骤进行操作，其中包括：

1. Azure 门户中的配置
    1.  在 Azure AD 中将应用程序注册为**本机应用程序**。 注册过程中，需要确定应用程序是否应为多租户应用程序，并提供应用程序允许的重定向 Url。
        1.  切换到**API 权限**选项卡
        2.  选择 "**添加权限**"
            1.  在 "**我的组织使用的 api** " 选项卡下选择**混合现实资源提供程序**
            2.  选择**委托权限**
            3.  选中 mixedreality 下的 " **mixedreality** " 复选框 **。**
            4.  选择 "**添加权限**"
        3.  选择 "**授予管理员许可**"
    2.  向应用程序或用户授予对资源的访问权限：
        1.  导航到 Azure 门户中的空间锚定资源
        2.  切换到 "**访问控制（IAM）** " 选项卡
        3.  命中**添加角色分配**
            1.  [选择角色](#role-based-access-control)
            2.  在 "**选择**" 字段中，输入要向其分配访问权限的用户、组和/或应用程序的名称。
            3.  点击“保存”。****
2. 在代码中：
    1.  请确保使用你自己的 Azure AD 应用程序的**应用程序 id**和**重定向 Uri**作为 ADAL 中的**客户端 id**和**RedirectUri**参数
    2.  设置租户信息：
        1.  如果你的应用程序**仅支持我的组织**，请将此值替换为你的**租户 ID**或**租户名称**（例如，contoso.microsoft.com）
        2.  如果你的应用程序支持**任何组织目录中的帐户**，请将此值替换为**组织**
        3.  如果你的应用程序支持**所有 Microsoft 帐户用户**，请将此值替换为**Common**
    3.  在令牌请求上，将**资源**设置为 "https://sts.mixedreality.azure.com"。 此 "资源" 将指示 Azure AD 应用程序正在请求 Azure 空间锚定服务的令牌。

这样，应用程序应该能够从 MSAL 获取 Azure AD 令牌;可以在云会话配置对象上将该 Azure AD 令牌设置为**authenticationToken** 。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C + + NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C + + WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服务身份验证

使用 Azure 空间锚点将应用部署到生产环境的建议选项是利用将代理身份验证请求的后端服务。 一般方案应该如下图所示：

![Azure 空间标记身份验证概述](./media/spatial-anchors-aad-authentication.png)

此处，假设你的应用程序使用自己的机制（例如： Microsoft 帐户、PlayFab、Facebook、Google ID、自定义用户名/密码等）来向其后端服务进行身份验证。 向后端服务对用户进行身份验证后，该服务可以检索 Azure AD 令牌，并为 Azure 空间锚定访问令牌，并将其返回给客户端应用程序。

使用[MSAL 库](../../active-directory/develop/msal-overview.md)检索 Azure AD 访问令牌。 你应按照[注册应用快速入门](../../active-directory/develop/quickstart-register-app.md)中列出的步骤进行操作，其中包括：

1.  Azure 门户中的配置：
    1.  在 Azure AD 中注册应用程序：
        1.  在 Azure 门户中，导航到**Azure Active Directory**，然后选择 "**应用注册**"
        2.  选择**新的应用程序注册**
        3.  输入应用程序的名称，选择 " **Web 应用/API** " 作为应用程序类型，然后输入服务的 "身份验证 URL"。 然后单击 "**创建**"。
        4.  在该应用程序上，点击 "**设置**"，然后选择 "**密钥**" 选项卡。输入密钥的名称，选择一个持续时间，然后单击 "**保存**"。 请确保保存当时显示的密钥值，因为需要将其包含在 web 服务的代码中。
    2.  向应用程序和/或用户授予对资源的访问权限：
        1.  导航到 Azure 门户中的空间锚定资源
        2.  切换到 "**访问控制（IAM）** " 选项卡
        3.  命中**添加角色分配**
        1.  [选择角色](#role-based-access-control)
        2.  在 "**选择**" 字段中，输入你创建的应用程序的名称，以及要向其分配访问权限的应用程序的名称。 如果希望应用的用户具有不同于空间锚点帐户的角色，则应在 Azure AD 中注册多个应用程序，并为每个应用程序分配单独的角色。 然后实现授权逻辑，为用户使用适当的角色。
    3.  点击“保存”。****
2.  在代码中（注意：可以使用 GitHub 上包含的服务示例）：
    1.  请确保使用你自己的 Azure AD 应用程序的应用程序 ID、应用程序密钥和重定向 Uri 作为 ADAL 中的客户端 ID、secret 和 RedirectUri 参数
    2.  将租户 ID 设置为自己的 AAAzure 在 ADAL 中的颁发机构参数中添加租户 ID
    3.  在令牌请求中，将 "**资源**" 设置https://sts.mixedreality.azure.com为 ""

使用该服务，后端服务可以检索 Azure AD 令牌。 然后，它会将它返回给客户端，以获取 MR 令牌。 使用 Azure AD 令牌检索 MR 标记是通过 REST 调用来完成的。 下面是一个示例调用：

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

其中，授权标头的格式如下所示：`Bearer <accoundId>:<accountKey>`

并且响应包含以纯文本形式的 MR 标记。

然后，该 MR 标记将返回到客户端。 然后，客户端应用可以将其设置为云会话配置中的访问令牌。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C + + NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C + + WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>基于角色的访问控制

为了帮助控制向你的服务中的应用程序、服务或 Azure AD 用户授予的访问级别，已为你创建了以下角色以根据你的 Azure 空间锚定帐户进行分配：

- **空间锚定帐户所有者**：具有此角色的应用程序或用户可以创建空间锚，对其进行查询，并将其删除。 使用帐户密钥对帐户进行身份验证时，会将**空间锚定帐户所有者**角色分配给经过身份验证的主体。
- **空间锚定帐户参与者**：具有此角色的应用程序或用户可以创建空间锚，对其进行查询，但不能删除它们。
- **空间锚定帐户读者**：具有此角色的应用程序或用户只能查询空间定位点，但不能创建新的空间定位点，也不能删除现有的内容或更新空间锚的元数据。 这通常用于某些用户组织环境的应用程序，而其他用户则只能撤回之前放置在该环境中的定位点。

## <a name="next-steps"></a>后续步骤

创建具有 Azure 空间锚点的第一个应用。

> [!div class="nextstepaction"]
> [Unity （HoloLens）](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity （iOS）](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity （Android）](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin （Android）](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin （iOS）](../quickstarts/get-started-xamarin-ios.md)
