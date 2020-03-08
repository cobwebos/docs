---
title: 构建一个可调用 web Api 的后台应用程序-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的守护程序应用
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5718a23e5669de6ba16354a718d72b68d14bbf49
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894555"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>方案：调用 web Api 的后台应用程序

了解你需要构建一个可调用 web Api 的守护程序应用程序。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>概述

您的应用程序可以获取一个令牌，以代表自己调用 web API （不代表用户）。 此方案对于后台应用程序很有用。 它使用标准 OAuth 2.0[客户端凭据](v2-oauth2-client-creds-grant-flow.md)授予。

![守护程序应用](./media/scenario-daemon-app/daemon-app.svg)

下面是后台应用程序的一些用例示例：

- 用于预配或管理用户或在目录中执行批处理的 Web 应用程序
- 执行批处理作业或在后台运行的操作系统服务的桌面应用程序（如 Windows 上的 Windows 服务或 Linux 上的后台程序进程）
- 需要操作目录而不是特定用户的 Web Api

另一种常见的情况是，非守护程序应用程序使用客户端凭据：即使是代表用户操作时，他们也需要出于技术原因，访问 web API 或其自己的标识。 例如，访问 Azure Key Vault 或 Azure SQL 数据库中用于缓存的机密。

为自己的标识获取令牌的应用程序：

- 是机密客户端应用程序。 由于这些应用程序独立于用户访问资源，因此需要证明其身份。 它们也是敏感应用。 它们需要由 Azure Active Directory （Azure AD）租户管理员批准。
- 已使用 Azure AD 注册了机密（应用程序密码或证书）。 此密钥在调用 Azure AD 时传入，以获取令牌。

## <a name="specifics"></a>细节

> [!IMPORTANT]
>
> - 用户无法与后台应用程序进行交互。 后台应用程序需要其自己的标识。 此类型的应用程序通过使用其应用程序标识并向 Azure AD 提供其应用程序 ID、凭据（密码或证书）以及应用程序 ID URI 来请求访问令牌。 身份验证成功后，后台程序会从 Microsoft 标识平台终结点接收访问令牌（以及刷新令牌）。 然后，此令牌用于调用 web API （并根据需要进行刷新）。
> - 由于用户无法与后台应用程序进行交互，因此不能进行增量许可。 所有必需的 API 权限都需要在应用程序注册时进行配置。 应用程序的代码只请求静态定义的权限。 这也意味着后台应用程序不支持增量许可。

对于开发人员而言，此方案的端到端体验具有以下特点：

- 守护程序应用程序只能在 Azure AD 租户中运行。 构建尝试操作 Microsoft 个人帐户的后台应用程序并无意义。 如果你是业务线（LOB）应用开发人员，则需要在租户中创建后台程序应用。 如果你是 ISV，你可能想要创建多租户守护程序应用程序。 每个租户管理员都需要提供许可。
- 在[应用程序注册](./scenario-daemon-app-registration.md)期间，不需要答复 URI。 需要与 Azure AD 共享机密或证书或签名断言。 还需要请求应用程序权限，并授予管理员许可才能使用这些应用权限。
- 在应用程序注册过程中，[应用程序配置](./scenario-daemon-app-configuration.md)需要提供与 Azure AD 共享的客户端凭据。
- 用于获取具有客户端凭据流的令牌的[作用域](scenario-daemon-acquire-token.md#scopes-to-request)需要为静态作用域。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [后台应用程序注册](./scenario-daemon-app-registration.md)
