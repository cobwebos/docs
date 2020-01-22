---
title: 构建一个可调用 web Api 的桌面应用-Microsoft 标识平台 |Microsoft
description: 了解如何构建一个可调用 web Api 的桌面应用程序（概述）
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23085e486972ef6a10b3bd2ee86ae1bb1dc3006
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293311"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>方案：调用 web Api 的桌面应用

了解你需要构建一个可调用 web Api 的桌面应用程序。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>开始体验

如果尚未创建第一个应用程序，请按照 .NET 桌面快速入门、通用 Windows 平台（UWP）快速入门或 macOS 本机应用程序快速入门：

> [!div class="nextstepaction"]
> [快速入门：从 Windows 桌面应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入门：从 UWP 应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [快速入门：从 macOS 本机应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-ios.md)

## <a name="overview"></a>概述

编写桌面应用程序，并想要将用户登录到应用程序，并调用 web Api，如 Microsoft Graph、其他 Microsoft Api 或自己的 web API。 有几种可能性：

- 可以使用交互式令牌获取：

  - 例如，如果您的桌面应用程序支持图形控件，例如，它是一个 Windows 窗体应用程序、一个 WPF 应用程序或一个 macOS 的本机应用程序。
  - 或者，如果它是 .NET Core 应用程序，并且您同意与系统浏览器中的 Azure Active Directory （Azure AD）交互。

- 对于 Windows 托管的应用程序，也可以将计算机上运行的应用程序（已加入 Windows 域或 Azure AD）连接到使用集成 Windows 身份验证以无提示方式获取令牌。
- 最后，尽管不建议这样做，但您也可以在公共客户端应用程序中使用用户名和密码。 在某些情况下，它仍是必需的，如 DevOps。 使用它会对应用程序施加限制。 例如，它不能登录需要执行多重身份验证（条件访问）的用户。 此外，应用程序也不会受益于单一登录（SSO）。

  它也不适用于新式身份验证原则，仅出于遗留原因提供。

  ![桌面应用程序](media/scenarios/desktop-app.svg)

- 如果你编写可移植命令行工具（可能是在 Linux 或 Mac 上运行的 .NET Core 应用程序），并且如果你接受将身份验证委派给系统浏览器，则可以使用交互式身份验证。 .NET Core 不提供[web 浏览器](https://aka.ms/msal-net-uses-web-browser)，因此会在系统浏览器中进行身份验证。 否则，在这种情况下，最好的选择是使用设备代码流。 此流还适用于没有浏览器的应用程序，如 IoT 应用程序。

  ![Browserless 应用程序](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>细节

桌面应用程序有很多 specificities。 它们主要取决于你的应用程序是否使用交互式身份验证。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用：应用注册](scenario-desktop-app-registration.md)
