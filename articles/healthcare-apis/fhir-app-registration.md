---
title: 为 Azure API for FHIR 注册 Azure Active Directory 应用
description: 本教程介绍需要为 Azure API for FHIR 和适用于 Azure 的 FHIR 服务器注册哪些应用程序。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 22f31cf3911b5ea24e8798fb226e389071fadd0b
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848972"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>为 Azure API for FHIR 注册 Azure Active Directory 应用

设置 Azure API for FHIR 或适用于 Azure 的 FHIR 服务器 (OSS) 时，可以选择多个配置选项。 对于开源项目，需要创建自己的资源应用程序注册。 对于 Azure API for FHIR，系统会自动创建此资源应用程序。

## <a name="application-registrations"></a>应用程序注册

要使某个应用程序能够与 Azure AD 交互，需要注册该应用程序。 在 FHIR 服务器的上下文中，涉及到两种类型的应用程序注册：

1. 资源应用程序注册。
1. 客户端应用程序注册。

资源应用程序是使用 Azure AD 保护的 API 或资源（具体而言，是 Azure API for FHIR）在 Azure AD 中的表示形式。  预配服务时，系统会自动创建 Azure API for FHIR 的资源应用程序；但如果使用开源服务器，则需要在 Azure AD 中[注册一个资源应用程序](register-resource-azure-ad-client-app.md)。 此资源应用程序具有标识符 URI。 建议指定与 FHIR 服务器 URI 相同的 URI。 此 URI 应用作 FHIR 服务器的 `Audience`。 客户端应用程序在请求令牌时，可以请求访问此 FHIR 服务器。

客户端应用程序是要请求令牌的客户端的注册。  在 OAuth 2.0 中，我们通常区分至少三种不同类型的应用程序：

1. 机密客户端，在 Azure AD 中也称为 Web 应用。  机密客户端是使用[授权代码流](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)，代表可提供有效凭据的已登录用户获取令牌的应用程序。 之所以将它们称作机密客户端，是因为它们能够保守机密，并在使用身份验证代码交换令牌时向 Azure AD 提供此机密。 由于机密客户端能够使用客户端机密进行自我身份验证，因此它们的受信任程度高于公共客户端，可以获得生存期更长的令牌，且可获得刷新令牌。 阅读有关如何[注册机密客户端](register-confidential-azure-ad-client-app.md)的详细信息。 请注意，必须注册回复 URL，客户端将凭此接收授权代码。
1. **公共客户端**。 这些客户端不能保守机密。 通常，它是一个移动设备应用程序或单页 JavaScript 应用程序，客户端中的机密可能会被用户发现。 公共客户端也使用授权代码流，但在获取令牌时，它们不可提供机密，只能获得生存期较短的令牌，且无法获得刷新令牌。 阅读有关如何[注册公共客户端](register-public-azure-ad-client-app.md)的详细信息。
1. 服务客户端。 这些客户端使用[客户端凭据流](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)代表自身（不代表用户）获取令牌。 它们通常代表以非交互方式访问 FHIR 服务器的应用程序。 例如引入进程。 使用服务客户端时，无需启动通过调用 `/authorize` 终结点获取令牌的进程。 服务客户端可以直接进入 `/token` 终结点，并提供客户端 ID 和客户端机密来获取令牌。 阅读有关如何[注册服务客户端](register-service-azure-ad-client-app.md)的详细信息

## <a name="next-steps"></a>后续步骤

在本概述文章中，你已了解了在使用 FHIR API 时可能需要的应用程序注册类型。

请根据自己的设置查看有关注册应用程序的操作指南

* [注册资源应用程序](register-resource-azure-ad-client-app.md)
* [注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)
* [注册公共客户端应用程序](register-public-azure-ad-client-app.md)
* [注册服务应用程序](register-service-azure-ad-client-app.md)

注册应用程序后，即可部署 Azure API for FHIR。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)