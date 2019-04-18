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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498355"
---
## <a name="register-your-application"></a>注册应用程序

可以通过以下两种方式之一注册应用程序。

### <a name="option-1-express-mode"></a>选项 1：快速模式

可以通过执行以下操作快速注册应用程序：
1. 访问 [Azure 门户 - 应用程序注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)。
1. 输入应用程序的名称并选择“注册”。
1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

若要注册应用程序并将应用程序注册信息添加到解决方案，请执行以下操作：
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. 选择“新注册”。
   - 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `Win-App-calling-MsGraph`。
   - 在“支持的帐户类型”部分，选择“任何组织目录中的帐户和个人 Microsoft 帐户(例如 Skype、Xbox、Outlook.com)”。
   - 选择“注册”以创建应用程序。
1. 在应用的页面列表中，选择“身份验证”。
1. 在“重定向 URI”部分，找到“建议用于公共客户端(移动、桌面)的重定向 URI”部分，然后选择“urn:ietf:wg:oauth:2.0:oob”。
1. 选择“保存”。
1. 转到 Visual Studio，打开 *App.xaml.cs* 文件，然后将 `Enter_the_Application_Id_here` 替换为刚注册并复制的应用程序 ID。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
