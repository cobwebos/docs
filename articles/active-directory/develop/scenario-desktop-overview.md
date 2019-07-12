---
title: 调用 Web API 的桌面应用（概述）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的桌面应用（概述）
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
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785640"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>场景：用于调用 Web API 的 桌面应用

了解构建调用 Web API 的桌面应用所需的一切

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

如果还未创建第一个应用程序，请按 .NET 桌面快速入门或 UWP 快速入门的说明来这样做：

> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 Windows 桌面应用中调用 Microsoft Graph API](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 UWP 应用中调用 Microsoft Graph API](./quickstart-v2-uwp.md)

## <a name="overview"></a>概述

你编写桌面应用程序，需要将用户登录到应用程序并调用 Web API，例如 Microsoft Graph、其他 Microsoft API 或你自己的 Web API。 有几种可能性：

- 可以使用交互式令牌获取：

  - 如果桌面应用程序支持图形控件，例如是否 Windows.Form 应用程序或 WPF 应用程序。
  - 如果它是.NET Core 应用程序并且您同意已与 Azure AD 的身份验证交互发生在系统浏览器中

- 对于 Windows 托管的应用程序，也可让加入到 Windows 域或加入 AAD 的计算机上运行的应用程序使用集成 Windows 身份验证以无提示方式获取令牌。
- 最后，可以在公共客户端应用程序中使用用户名/密码，虽然不建议使用这种方式。 它仍需要在某些情况下 （如 DevOps)，但要注意，使用它将施加约束你的应用程序。 例如，它不能在需要执行多重身份验证 （条件性访问） 用户登录。 另外，应用程序无法利用单一登录 (SSO)。

  它也违反新式身份验证的原则，提供它只是出于历史原因。

  ![桌面应用程序](media/scenarios/desktop-app.svg)

- 如果您要编写可移植的命令行工具-可能在 Linux 或 Mac 上运行的.NET Core 应用-并接受身份验证可以委派给各个系统浏览器，您将能够使用交互式身份验证。 (.NET core 不提供尚未[Web 浏览器](https://aka.ms/msal-net-uses-web-browser)并因此在系统浏览器中执行身份验证)，否则为在这种情况下的最佳选项是使用设备代码流。 此流还可用于应用程序，而浏览器中，如 IoT 应用程序

  ![无浏览器应用程序](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>详情

桌面应用程序有许多具体要求，主要取决于应用程序是否使用交互式身份验证。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [桌面应用 - 应用注册](scenario-desktop-app-registration.md)
