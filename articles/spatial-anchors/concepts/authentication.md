---
title: 身份验证和授权
description: 了解应用或服务向 Azure 空间定位点进行身份验证的各种方式，以及在限制访问 Azure 空间定位点时可以采用的控制级别。
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91857718"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 空间定位点身份验证和授权

本部分介绍从应用或 Web 服务向 Azure 空间定位点进行身份验证的各种方式，以及使用 Azure Active Directory (Azure AD) 中基于角色的访问控制来控制访问空间定位点帐户的方式。

## <a name="overview"></a>概述

![显示 Azure 空间定位点身份验证概述的示意图。](./media/spatial-anchors-authentication-overview.png)

若要访问给定的 Azure 空间定位点帐户，客户端需要先从 Azure 混合现实安全令牌服务 (STS) 获取访问令牌。 从 STS 获取的令牌将会生存 24 小时，其中包含空间定位点服务对帐户做出授权决策所需的信息，确保只有已获授权的主体可以访问该帐户。

可以通过交换方式从帐户密钥或 Azure AD 颁发的令牌获取访问令牌。

帐户密钥可让你快速开始使用 Azure 空间定位点服务；但是，在将应用程序部署到生产环境之前，建议将应用更新为使用基于 Azure AD 的身份验证。

可通过两种方式获取 Azure AD 身份验证令牌：

- 如果你正在生成企业应用程序，并且你的公司将 Azure AD 用作其标识系统，则你可以在应用中使用基于用户的 Azure AD 身份验证，并向使用现有 Azure AD 安全组的空间定位点帐户授予访问权限，或直接向组织中的用户授予访问权限。
- 否则，建议从支持你的应用的 Web 服务获取 Azure AD 令牌。 对于生产应用程序，建议使用某个支持性 Web 服务作为身份验证方法，因为这样可以避免在客户端应用程序中嵌入用于访问 Azure 空间定位点的凭据。

## <a name="account-keys"></a>帐户密钥

使用帐户密钥访问 Azure 空间定位点帐户是最简单的入手方法。 可以在 Azure 门户上找到你的帐户密钥。 导航到你的帐户，然后选择 "密钥" 选项卡。

![显示 "键" 页的屏幕截图，其中突出显示了 "主键" 的 "复制" 按钮。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

提供了两个密钥，两者都可用于访问空间定位点帐户。 建议定期更新用于访问该帐户的密钥；准备好两个不同的有效密钥可以实现此类更新且不会造成停机；只需交替更新主密钥和辅助密钥即可。

SDK 为使用帐户密钥进行身份验证提供内置支持；只需在 cloudSession 对象中设置 AccountKey 属性即可。

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

完成该操作后，SDK 将处理使用帐户密钥交换访问令牌的过程，并为应用提供必要的令牌缓存。

> [!WARNING]
> 建议使用帐户密钥快速加入，但仅限在开发/原型设计期间使用。 强烈建议不要使用应用程序中的嵌入式帐户密钥将应用程序交付到生产环境，而应使用下面列出的基于用户或基于服务的 Azure AD 身份验证方法。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于面向 Azure Active Directory 用户的应用程序，建议的方法是对用户使用 Azure AD 令牌，该令牌可以使用 [MSAL 库](../../active-directory/develop/msal-overview.md)来获取。 应该按照[应用注册快速入门](../../active-directory/develop/quickstart-register-app.md)中列出的步骤操作，其中包括：

1. Azure 门户中的配置
    1.    在 Azure AD 中将应用程序注册为**本机应用程序**。 在注册过程中，需要确定应用程序是否应为多租户，并提供应用程序允许的重定向 URL。
        1.  切换到“API 权限”选项卡
        2.  选择“添加权限”
            1.  在 "**我的组织使用的 api** " 选项卡下选择**混合现实资源提供程序**
            2.  选择“委托的权限”
            3.  选中“mixedreality”下的“mixedreality.signin”对应的框 
            4.  选择“添加权限”
        3.  选择“授予管理员许可”
    2.    向应用程序或用户授予对你的资源的访问权限：
        1.    在 Azure 门户中导航到你的空间定位点资源
        2.    切换到“访问控制(标识和访问管理)”选项卡
        3.    点击“添加角色分配”
            1.    [选择一个角色](#role-based-access-control)
            2.    在“选择”字段中，输入要向其分配访问权限的用户、组和/或应用程序的名称。
            3.    点击“保存”。
2. 在代码中：
    1.    确保使用自己的 Azure AD 应用程序的“应用程序 ID”和“重定向 URI”作为 MSAL 中的“client ID”和“RedirectUri”参数   
    2.    设置租户信息：
        1.    如果应用程序支持“仅限我的组织”，请将此值替换为你的“租户 ID”或“租户名称”（例如 contoso.microsoft.com）  
        2.    如果应用程序支持“任何组织目录中的帐户”，请将此值替换为 **Organizations** 
        3.    如果应用程序支持“所有 Microsoft 帐户用户”，请将此值替换为 **Common** 
    3.    在令牌请求中，将 **scope** 设置为“https://sts.mixedreality.azure.com//.default”。 此范围告知 Azure AD，应用程序正在请求混合现实安全令牌服务 (STS) 的令牌。

然后，应用程序应该能够从 MSAL 获取 Azure AD 令牌；可将该 Azure AD 令牌设置为云会话配置对象中的 **authenticationToken**。

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 服务身份验证

若要将利用 Azure 空间定位点的应用部署到生产环境，建议的做法是利用一个可充当身份验证请求中介的后端服务。 一般方案应如下图所示：

![Azure 空间定位点身份验证概述](./media/spatial-anchors-aad-authentication.png)

此处，假设应用使用自身的机制（例如：Microsoft 帐户、PlayFab、Facebook、Google ID、自定义用户名/密码等）向其后端服务进行身份验证。 在后端服务中对用户进行身份验证后，该服务可以检索 Azure AD 令牌，使用该令牌交换 Azure 空间定位点的访问令牌，然后将该访问令牌返回给客户端应用程序。

使用 [MSAL 库](../../active-directory/develop/msal-overview.md)检索 Azure AD 访问令牌。 应该按照[应用注册快速入门](../../active-directory/develop/quickstart-register-app.md)中列出的步骤操作，其中包括：

1.    Azure 门户中的配置：
    1.    在 Azure AD 中注册应用程序：
        1.    在 Azure 门户中，导航到“Azure Active Directory”并选择“应用注册” 
        2.    选择“新建应用程序注册”
        3.    输入应用程序的名称，选择“Web 应用/API”作为应用程序类型，然后输入服务的身份验证 URL。 点击“创建”。
        4.    在该应用程序上，点击 " **设置**"，然后选择 " **证书和密钥** " 选项卡。创建新的客户端密钥，选择持续时间，然后单击 " **添加**"。 请确保保存机密值，因为需要将其包含在 web 服务的代码中。
    2.    向应用程序和/或用户授予对你的资源的访问权限：
        1.    在 Azure 门户中导航到你的空间定位点资源
        2.    切换到“访问控制(标识和访问管理)”选项卡
        3.    点击“添加角色分配”
        1.    [选择一个角色](#role-based-access-control)
        2.    在“选择”字段中，输入你创建的且要向其分配访问权限的应用程序的名称。 如果希望应用的用户具有不同于空间锚点帐户的角色，则应在 Azure AD 中注册多个应用程序，并为每个应用程序分配单独的角色。 然后实现授权逻辑，以便为用户使用适当的角色。
        3.    注意-在 " **添加角色分配** " 选择中，你希望将 " **分配访问权限** " 设置为 "Azure AD 用户、组或服务主体"。
    3.    点击“保存”。
2.    在代码中（注意：可以使用 GitHub 上包含的服务示例）：
    1.    确保使用自己的 Azure AD 应用程序的应用程序 ID、应用程序机密和重定向 URI 作为 MSAL 中的 client ID、secret 和 RedirectUri 参数
    2.    在 MSAL 的 authority 参数中，将 tenant ID 设置为你自己的 Azure ADD 租户 ID。
    3.    在令牌请求中，将 **scope** 设置为“https://sts.mixedreality.azure.com//.default”

完成该操作后，后端服务便可以检索 Azure AD 令牌。 然后，该服务会使用该令牌来交换要返回给客户端的 MR 令牌。 使用 Azure AD 令牌检索 MR 令牌的过程是通过 REST 调用完成的。 下面是一个示例调用：

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

其中 Authorization 标头的格式如下：`Bearer <Azure_AD_token>`

响应包含纯文本格式的 MR 令牌。

该 MR 令牌随后会返回给客户端。 然后，客户端应用可在云会话配置中将此令牌设置为其访问令牌。

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

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>基于角色的访问控制

为了帮助控制向应用程序、服务或服务的 Azure AD 用户授予的访问权限级别，我们创建了以下角色，你可以根据需要对 Azure 空间定位点帐户分配这些角色：

- **空间定位点帐户所有者**：具有此角色的应用程序或用户可以创建、查询和删除空间定位点。 当你使用帐户密钥向帐户进行身份验证时，系统会将“空间定位点帐户所有者”角色分配给经过身份验证的主体。
- **空间定位点帐户参与者**：具有此角色的应用程序或用户可以创建、查询空间定位点，但无法删除空间定位点。
- **空间定位点帐户读取者**：具有此角色的应用程序或用户只能查询空间定位点，但无法创建新的空间定位点、删除现有的空间定位点或更新空间定位点中的元数据。 此角色通常用于其中有些用户策划了环境，还有一些用户只能重新调用以前放置在该环境中的定位点的应用程序。

## <a name="next-steps"></a>后续步骤

使用 Azure 空间定位点创建你的第一个应用。

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
