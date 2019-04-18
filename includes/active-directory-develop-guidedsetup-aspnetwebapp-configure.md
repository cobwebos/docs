---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528521"
---
## <a name="register-your-application"></a>注册应用程序

若要注册应用程序并将应用程序注册信息添加到解决方案，有两个选项：

### <a name="option-1-express-mode"></a>选项 1：快速模式

可以通过执行以下操作快速注册应用程序：

1. 转到新的 [Azure 门户 - 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)窗格。
1. 输入应用程序的名称，然后单击“注册”。
1. 遵照说明下载内容，并一键式自动配置新应用程序。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：

1. 转到 Visual Studio 并执行以下操作：
   1. 在解决方案资源管理器中，选择项目并查看“属性”窗口（如果看不到“属性”窗口，请按 F4）。
   1. 将“已启用 SSL”更改为 `True`。
   1. 在 Visual Studio 中右键单击该项目，然后选择“属性”和“Web”选项卡。在“服务器”部分中，将项目 URL 更改为 SSL URL。
   1. 复制 SSL URL。 需在下一步将此 URL 添加到注册门户的重定向 URL 列表：<br/><br/>![项目属性](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. 选择“新注册”。
1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `ASPNET-Tutorial`。
   1. 在步骤 1（例如 `https://localhost:44368/`）的“回复 URL”中添加从 Visual Studio 复制的 SSL URL，然后单击“注册”。
1. 选择“身份验证”菜单，在“隐式授权”下设置“ID 令牌”，然后选择“保存”。
1. 在根文件夹内 `web.config` 中的 `configuration\appSettings` 部分之下添加以下内容：

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. 将 `ClientId` 替换为刚注册的应用程序 ID。
1. 将 `redirectUri` 替换为项目的 SSL URL。
