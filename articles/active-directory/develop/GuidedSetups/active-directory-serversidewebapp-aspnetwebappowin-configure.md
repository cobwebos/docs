---
title: "Azure AD v2 ASP.NET Web 服务器获取已启动 – 配置 |Microsoft 文档"
description: "与传统 web 基于浏览器应用程序使用 OpenID Connect 标准在 ASP.NET 解决方案上实现 Microsoft 登录"
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
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="create-an-application-express"></a>创建应用程序 （速成版）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 注册你的应用程序通过[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  输入你的应用程序和你的电子邮件的名称
3.  请确保选中的引导式安装的选项
4.  按照说明进行操作将重定向 URL 添加到你的应用程序

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到你的解决方案 （高级）
现在你需要注册你的应用程序中*Microsoft 应用程序注册门户*:
1. 转到[Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入你的应用程序和你的电子邮件的名称 
3.  请确保未选中的引导式安装的选项
4.  单击`Add Platform`，然后选择`Web`
5.  请转回 Visual Studio 并在解决方案资源管理器中，选择项目并查看属性窗口 （如果你看不到属性窗口中，按 F4）
6.  更改到已启用 SSL`True`
7.  复制 SSL URL 并将此 URL 添加到注册门户列表的重定向 Url 中的重定向 Url 列表：<br/><br/>![项目属性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  将在以下代码添加`web.config`部分下的根文件夹中`configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
替换`ClientId`刚刚注册的应用程序 id
</li>
<li>
替换`redirectUri`替换为你的项目 SSL URL
</li>
</ol>
<!-- End Docs -->
