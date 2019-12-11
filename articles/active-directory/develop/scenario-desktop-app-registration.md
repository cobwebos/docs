---
title: 注册一个用于调用 web Api 的桌面应用程序-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的桌面应用（应用注册）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f7f2dfdbf404a092773857a0f7727618cd429a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965528"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>用于调用 web Api 的桌面应用-应用注册

本文介绍桌面应用程序的应用注册细节。

## <a name="supported-accounts-types"></a>支持的帐户类型

桌面应用程序中支持的帐户类型取决于您想要突出的体验。 由于这种关系，受支持的帐户类型取决于要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

如果桌面应用程序使用交互式身份验证，则可以通过任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面应用程序的无提示流的受众

- 若要使用集成的 Windows 身份验证或用户名/密码，你的应用程序需要在你自己的租户（LOB 开发人员）或 Azure Active directory 组织（ISV 方案）中登录用户。 Microsoft 个人帐户不支持这些身份验证流。
- 如果要使用设备代码流，则无法使用其 Microsoft 个人帐户登录用户。
- 如果你使用社交标识传递 B2C 机构和策略的用户登录，则只能使用交互式用户名和用户名密码身份验证。

## <a name="redirect-uris"></a>重定向 URI

要在桌面应用程序中使用的重定向 Uri 将取决于你想要使用的流。

- 如果使用的是**交互式身份验证**或**设备代码流**，则需要使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 可以通过单击应用程序的 "**身份验证**" 部分中相应的 URL 来完成此配置。
  
  > [!IMPORTANT]
  > 如今，在 Windows 上运行的桌面应用程序中，MSAL.NET 默认使用另一个重定向 URI （`urn:ietf:wg:oauth:2.0:oob`）。 将来我们将需要更改此默认设置，因此，我们建议使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`

- 如果要为 macOS 生成本机目标 C 或 Swift 应用程序，则需要根据应用程序的捆绑标识符，使用以下格式注册 redirectUri： **msauth >：//auth** （使用应用程序的捆绑包标识符替换 < > < id）（& c）
- 如果你的应用程序仅使用集成 Windows 身份验证或用户名/密码，则不需要为你的应用程序注册重定向 URI。 这些流执行到 Microsoft 标识平台 v2.0 终结点的往返过程，并且不会对任何特定的 URI 调用你的应用程序。
- 若要将设备代码流、集成的 Windows 身份验证和用户名/密码与没有重定向 Uri 的机密客户端应用程序流区分开来（守护程序应用程序中使用的客户端凭据流），需要将应用程序表达为公用客户端应用程序。 若要实现此配置，请参阅应用程序的 "**身份验证**" 部分。 然后，在 "**高级设置**" 子节的 "**默认客户端类型**" 段落中，选择 **"是**"，将**应用程序视为公共客户端**。

  ![允许公用客户端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 Api。 它们需要请求委托的权限。 但是，它们不能请求仅在[后台应用程序](scenario-daemon-overview.md)中处理的应用程序权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用-应用配置](scenario-desktop-app-configuration.md)
