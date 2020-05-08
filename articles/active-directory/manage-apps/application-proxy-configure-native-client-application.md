---
title: 发布本机客户端应用 - Azure AD | Microsoft Docs
description: 介绍如何使本机客户端应用与 Azure AD 应用程序代理连接器通信，以提供本地应用的安全远程访问。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: a649a6fab1fe85efc4edcfd2d3151ab85302101b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610269"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何允许本机客户端应用程序与代理应用程序交互

你可以使用 Azure Active Directory （Azure AD）应用程序代理发布 web 应用，但也可以使用它来发布使用 Azure AD 身份验证库（ADAL）配置的 native client 应用程序。 本机客户端应用程序不同于 Web 应用，因为前者安装在设备上，而后者需通过浏览器进行访问。

为了支持本机客户端应用程序，应用程序代理接受在标头中发送 Azure AD 颁发的令牌。 应用程序代理服务对用户进行身份验证。 此解决方案不使用应用程序令牌进行身份验证。

![最终用户、Azure AD 和发布的应用程序之间的关系](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要发布本机应用程序，请使用 Azure AD 身份验证库，它负责身份验证并支持许多客户端环境。 应用程序代理适合[本机应用程序到 Web API 方案](../azuread-dev/native-app.md)。

本文引导读者完成使用应用程序代理和 Azure AD 身份验证库发布本机应用程序的四个步骤。

## <a name="step-1-publish-your-proxy-application"></a>步骤1：发布代理应用程序

像发布任何其他应用程序一样发布代理应用程序，并分配可访问该应用程序的用户。 有关详细信息，请参阅[使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步骤2：注册本机应用程序

现在需要在 Azure AD 中注册应用程序，如下所示：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时将显示 " **Azure Active Directory 管理中心**" 的**仪表板**。
1. 在边栏中选择 " **Azure Active Directory**"。 此时将显示**Azure Active Directory**概述 "页。
1. 在 Azure AD 概述侧栏中，选择 "**应用注册**"。 此时将显示所有应用注册的列表。
1. 选择“新注册”。  此时将显示 "**注册应用程序**" 页。

   ![在 Azure 门户中创建新的应用注册](./media/application-proxy-configure-native-client-application/create.png)

1. 在 "**名称**" 标题中，为应用程序指定面向用户的显示名称。
1. 在 "**支持的帐户类型**" 标题下，使用以下准则选择访问级别：

   - 若要仅针对组织内部的帐户，请仅选择**此组织目录中的帐户**。
   - 若要仅面向业务或教育客户，请选择**任何组织目录中的帐户**。
   - 若要以最大的一组 Microsoft 标识为目标，请选择**任何组织目录中的帐户和个人 Microsoft 帐户**。

1. 在 "**重定向 URI** " 标题中，选择 "**公用客户端（移动 & 桌面）**"，然后键入应用程序的重定向 URI。
1. 选择并阅读**Microsoft 平台策略**，然后选择 "**注册**"。 将创建并显示新应用程序注册的概述页。

有关创建新的应用程序注册的更多详细信息，请参阅将[应用程序与 Azure Active Directory 集成](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步骤3：授予对代理应用程序的访问权限

现在，你已注册了本机应用程序，接下来可以访问目录中的其他应用程序，在这种情况下，可以访问代理应用程序。 若要使本机应用程序能够公开给代理应用程序：

1. 在 "新建应用程序注册" 页的边栏中，选择 " **API 权限**"。 此时将显示新应用程序注册的 " **API 权限**" 页。
1. 选择“添加权限”****。 此时将显示 "**请求 API 权限**" 页。
1. 在 "**选择 api** " 设置下，选择 **"我的组织使用的 api**"。 将显示一个列表，其中包含目录中公开 Api 的应用程序。
1. 在 "搜索" 框中键入或滚动查找在[步骤1：发布代理应用程序](#step-1-publish-your-proxy-application)中发布的代理应用程序，然后选择代理应用程序。
1. 在 "**你的应用程序需要哪种类型的权限？** " 标题中，选择权限类型。 如果本机应用程序需要以登录用户的身份访问代理应用程序 API，请选择 "**委托的权限**"。
1. 在 "**选择权限**" 标题中，选择所需的权限，然后选择 "**添加权限**"。 本机应用程序的 " **API 权限**" 页现在显示你添加的代理应用程序和权限 API。

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步骤 4：编辑 Active Directory 身份验证库

在 Active Directory 身份验证库 (ADAL) 的身份验证上下文中编辑本机应用程序代码，以包含以下文本：

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

示例代码中的必需信息可在 Azure AD 门户中找到，如下所示：

| 所需信息 | 如何在 Azure AD 门户中找到它 |
| --- | --- |
| \<租户 ID> | **Azure Active Directory** > **Properties**属性 > **目录 ID** |
| \<代理应用> 的外部 Url | **企业应用** > 程序*代理应用* > **程序应用程序代理** > **外部 Url** |
| \<本机应用的应用 ID> | **企业应用程序** > *本机应用* > 程序**属性** > **应用程序 ID** |
| \<本机应用的重定向 URI> | **Azure Active Directory** > **App registrations**应用注册 > *本机应用程序* > **重定向 uri** |
| \<代理应用 API Url> | **Azure Active Directory** > **App registrations**应用注册 > *本机应用程序* > **api 权限** > **api/权限名称** |

在你用这些参数编辑 ADAL 后，你的用户可以对本机客户端应用程序进行身份验证，即使它们在公司网络之外。

## <a name="next-steps"></a>后续步骤

有关本机应用程序流的详细信息，请参阅[Azure Active Directory 中的本机应用](../azuread-dev/native-app.md)。

了解如何[在 Azure Active Directory 中设置应用程序的单一登录](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
