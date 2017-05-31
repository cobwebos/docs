---
title: "Azure AD v2 ASP.NET Web 服务器入门 - 配置 | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: zh-cn


---

## <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)注册应用程序
2.    输入应用程序的名称和你的电子邮件
3.    确保选中“指导式设置”选项
4.    按照说明向应用程序添加重定向 URL

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和你的电子邮件 
3.    确保取消选中“指导式设置”选项
4.    单击 `Add Platforms`，然后选择 `Web`
5.    返回 Visual Studio，在解决方案资源管理器中选择项目并查看“属性”窗口（如果看不到“属性”窗口，请按 F4）
6.    将“已启用 SSL”更改为 `True`：<br/><br/>![项目属性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    复制 SSL URL 并将此 URL 添加到注册门户的重定向 URL 列表
8.    在根文件夹内 `web.config` 中的 `configuration\appSettings` 部分之下添加以下内容：

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. 用刚注册的应用程序 ID 替换 `ClientId`
10. 用项目的 SSL URL 替换 `redirectUri` 
<!-- End Docs -->

> 注意
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>仅允许一个组织中的用户登录应用程序
> 默认情况下，个人帐户（包括 outlook.com、live.com 和其他帐户）以及与 Azure Active Directory 集成的任何公司或组织的工作和学校帐户都可以登录你的应用程序。 如果希望应用程序仅接受来自某一 Azure Active Directory 组织的登录，请将 `web.config` 中的 `Tenant` 参数从 `Common` 替换为该组织的租户名，例如 `contoso.onmicrosoft.com`。 随后将 OWIN Startup 类中的 ValidateIssuer 参数更改为 `true`。
若要仅允许来自特定组织列表的用户，请将 `ValidateIssuer` 设置为 `true`，并使用 `ValidIssuers` 参数指定组织列表。
还可通过 `IssuerValidator parameter` 实现自定义方法来验证颁发者。 有关 `TokenValidationParameters` 的详细信息，请参阅[此](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx) MSDN 文章。


