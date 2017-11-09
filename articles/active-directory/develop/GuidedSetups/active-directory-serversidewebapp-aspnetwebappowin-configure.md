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
ms.custom: aaddev
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
## <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)注册应用程序
2.  输入应用程序的名称和电子邮件
3.  确保选中“指导式设置”选项
4.  按照说明向应用程序添加重定向 URL

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和电子邮件 
3.  确保取消选中“指导式设置”选项
4.  单击 `Add Platform`，并选择 `Web`
5.  返回 Visual Studio，在解决方案资源管理器中选择项目并查看“属性”窗口（如果看不到“属性”窗口，请按 F4）
6.  将“已启用 SSL”更改为 `True`
7.  复制 SSL URL 并将此 URL 添加到注册门户重定向列表中的重定向 URL 列表：<br/><br/>![项目属性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  在根文件夹内 `web.config` 中的 `configuration\appSettings` 部分之下添加以下内容：

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
用刚注册的应用程序 ID 替换 `ClientId`
</li>
<li>
用项目的 SSL URL 替换 `redirectUri`
</li>
</ol>
<!-- End Docs -->
