---
title: 调用 Web API 的桌面应用（应用注册）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的桌面应用（应用注册）
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
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268403"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>调用 Web API 的桌面应用 - 应用注册

本文包含适用于桌面应用程序的应用注册详细信息。

## <a name="supported-accounts-types"></a>支持的帐户类型

桌面应用程序中支持的帐户类型取决于您想要突出的体验。 由于这种关系，受支持的帐户类型取决于要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

如果桌面应用程序使用交互式身份验证，则可以通过任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)登录用户。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面应用无提示流的受众

- 若要使用集成的 Windows 身份验证或用户名/密码，你的应用程序需要在你自己的租户（LOB 开发人员）或 Azure Active directory 组织（ISV 方案）中登录用户。 Microsoft 个人帐户不支持这些身份验证流。
- 如果要使用设备代码流，则无法使用其 Microsoft 个人帐户登录用户。
- 如果使用传入 B2C 颁发机构和策略的社交标识来登录用户，则只能使用交互式和用户-密码身份验证。

## <a name="redirect-uris"></a>重定向 URI

要在桌面应用程序中使用的重定向 Uri 将取决于你想要使用的流。

- 如果你使用的是**交互式身份验证**或**设备代码流**，你将需要使用`https://login.microsoftonline.com/common/oauth2/nativeclient`。 可以通过单击应用程序的 "**身份验证**" 部分中相应的 URL 来完成此配置。
  
  > [!IMPORTANT]
  > 目前，在默认情况下，MSAL.NET 会在 Windows 上运行的桌面应用程序中使用另一重定向 URI (`urn:ietf:wg:oauth:2.0:oob`)。 我们在将来需要更改此默认设置，因此建议你使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`

- 如果要为 macOS 生成本机目标 C 或 Swift 应用程序，则需要根据应用程序的捆绑标识符，使用以下格式注册 redirectUri： **< msauth >：//auth** （replace <）。你的 app.config. id > 应用程序的捆绑包标识符）
- 如果你的应用程序仅使用集成 Windows 身份验证或用户名/密码，则不需要为你的应用程序注册重定向 URI。 这些流执行到 Microsoft 标识平台 v2.0 终结点的往返过程，并且不会对任何特定的 URI 调用你的应用程序。
- 若要从不具有重定向 Uri 的机密客户端应用程序流（守护程序应用程序中使用的客户端凭据流）区分设备代码流、集成的 Windows 身份验证和用户名/密码，需将应用程序是公用客户端应用程序。 若要实现此配置，请参阅应用程序的 "**身份验证**" 部分。 然后，在 "**高级设置**" 子节的 "**默认客户端类型**" 段落中，选择 **"是**"，将**应用程序视为公共客户端**。

  ![允许公共客户端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 Api。 它们需要请求委托的权限。 但是，它们不能请求仅在[后台应用程序](scenario-daemon-overview.md)中处理的应用程序权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用 - 应用配置](scenario-desktop-app-configuration.md)
