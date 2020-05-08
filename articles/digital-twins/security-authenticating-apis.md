---
title: 了解 API 身份验证-Azure 数字孪生 |Microsoft Docs
description: 了解如何使用 Azure 数字孪生连接到 Api 并向其进行身份验证。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 63cc188b5bdabedf0902c02264c1a269ca8111af
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611800"
---
# <a name="connect-to-and-authenticate-with-apis"></a>连接到 Api 并向其进行身份验证

Azure 数字孪生使用 Azure Active Directory (Azure AD) 对用户进行身份验证并保护应用程序。 Azure AD 支持各种新式体系结构的身份验证。 所有这些体系结构都以行业标准协议 OAuth 2.0 或 OpenID Connect 为基础。 此外，开发人员可以使用 Azure AD 生成单租户和业务线 (LOB) 应用程序。 开发人员还可以使用 Azure AD 开发多[租户应用程序](how-to-multitenant-applications.md)。

有关 Azure AD 的概述，请访问[基础知识页](https://docs.microsoft.com/azure/active-directory/fundamentals/)的分步指南、概念和快速入门。

> [!TIP]
> 按照[教程](tutorial-facilities-setup.md)设置并运行 Azure 数字孪生示例应用。

若要将某个应用程序或服务与 Azure AD 进行集成，开发人员必须先将该应用程序注册到 Azure AD。 有关详细说明和屏幕截图，请阅读[此快速入门](../active-directory/develop/quickstart-register-app.md)。

Azure AD 支持[五种主要应用程序方案](../active-directory/develop/v2-app-types.md)：

* 单页应用程序 (SPA)：用户需要登录到受 Azure AD 保护的单页应用程序。
* Web 浏览器到 Web 应用程序：用户需要登录到受 Azure AD 保护的 Web 应用程序。
* 本机应用程序到 Web API：在手机、平板电脑或电脑上运行的本机应用程序需要对用户进行身份验证以通过受 Azure AD 保护的 Web API 获取资源。
* Web 应用程序到 Web API：Web 应用程序需要通过受 Azure AD 保护的 Web API 获取资源。
* 守护程序或服务器应用程序到 Web API：没有 Web UI 的守护程序应用程序或服务器应用程序需要通过受 Azure AD 保护的 Web API 获取资源。

> [!IMPORTANT]
> Azure 数字孪生支持以下两种身份验证库：
> * 最新的 [Microsoft 身份验证库 (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory 身份验证库 (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>从中间层 Web API 调用数字孪生

当开发人员构建数字孪生解决方案时，通常会创建中间层应用程序或 API。 然后，该应用或 API 将调用下游数字孪生 API。 若要支持此标准 Web 解决方案体系结构，请确保用户首先：

1. 使用中间层应用程序进行身份验证

1. 在身份验证期间获取 OAuth 2.0 代理令牌

1. 然后，使用获取的令牌，以向使用代理流的更下游 API 进行身份验证或调用该 API

有关如何安排代理流的说明，请参阅[OAuth 2.0 代理流](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 还可以查看[调用下游 Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) 中的代码示例。

## <a name="next-steps"></a>后续步骤

若要使用 OAuth 2.0 隐式授权流配置和测试 Azure 数字孪生，请阅读[配置 Postman](./how-to-configure-postman.md)。

若要了解有关 Azure 数字孪生安全性的信息，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。
