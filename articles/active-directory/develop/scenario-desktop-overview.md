---
title: 构建用于调用 Web API 的桌面应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的桌面应用（概述）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a6cbb574245c7b11d2437693d60fd952becedba3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257548"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>方案：用于调用 Web API 的 桌面应用

了解构建用于调用 Web API 的桌面应用所需的一切。

## <a name="get-started"></a>入门

如果尚未创建第一个应用程序，请先完成快速入门：

- [快速入门：获取令牌并从 Windows 桌面应用中调用 Microsoft Graph API](./quickstart-v2-windows-desktop.md)
- [快速入门：获取令牌并从 UWP 应用中调用 Microsoft Graph API](./quickstart-v2-uwp.md)
- [快速入门：获取令牌并从 macOS 本机应用中调用 Microsoft Graph API](./quickstart-v2-ios.md)

## <a name="overview"></a>概述

编写桌面应用程序后，想要将用户登录到应用程序，并调用 Web API，如 Microsoft Graph、其他 Microsoft API 或自己的 Web API。 有几种选项：

- 可以使用交互式令牌获取：

  - 如果您的桌面应用程序支持图形控件，例如，它是一个 Windows.Form 应用程序、一个 WPF 应用程序或一个 macOS 本机应用程序。
  - 或者，如果它是 .NET Core 应用程序，并且你同意在系统浏览器中与 Azure Active Directory (Azure AD) 进行身份验证交互。

- 对于 Windows 托管的应用程序，在已加入 Windows 域或 Azure AD 的计算机上运行的应用程序也可以使用集成 Windows 身份验证以无提示方式获取令牌。
- 最后，虽然不推荐但也可以在公共客户端应用程序中使用用户名和密码。 在某些方案（例如 DevOps）中仍需要它。 但是，使用它会对应用程序施加约束。 例如，它无法使需要执行[多重身份验证](../authentication/concept-mfa-howitworks.md)（条件访问）的用户登录。 此外，应用程序将无法受益于单一登录 (SSO)。

  它也不适用于新式身份验证原则，仅出于遗留原因提供。

  ![桌面应用程序](media/scenarios/desktop-app.svg)

- 如果编写可移植命令行工具（可能是在 Linux 或 Mac 上运行的 .NET Core 应用程序），并且如果接受将身份验证委派给系统浏览器，则可以使用交互式身份验证。 .NET Core 不提供 [Web 浏览器](https://aka.ms/msal-net-uses-web-browser)，因此会在系统浏览器中进行身份验证。 否则，在这种情况下，最好的选择是使用设备代码流。 此流还适用于没有浏览器的应用程序，如 IoT 应用程序。

  ![无浏览器应用程序](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>特性

桌面应用程序具有很多特性。 它们主要取决于你的应用程序是否使用交互式身份验证。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用：应用注册](scenario-desktop-app-registration.md)
