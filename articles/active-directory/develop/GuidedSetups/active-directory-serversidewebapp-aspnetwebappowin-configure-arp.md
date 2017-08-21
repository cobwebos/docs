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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>使用应用程序的注册信息配置 ASP.NET Web 应用

此步将配置项目以使用 SSL，然后使用 SSL URL 配置应用程序的注册信息。 此后通过 web.config 将应用程序注册信息添加到解决方案。

1.  在解决方案资源管理器中，选择项目并查看`Properties`窗口（如果你看不到“属性”窗口，请按 F4）
2.  将 `SSL Enabled` 更改为 `True`
3.  复制以上 `SSL URL` 中的值，并将其粘贴到此页顶部的`Redirect URL` 字段，然后单击“更新”:<br/><br/>![项目属性](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  在 `configuration\appSettings` 节下根文件夹内 `web.config` 文件中添加以下内容：

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

