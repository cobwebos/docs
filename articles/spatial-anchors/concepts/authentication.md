---
title: 身份验证和授权 Azure 空间的定位点 |Microsoft Docs
description: 了解如何对 Azure 空间的定位点和到 Azure 空间的定位点旨在限制访问者的控制级别的各种方法的应用或服务可以进行身份验证。
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2b3f4cf4099459b655fc0e370935ddc8079de810
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073946"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>身份验证和授权 Azure 空间的定位点

本部分中，我们将介绍可以对从您的应用程序或 web 服务，Azure 空间的定位点进行身份验证的各种方法和在其中可以在 Azure Directory (Azure AD) 中使用基于角色的访问控制的方式来控制对空间的定位点帐户访问。  

## <a name="overview"></a>概述

![为 Azure 空间的定位点的身份验证的概述](./media/spatial-anchors-authentication-overview.png)

若要访问给定的 Azure 空间的定位点帐户，客户端需要先从 Azure 混合现实安全令牌服务 (STS) 获取访问令牌。 从 STS 获取的令牌 24 小时，和包含空间的定位点服务，以在帐户中，做出授权决定，并确保只有经过授权的主体可以访问该帐户的信息。 

从任一帐户密钥，或从 Azure AD 颁发的令牌，可以在 exchange 中获取访问令牌。 

帐户密钥，你可以快速开始使用 Azure 空间的定位点服务;但是，在部署应用程序进入生产之前，建议你更新应用以使用 Azure 基于 AD 的身份验证。 

可以通过两种方式获取 azure AD 身份验证令牌：

- 如果您构建企业应用程序，并且你的公司使用 Azure AD 作为其标识系统，可以使用基于用户的 Azure AD 中应用，并授予对空间的定位点帐户使用现有的 Azure AD 安全组，访问权限的身份验证或直接向你的组织中的用户。 
- 否则，建议从 web 服务支持你的应用获取 Azure AD 令牌。 使用支持 web 服务是为生产应用程序的身份验证的建议的方法，因为它避免了嵌入用于在客户端应用程序中访问 Azure 空间的定位点的凭据。 

## <a name="account-keys"></a>帐户密钥

使用帐户密钥来提供对 Azure 空间的定位点帐户的访问是入门的最简单方法。 在 Azure 门户中，您会发现你的帐户密钥。 导航到你的帐户，并选择"密钥"选项卡。

![为 Azure 空间的定位点的身份验证的概述](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


两个密钥供，它们都会对访问同时有效空间的定位点帐户。 建议您定期更新该密钥用于访问帐户;具有两个单独的有效密钥启用此类更新而无需停机;只需更新或者主键和辅助密钥。 

SDK 具有对帐户密钥; 使用进行身份验证的内置支持只需在 cloudSession 对象上设置 AccountKey 属性。 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

完成后，SDK 会处理的访问令牌，帐户密钥交换和需要为你的应用的令牌的缓存。 

> [!WARNING] 
> 对于快速面市，但在开发/原型制作仅建议使用的帐户密钥。 强烈建议不在发运你的应用程序到生产环境中，使用嵌入的帐户密钥并要改为使用基于用户的或基于服务的 Azure AD 身份验证方法列出的下一步。

## <a name="azure-ad-user-authentication"></a>Azure AD 用户身份验证

对于面向 Azure Active Directory 用户的应用程序，建议的方法是为了让用户，你可以获取以下文档中所述，使用 ADAL 库使用的 Azure AD 令牌： [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); 您应遵循下列出"快速启动"，其中包括的步骤：

1. 在 Azure 门户中的配置
    1.  在 Azure AD 中注册你的应用程序**本机应用程序**。 作为注册的一部分，需要确定你的应用程序是否应为多租户，并提供允许应用程序的 Url 的重定向。  
    2.  授予应用程序或用户访问权限所需的资源： 
        1.  导航到 Azure 门户中的空间的定位点资源
        2.  切换到**访问控制 (IAM)** 选项卡
        3.  命中**添加角色分配**
            1.  [选择一个角色](#role-based-access-control)
            2.  在中**选择**字段中输入的用户、 组和/或你想要分配的访问权限的应用程序的名称。 
            3.  命中**保存**。
2. 在您的代码：
    1.  请务必使用**应用程序 ID**并**重定向 Uri**作为自己的 Azure AD 应用程序的**客户端 ID**并**RedirectUri**ADAL 中的参数
    2.  设置租户信息：
        1.  如果你的应用程序支持**我的组织仅**，将此值替换为你**租户 ID**或**租户名称**(例如，contoso.microsoft.com)
        2.  如果你的应用程序支持**任何组织的目录中的帐户**，将此值替换为**组织**
        3.  如果你的应用程序支持**所有的 Microsoft 帐户用户**，将此值替换为**常见**
    3.  在你的令牌请求，将设置**资源**到"https://sts.mixedreality.azure.com"。 应用程序正在请求 Azure 空间的定位点服务的令牌向 Azure AD 将指示此"资源"。  

这样，你的应用程序应该能够从 ADAL 获取 Azure AD 令牌;您可以设置为该 Azure AD 令牌**authenticationToken**你的云会话配置对象。 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Azure AD 服务身份验证

若要部署到生产环境中利用 Azure 空间的定位点的应用的建议的选项是利用将 broker 身份验证请求的后端服务。 在此图中所述，应为常规方案：

![为 Azure 空间的定位点的身份验证的概述](./media/spatial-anchors-aad-authentication.png)

此处，假定您的应用程序使用其自身的机制 (例如：Microsoft 帐户，PlayFab、 Facebook、 Google ID、 自定义用户名/密码，等等。）若要对其的后端服务进行身份验证。 一旦你的用户进行身份验证到后端服务，服务可以检索的 Azure AD 令牌，交换访问令牌 Azure 空间的定位点，并返回到客户端应用程序。

以下文档中所述，使用 ADAL 库中检索 Azure AD 访问令牌： [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); 应遵循下列出"快速启动"，其中包括的步骤：

1.  在 Azure 门户中的配置：
    1.  在 Azure AD 中注册你的应用程序：
        1.  在 Azure 门户中，导航到**Azure Active Directory**，然后选择**应用注册**
        2.  选择**新应用程序注册**
        3.  输入应用程序中，选择的名称**Web 应用 / API**作为应用程序类型，然后输入你的服务身份验证 URL。 然后，单击**创建**。
        4.  该应用程序上命中**设置**，然后选择**密钥**选项卡。输入你的密钥的名称，选择持续时间，并命中**保存**。 请务必保存在该时间，显示的密钥值，因为你将需要将其包含在 web 服务的代码。
    2.  授予应用程序和/或用户访问权限所需的资源：
        1.  导航到 Azure 门户中的空间的定位点资源
        2.  切换到**访问控制 (IAM)** 选项卡
        3.  命中**添加角色分配**
        1.  [选择一个角色](#role-based-access-control)
        2.  在中**选择**字段中，输入您创建的应用程序的名称并想要分配的访问。 如果你希望应用程序的用户具有针对空间的定位点帐户不同的角色，应在 Azure AD 中注册多个应用程序并为每个单独的角色分配。 然后，实现你的授权逻辑来为你的用户使用了正确的角色。  
    3.  命中**保存**。
2.  在代码中 (请注意： 可以使用 GitHub 上提供的服务示例):
    1.  请确保使用的应用程序 ID、 应用程序机密和客户端 ID，作为自己的 Azure AD 应用程序的重定向 Uri 机密和 ADAL 中的重定向 Uri 参数
    2.  设置为你自己的租户 ID AAAzure 添加 ADAL 中的颁发机构参数中的租户 ID
    3.  在你的令牌请求，将设置**资源**到"https://sts.mixedreality.azure.com" 

这样，你的后端服务可以检索的 Azure AD 令牌。 它可以交换其 MR 令牌，它将返回向客户端。 使用 Azure AD 令牌检索 MR 令牌是通过 REST 调用。 下面是示例调用：

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

其中的 Authorization 标头的格式如下： `Bearer <accoundId>:<accountKey>`

而响应则包含纯文本中的 MR 令牌。
 
该 MR 令牌然后返回到客户端。 客户端应用程序然后可以将其与云会话配置中其访问令牌。

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>基于角色的访问控制

若要帮助控制的访问级别授予应用程序、 服务或服务，Azure AD 用户已创建，以便根据需要针对 Azure 空间的定位点帐户将分配以下角色：

- **空间的定位点帐户所有者**： 应用程序或具有此角色的用户将能够创建空间的定位点，查询它们，并将其删除。 当对使用帐户密钥，你的帐户进行身份验证**空间的定位点帐户所有者**角色分配给经过身份验证的主体。 
- **空间的定位点帐户参与者**： 应用程序或具有此角色的用户可以创建空间的定位点，查询它们，但不能删除它们。 
- **空间的定位点帐户读取器**： 应用程序或具有此角色的用户才能够查询空间的定位点，但不能创建新的、 删除现有的或更新上空间的定位点的元数据。 这是通常用于应用程序的某些用户评判环境，而其他人可以仅重新调用以前放置在该环境中的定位点。

## <a name="next-steps"></a>后续步骤

使用 Azure 空间的定位点创建第一个应用。

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
