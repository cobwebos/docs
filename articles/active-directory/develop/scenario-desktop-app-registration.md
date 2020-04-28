---
title: 注册用于调用 Web API 的桌面应用 - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的桌面应用（应用注册）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885151"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>调用 Web API 的桌面应用：应用注册

本文涵盖了桌面应用程序的应用注册详细信息。

## <a name="supported-account-types"></a>支持的帐户类型

在桌面应用程序中支持的帐户类型取决于你想要启用的体验。 由于此关系，支持的帐户类型取决于要使用的流。

### <a name="audience-for-interactive-token-acquisition"></a>交互式令牌获取的受众

如果桌面应用程序使用交互式身份验证，则可通过任何[帐户类型](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)将用户登录。

### <a name="audience-for-desktop-app-silent-flows"></a>桌面应用无提示流的受众

- 若要使用集成 Windows 身份验证或用户名和密码，应用程序需要在你自己的租户中将用户登录，例如，当你是业务线应用 (LOB) 开发人员时。 或者，在 Azure Active Directory 组织中，如果你的应用程序是 ISV 方案，也需要在你自己的租户中将用户登录。 Microsoft 个人帐户不支持这些身份验证流。
- 如果要使用设备代码流，则无法使用其 Microsoft 个人帐户登录用户。
- 如果你使用传递商家对客户 (B2C) 颁发机构和策略的社交标识来将用户登录，则只能使用交互式和用户-密码身份验证。

## <a name="redirect-uris"></a>重定向 URI

可以在桌面应用程序中使用的重定向 URI 取决于要使用的流。

- 如果使用交互式身份验证或设备代码流，请使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 若要实现此配置，请在应用程序的“身份验证”部分中选择相应的 URL。****
  
  > [!IMPORTANT]
  > 目前，默认情况下，MSAL.NET 会在 Windows 上运行的桌面应用程序中使用另一重定向 URI (`urn:ietf:wg:oauth:2.0:oob`)。 将来，我们需要更改此默认设置，因此建议你使用 `https://login.microsoftonline.com/common/oauth2/nativeclient`。

- 如果为 macOS 生成本机目标 C 或 Swift 应用，请根据应用程序的捆绑标识符，按以下格式注册重定向 URI： msauth. <your.app.bundle.id>：//auth. 将 <your.app.bundle.id> 替换为应用程序的捆绑标识符。
- 如果你的应用仅使用集成 Windows 身份验证或用户名和密码，则不需要为应用程序注册重定向 URI。 这些流前往 Microsoft 标识平台 v2.0 终结点并返回。 不会在任何特定 URI 上调用你的应用程序。
- 为了将设备代码流、集成 Windows 身份验证以及用户名和密码与也没有重定向 URI 的机密客户端应用程序流（在守护程序应用程序中使用的客户端凭据流）区分开来，你需要表明你的应用程序是公共客户端应用程序。 若要实现此配置，请转到应用程序的“身份验证”部分。**** 在“高级设置”子部分中，在“默认客户端类型”段落中，针对“将应用程序视为公共客户端”问题选择“是”。****************

  ![允许公共客户端](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API 权限

桌面应用程序为已登录用户调用 API。 它们需要请求委托的权限。 它们无法请求应用程序权限，权限仅在[守护程序应用程序](scenario-daemon-overview.md)中处理。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用：应用配置](scenario-desktop-app-configuration.md)
