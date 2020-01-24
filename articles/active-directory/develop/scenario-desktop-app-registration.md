---
title: 注册用于调用 web Api 的桌面应用-Microsoft 标识平台 |Microsoft
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
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702175"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>用于调用 web Api 的桌面应用：应用注册

本文介绍桌面应用程序的应用注册细节。

## <a name="supported-account-types"></a>支持的帐户类型

桌面应用程序中支持的帐户类型取决于您想要亮起的体验。 由于这种关系，受支持的帐户类型取决于要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

如果桌面应用程序使用交互式身份验证，则可以通过任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面应用程序的无提示流的受众

- 若要使用集成的 Windows 身份验证或用户名和密码，你的应用程序需要在你自己的租户中登录用户，例如，如果你是业务线（LOB）开发人员。 或者，在 Azure Active Directory 组织中，如果你的应用程序是 ISV 方案，则需要在你自己的租户中登录用户。 Microsoft 个人帐户不支持这些身份验证流。
- 如果要使用设备代码流，则无法使用其 Microsoft 个人帐户登录用户。
- 如果使用传递企业到企业（B2C）机构和策略的社交标识登录用户，则只能使用交互式用户名和用户名密码身份验证。

## <a name="redirect-uris"></a>重定向 URI

要在桌面应用程序中使用的重定向 Uri 取决于要使用的流。

- 如果使用交互式身份验证或设备代码流，请使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 若要实现此配置，请在应用程序的 "**身份验证**" 部分中选择相应的 URL。
  
  > [!IMPORTANT]
  > 目前，在 Windows 上运行的桌面应用程序中，MSAL.NET 默认使用另一个重定向 URI （`urn:ietf:wg:oauth:2.0:oob`）。 将来，我们需要更改此默认设置，因此，我们建议使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。

- 如果生成适用于 macOS 的本机目标 C 或 Swift 应用，请根据应用程序的捆绑包标识符注册重定向 URI，格式如下 <：//auth. >：：替换为应用程序的捆绑标识符 > <。
- 如果应用只使用集成 Windows 身份验证或用户名和密码，则不需要为应用程序注册重定向 URI。 这些流执行到 Microsoft 标识平台 v2.0 终结点的往返过程。 不会对任何特定的 URI 调用应用程序。
- 若要将设备代码流、集成的 Windows 身份验证和不具有重定向 Uri 的机密客户端应用程序流中的用户名和密码区分开来（守护程序应用程序中使用的客户端凭据流），需要表达应用程序是公用客户端应用程序。 若要实现此配置，请参阅应用程序的 "**身份验证**" 部分。 在 "**高级设置**" 子节的 "**默认客户端类型**" 段落中，选择 **"是"** ，将**应用程序视为公共客户端**。

  ![允许公用客户端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 Api。 它们需要请求委托的权限。 它们不能请求仅在[后台程序应用](scenario-daemon-overview.md)程序中处理的应用程序权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用：应用配置](scenario-desktop-app-configuration.md)
