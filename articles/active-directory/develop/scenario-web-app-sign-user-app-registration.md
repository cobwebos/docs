---
title: 用于登录用户的 Web 应用（应用注册）- Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（应用注册）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086702"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>用于登录用户的 Web 应用 - 应用注册

此页介绍用于登录用户的 Web 应用的应用注册详细信息。

若要注册应用程序，可以使用：

- [Web 应用快速入门](#register-an-app-using-the-quickstarts) - 除了提供创建应用程序的第一手体验，Azure 门户中的快速入门还包含名为“为我进行此更改”的按钮。 可以使用此按钮设置所需属性，对现有应用也可以这样做。 需根据自己的情况调整这些属性的值。 具体而言，应用程序的 web API URL 可能与建议的默认值不同，这也会影响注销 URI。
- 用于[手动注册应用程序](#register-an-app-using-azure-portal)的 Azure 门户
- PowerShell 和命令行工具

## <a name="register-an-app-using-the-quickstarts"></a>按照快速入门注册应用

如果导航到此链接，则可通过创建 Web 应用程序来创建启动过程：

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>使用 Azure 门户注册应用

> [!NOTE]
> 要使用的门户不同，具体取决于你的应用程序是在 Microsoft Azure 公有云还是在全国或主权云中运行。 有关详细信息，请参阅[国家/地区云](./authentication-national-cloud.md#app-registration-endpoints)

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。 或者，登录到所选的国家/地区云 Azure 门户。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务，然后选择“应用注册” > “新建注册”。
1. 出现“注册应用程序”页后，请输入应用程序的注册信息：
   1. 为应用程序选择支持的帐户类型（请参阅[支持的帐户类型](./v2-supported-account-types.md)）
   1. 在“名称”部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `AspNetCore-WebApp`。
   1. 在**重定向 URI** 中，添加将在成功进行身份验证后接受返回的令牌响应的应用程序类型和 URI 目标。 例如， `https://localhost:44321/` 。  选择“注册”。
1. 选择“身份验证”菜单，然后添加以下信息：
   1. 在“答复 URL”中添加 `https://localhost:44321/signin-oidc`。
   1. 在“高级设置”部分，将“注销 URL”设置为 `https://localhost:44321/signout-oidc`。
   1. 在“隐式授权”下，勾选“ID 令牌”。
   1. 选择**保存**。

### <a name="register-an-app-using-powershell"></a>使用 PowerShell 注册应用

> [!NOTE]
> 目前，Azure AD PowerShell 仅创建使用下述受支持帐户类型的应用程序：
>
> - MyOrg（仅此组织目录中的帐户）
> - AnyOrg（任何组织目录中的帐户）。
>
> 如果你想要创建一个使用个人 Microsoft 帐户（例如 Skype、XBox、Outlook.com）登录用户的应用程序，你可以首先创建一个多租户应用程序（受支持的帐户类型 = 任何组织目录中的帐户），然后更改Azure 门户`signInAudience`中的应用程序清单中的属性。 ASP.NET Core 教程的步骤[1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant)中详细说明了这一点（并且可以采用任何语言通用化到 web 应用）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-web-app-sign-user-app-configuration.md)
