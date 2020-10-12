---
title: 构建调用 Web API 的守护程序应用 - Microsoft 标识平台 | Azure
description: 了解如何构建用于调用 Web API 的守护程序应用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8c01724340df7e0efffe9847b49e1fe9d444ee01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257616"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>方案：用于调用 Web API 的守护程序应用程序

了解在构建用于调用 Web API 的守护程序应用程序时需要的所有项目。

## <a name="overview"></a>概述

应用程序可以获取一个令牌，代表自身（而不是用户）调用 Web API。 此方案适用于守护程序应用程序。 它使用标准 OAuth 2.0 [客户端凭据](v2-oauth2-client-creds-grant-flow.md)授予。

![守护程序应用](./media/scenario-daemon-app/daemon-app.svg)

下面是介绍守护程序应用用例的一些例子：

- 用于预配或管理用户，或者在目录中执行批处理的 Web 应用程序
- 执行批处理作业的桌面应用程序（例如 Windows 上的 Windows 服务，或 Linux 上的守护程序进程），或者在后台运行的操作系统服务
- 需操作目录而非特定用户的 Web API

非守护程序应用程序使用客户端凭据的另一种常见情况是：即使它们代表用户进行操作，出于技术原因，它们也需要以自己的标识访问 Web API 或资源。 例如，访问 Azure Key Vault 或用于缓存的 Azure SQL 数据库中的机密。

获取其自身标识的令牌的应用程序具有以下特点：

- 是机密客户端应用程序。 这些应用是以独立方式访问用户的资源，因此需证明其身份。 它们也是相当敏感的应用。 它们需要获得 Azure Active Directory (Azure AD) 租户管理员的批准。
- 已将机密（应用程序密码或证书）注册到 Azure AD。 该机密是在调用 Azure AD 以获取令牌的过程中传入的。

## <a name="specifics"></a>详情

> [!IMPORTANT]
>
> - 用户无法与守护程序应用程序进行交互。 守护程序应用程序需要其自己的标识 此类型的应用程序通过以下方式来请求访问令牌：使用其应用程序标识并向 Azure AD 提供其应用程序 ID、凭据（密码或证书）以及应用程序 ID URI。 在身份验证成功后，守护程序应用程序会从 Microsoft 标识平台终结点收到一个访问令牌（和一个刷新令牌）。 然后，将使用该令牌来调用 Web API（将会根据需要刷新该令牌）。
> - 由于用户无法与守护程序应用程序进行交互，因此无法进行增量许可。 所有必需的 API 权限都需要在注册应用程序时配置。 应用程序的代码只请求静态定义的权限。 这也意味着守护程序应用程序不会支持增量许可。

对于开发人员来说，此方案的端到端体验具有以下特点：

- 守护程序应用程序只能在 Azure AD 租户中工作。 构建尝试操作 Microsoft 个人帐户的后台应用程序并无意义。 如果你是业务线 (LOB) 应用开发人员，则需在租户中创建守护程序应用。 如果你是 ISV，你可能希望创建多租户守护程序应用程序， 每个租户管理员都需要提供许可。
- 在[注册应用程序](./scenario-daemon-app-registration.md)期间，回复 URI 不是必需的。 你需要与 Azure AD 共享机密或证书或已签名断言。 你还需要请求应用程序权限，并授予管理员许可才能使用这些应用权限。
- [应用程序配置](./scenario-daemon-app-configuration.md)需要提供客户端凭据，这些凭据是在应用程序注册期间与 Azure AD 共享的。
- 用于通过客户端凭据流获取令牌的[作用域](scenario-daemon-acquire-token.md#scopes-to-request)必须是静态作用域。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [守护程序应用 - 应用注册](./scenario-daemon-app-registration.md)
