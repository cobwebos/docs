---
title: 身份验证和授权
description: 了解应用或服务对 Azure 空间锚点进行身份验证的各种方式，以及必须对 Azure 空间锚点进行门控级别的控制。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656959"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>对 Azure 空间锚点的身份验证和授权

在本节中，我们将介绍从应用或 Web 服务向 Azure 空间锚点进行身份验证的各种方法，以及使用 Azure 目录 （Azure AD） 中的基于角色的访问控制来控制对空间锚点帐户的访问的方式。

## <a name="overview"></a>概述

![对 Azure 空间锚点的身份验证概述](./media/spatial-anchors-authentication-overview.png)

要访问给定的 Azure 空间锚点帐户，客户端需要首先从 Azure 混合现实安全令牌服务 （STS） 获取访问令牌。 从 STS 获得的令牌可生存 24 小时，并包含 Spatial 锚点服务的信息，以便对帐户做出授权决策，并确保只有经过授权的委托人才能访问该帐户。

访问令牌可以从任一帐户密钥或从 Azure AD 颁发的令牌获得交换。

帐户键使您能够快速开始使用 Azure 空间锚点服务;因此，您可以快速开始使用 Azure 空间锚点服务。但是，在将应用程序部署到生产中之前，建议您更新应用以使用基于 Azure AD 的身份验证。

Azure AD 身份验证令牌可通过两种方式获得：

- 如果要构建企业应用程序，并且公司使用 Azure AD 作为其标识系统，则可以在应用中使用基于用户的 Azure AD 身份验证，并使用现有的 Azure AD 安全组授予对空间锚点帐户的访问权限，或者直接给组织中的用户。
- 否则，建议从支持应用的 Web 服务获取 Azure AD 令牌。 对于生产应用程序，使用支持 Web 服务是推荐的身份验证方法，因为它可以避免在客户端应用程序中嵌入用于访问 Azure 空间锚点的凭据。

## <a name="account-keys"></a>帐户密钥

使用帐户密钥访问 Azure 空间锚点帐户是入门的最简单方法。 您可以在 Azure 门户中找到帐户密钥。 导航到您的帐户，然后选择"密钥"选项卡。

![对 Azure 空间锚点的身份验证概述](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


两个密钥可用，这两个键同时对访问空间锚点帐户有效。 建议您定期更新用于访问帐户的密钥;拥有两个单独的有效密钥可启用此类更新，而不会停机;您只需要更新主键和辅助键。

SDK 具有内置支持使用帐户密钥进行身份验证;您只需在云会话对象上设置 AccountKey 属性即可。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成后，SDK 将处理访问令牌的帐户密钥的交换，以及应用所需的令牌缓存。

> [!WARNING]
> 建议使用帐户密钥快速载入，但仅在开发/原型设计期间使用。 强烈建议不要使用其中的嵌入式帐户密钥将应用程序运送到生产部门，而是使用下面列出的基于用户或基于服务的 Azure AD 身份验证方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于面向 Azure 活动目录用户的应用程序，建议的方法是为用户使用 Azure AD 令牌，您可以使用[MSAL 库](../../active-directory/develop/msal-overview.md)获取该令牌。 您应该按照列出的步骤列出[注册应用快速入门](../../active-directory/develop/quickstart-register-app.md)，其中包括：

1. Azure 门户中的配置
    1.  在 Azure AD 中注册应用程序作为**本机应用程序**。 作为注册的一部分，您需要确定应用程序是否应是多租户，并提供应用程序允许的重定向 URL。
        1.  切换到**API 权限**选项卡
        2.  选择 **"添加权限**"
            1.  选择 **"我的组织使用"选项卡**下的 API 下**的混合现实资源提供程序**
            2.  选择**委派权限**
            3.  选中混合**现实.在****混合现实**下登录的框
            4.  选择 **"添加权限**"
        3.  选择 **"授予管理员同意"**
    2.  授予应用程序或用户对资源的权限：
        1.  导航到 Azure 门户中的空间锚点资源
        2.  切换到**访问控制 （IAM）** 选项卡
        3.  点击**添加角色分配**
            1.  [选择角色](#role-based-access-control)
            2.  在 **"选择"** 字段中，输入要向其分配访问权限的用户、组和/或应用程序的名称。
            3.  点击“保存”。****
2. 在代码中：
    1.  请确保使用自己的 Azure AD**应用程序的应用程序 ID**和**重定向 Uri**作为 ADAL 中的**客户端 ID**和**重定向 Uri**参数
    2.  设置租户信息：
        1.  如果应用程序仅支持**我的组织**，请将此值替换为**租户 ID**或**租户名称**（例如，contoso.microsoft.com）
        2.  如果应用程序支持**任何组织目录中的帐户**，请将此值替换为**组织**
        3.  如果应用程序支持**所有 Microsoft 帐户用户**，请将此值替换为 **"通用"**
    3.  在令牌请求时，将**资源**设置为""。https://sts.mixedreality.azure.com 此"资源"将指示 Azure AD 应用程序正在请求 Azure 空间锚点服务的令牌。

这样，您的应用程序应该能够从 MSAL 获取 Azure AD 令牌;因此，应用程序应该能够从 MSAL 获取 Azure AD 令牌。您可以将该 Azure AD 令牌设置为云会话配置对象的**身份验证令牌**。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure 广告服务身份验证

将利用 Azure 空间锚点部署应用以进行生产的建议选项是利用将代理身份验证请求的后端服务。 一般方案应如下图所述：

![对 Azure 空间锚点的身份验证概述](./media/spatial-anchors-aad-authentication.png)

在这里，假定你的应用使用自己的机制（例如：微软帐户，PlayFab，Facebook，谷歌ID，自定义用户名/密码等）来验证其后端服务。 向后端服务验证用户后，该服务可以检索 Azure AD 令牌，将其交换为 Azure 空间锚点的访问令牌，并将其返回到客户端应用程序。

使用[MSAL 库](../../active-directory/develop/msal-overview.md)检索 Azure AD 访问令牌。 您应该按照列出的步骤列出[注册应用快速入门](../../active-directory/develop/quickstart-register-app.md)，其中包括：

1.  Azure 门户中的配置：
    1.  在 Azure AD 中注册应用程序：
        1.  在 Azure 门户中，导航到**Azure 活动目录**，并选择**应用注册**
        2.  **选择新的申请注册**
        3.  输入应用程序的名称，选择 Web**应用/API**作为应用程序类型，然后输入服务 auth URL。 然后点击 **"创建**"。
        4.  在该应用程序上，点击 **"设置**"，然后选择 **"键**"选项卡。输入密钥的名称，选择持续时间，然后点击 **"保存**"。 请确保保存当时显示的键值，因为您需要将其包含在 Web 服务的代码中。
    2.  授予应用程序和/或用户对资源的权限：
        1.  导航到 Azure 门户中的空间锚点资源
        2.  切换到**访问控制 （IAM）** 选项卡
        3.  点击**添加角色分配**
        1.  [选择角色](#role-based-access-control)
        2.  在**选择**字段中，输入您创建的应用程序的名称，以及要为其分配访问权限的名称。 如果希望应用的用户对空间锚点帐户具有不同的角色，则应在 Azure AD 中注册多个应用程序，并为每个单独的角色分配。 然后实现授权逻辑，以便为用户使用正确的角色。
    3.  点击“保存”。****
2.  在代码中（注意：您可以使用 GitHub 中包含的服务示例）：
    1.  请确保使用自己的 Azure AD 应用程序的应用程序 ID、应用程序机密和重定向 Uri 作为 ADAL 中的客户端 ID、机密和重定向 Uri 参数
    2.  在 ADAL 中的权限参数中将租户 ID 设置为您自己的 AAAzure ADD 租户 ID
    3.  在令牌请求中，将**资源**设置为""https://sts.mixedreality.azure.com

这样，后端服务就可以检索 Azure AD 令牌。 然后，它可以将其交换为 MR 令牌，以便返回客户端。 使用 Azure AD 令牌检索 MR 令牌是通过 REST 调用完成的。 下面是一个示例调用：

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

其中授权标头的格式如下：`Bearer <accoundId>:<accountKey>`

响应以纯文本形式包含 MR 令牌。

然后，该 MR 令牌将返回到客户端。 然后，客户端应用可以在云会话配置中将其设置为其访问令牌。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[迅速](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++温RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>基于角色的访问控制

为了帮助控制授予服务的应用程序、服务或 Azure AD 用户的访问级别，已创建以下角色，以便您根据需要根据 Azure 空间锚点帐户进行分配：

- **空间锚点帐户所有者**：具有此角色的应用程序或用户能够创建空间锚点、查询它们并删除它们。 当您使用帐户密钥向帐户进行身份验证时，**空间锚点帐户所有者**角色将分配给经过身份验证的委托人。
- **空间锚点帐户参与者**：具有此角色的应用程序或用户能够创建空间锚点，查询它们，但不能删除它们。
- **空间锚点帐户读取器**：具有此角色的应用程序或用户只能查询空间锚点，但不能创建新锚点、删除现有锚点或更新空间锚点上的元数据。 这通常用于某些用户策划环境的应用程序，而其他用户只能调用以前放置在该环境中的锚点。

## <a name="next-steps"></a>后续步骤

使用 Azure 空间锚点创建第一个应用。

> [!div class="nextstepaction"]
> [统一（霍洛伦斯）](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [统一 （iOS）](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [统一（安卓）](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [霍洛伦斯](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [夏马林（安卓）](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [夏马林 （iOS）](../quickstarts/get-started-xamarin-ios.md)
