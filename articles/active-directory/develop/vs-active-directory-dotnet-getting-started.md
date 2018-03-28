---
title: Visual Studio .NET MVC 项目中的 Azure AD 入门 | Microsoft Docs
description: 通过 Visual Studio 连接服务连接到或创建 Azure AD 之后，如何在 .NET MVC 项目中开始使用 Azure Active Directory
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 07fa4655d9e7ad74cae33391d55c7c9be1d446a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Azure Active Directory（ASP.NET MVC 项目）入门

> [!div class="op_single_selector"]
> - [入门](vs-active-directory-dotnet-getting-started.md)
> - [发生了什么情况](vs-active-directory-dotnet-what-happened.md)

本文提供通过 Visual Studio 的“项目”>“连接的服务”命令将 Active Directory 添加到 ASP.NET MVC 项目后的其他指南。 如果尚未将该服务添加到你的项目，可以随时执行该操作。

请参阅[我的 MVC 项目发生了什么情况？](vs-active-directory-dotnet-what-happened.md)，了解添加连接服务时对项目所做的更改。

## <a name="requiring-authentication-to-access-controllers"></a>访问控制器需要身份验证

项目中的所有控制器均带有 `[Authorize]` 属性。 此属性要求用户先进行身份验证，才能访问这些控制器。 若要允许匿名访问控制器，请从控制器删除此属性。 如果想要更详细地设置这些权限，请将该属性应用到需要身份验证的每个方法，而不是将它应用到控制器类。

## <a name="adding-signin--signout-controls"></a>添加 SignIn/SignOut 控件

若要将 SignIn/SignOut 控件添加到视图，可使用 `_LoginPartial.cshtml` 分部视图将该功能添加到某个视图。 下面是已添加到标准 `_Layout.cshtml` 视图的功能示例。 （注意带有 navbar-collapse 类的 div 中的最后一个元素）：

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 的身份验证方案](active-directory-authentication-scenarios.md)
- [向 ASP.NET Web 应用添加 Microsoft 登录功能](guidedsetups/active-directory-aspnetwebapp-v1.md)
