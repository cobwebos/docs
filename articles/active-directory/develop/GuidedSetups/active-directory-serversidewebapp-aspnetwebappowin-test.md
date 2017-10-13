---
title: "Azure AD v2 ASP.NET Web 服务器入门 - 测试 | Microsoft Docs"
description: "通过基于传统 Web 浏览器的使用 OpenID Connect 标准的应用程序，对 ASP.NET 解决方案实现 Microsoft 登录"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>测试代码

按 `F5` 在 Visual Studio 中运行项目。 浏览器将打开 http://localhost:{端口} 并你将重定向到该网址，其中会看到“使用 Microsoft 登录”按钮。 继续操作并单击它以登录。

准备好测试时，使用工作或学校帐户 (Azure Active Directory) 或个人帐户（live.com、 outlook.com）登录。 

![使用 Microsoft 浏览器窗口登录](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![使用 Microsoft 浏览器窗口登录](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>预期结果
登录后，用户将重定向到网站主页，该网站是 Microsoft 应用程序注册门户上应用程序注册信息中指定的 HTTPS URL。 此页现在显示 Hello {用户}、注销链接，以及查看用户声明的链接（即指向之前创建的 Authorize 控制器的链接）。

### <a name="see-users-claims"></a>查看用户的声明
选择超链接，查看用户的声明。 这你将引至控制器和视图，仅供通过身份验证的用户访问。

#### <a name="expected-results"></a>预期结果
 应看到一个表，其中包含已登录用户的基本属性：

| 属性 | 值 | 说明|
|---|---|---|
| 名称 | {用户全名} | 用户的名字和姓氏
|用户名 | <span>user@domain.com</span>| 用于标识已登录用户的用户名
| 使用者| {使用者}|一个唯一地标识 Web 上用户登录名的字符串|
| 租户 ID| {Guid}| guid 唯一地表示用户的 Azure Active Directory 组织。|

此外还将看到一个表格，其中包含身份验证请求中的所有用户声明。 有关 ID 令牌和说明中所有声明的列表，请参阅此[文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims " ID 令牌中所有声明的列表")。


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>对访问具有 [Authorize] 属性的方法进行测试（可选）
此步将测试作为匿名用户对 Authenticated 控制器的访问：<br/>
选择注销用户的链接并完成注销过程。<br/>
现在在浏览器中键入 http://localhost:{端口}/authenticated，访问受 `[Authorize]` 属性保护的控制器

#### <a name="expected-results"></a>预期结果
应收到提示，要求进行身份验证以查看视图。

## <a name="additional-information"></a>其他信息

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>保护整个网站
若要保护整个网站，请在 `Global.asax` `Application_Start` 方法中将 `AuthorizeAttribute` 添加到 `GlobalFilters`：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> 如何限制单个组织中的用户登录应用程序

> 默认情况下，个人帐户（包括 outlook.com、live.com 和其他），以及与 Azure Active Directory 集成的任何公司或组织的工作和学校帐户可以登录应用程序。 

> 如果要应用程序接受来自唯一的 Azure Active Directory 组织的登录，请将 `Common` 的 web.config 中的 `Tenant` 参数替换为组织的租户名，例如 contoso.onmicrosoft.com。之后将 OWIN 启动类中的 `ValidateIssuer` 参数更改为 `true`。

> 要允许来自特定组织列表的用户，将 `ValidateIssuer` 设置为 true，并使用 `ValidIssuers` 参数来指定组织的列表。

> 还可通过 IssuerValidator 参数实现自定义方法来验证颁发者。 有关 `TokenValidationParameters` 的详细信息，请参阅[此](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN 文章") MSDN 文章。

