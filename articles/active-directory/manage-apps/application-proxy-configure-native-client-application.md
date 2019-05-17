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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb36d6a03da07681db468184a489a79f7f0deab7
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825492"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何使本机客户端应用程序与代理应用程序交互

您可以使用 Azure Active Directory (Azure AD) 应用程序代理发布 web 应用，但它还可用于发布本机客户端应用程序配置与 Azure AD 身份验证库 (ADAL)。 本机客户端应用程序不同于 web 应用，因为它们安装在设备上，而通过浏览器访问 web 应用。

若要支持本机客户端应用程序，应用程序代理接受标头中发送的 Azure AD 颁发的令牌。 该应用程序代理服务会对用户身份验证。 此解决方案不使用应用程序令牌进行身份验证。

![最终用户、Azure Active Directory 和已发布应用程序之间的关系](./media/application-proxy-configure-native-client-application/richclientflow.png)

若要发布本机应用程序，使用 Azure AD 身份验证库，它负责处理身份验证并支持许多客户端环境。 应用程序代理适合[本机应用程序到 Web API 方案](../develop/native-app.md)。

本文引导读者完成使用应用程序代理和 Azure AD 身份验证库发布本机应用程序的四个步骤。

## <a name="step-1-publish-your-proxy-application"></a>步骤 1：发布代理应用程序

像发布任何其他应用程序一样发布代理应用程序，并分配可访问该应用程序的用户。 有关详细信息，请参阅[使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>步骤 2：注册本机应用程序

现在需要注册你的应用程序在 Azure AD 中，按如下所示：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 **仪表板**有关**Azure Active Directory 管理中心**出现。
2. 在侧栏中选择**Azure Active Directory**。 **Azure Active Directory**概述页将出现。
3. 在 Azure AD 的概述侧栏中选择**应用注册**。 显示所有应用程序注册的列表。
4. 选择“新注册”。 **注册应用程序**页将出现。

   ![创建新的应用注册](./media/application-proxy-configure-native-client-application/create.png)
5. 在中**名称**标题时，指定你的应用程序的面向用户的显示名称。
6. 下**支持的帐户类型**标题下方，选择一种访问级别，使用以下准则：
   - 若要针对你的组织内部的帐户，选择**此组织目录中的帐户**。
   - 若要针对仅企业或教育客户，请选择**任何组织的目录中的帐户**。
   - 若要尽可能最广泛的 Microsoft 标识集目标，请选择**中的任何组织的目录和个人 Microsoft 帐户的帐户**。
7. 在中**重定向 URI**标题下方，选择**公共客户端 （移动和桌面）**，然后键入你的应用程序的重定向 URI。
8. 选择并阅读**Microsoft 平台策略**，然后选择**注册**。 创建并显示新的应用程序注册的概览页。

有关详细信息创建新的应用程序注册，请参阅[将应用程序集成与 Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>步骤 3：向代理应用程序授予访问权限

现在，注册本机应用程序后，您可让它访问其他应用程序在你目录中，在这种情况下访问代理应用程序。 若要启用要向代理应用程序公开的本机应用程序：

1. 在新的应用程序注册页上侧栏中选择**API 权限**。 **API 权限**页上将显示新的应用程序注册。
2. 选择“添加权限”。 **请求 API 权限**页将出现。
3. 下**选择 API**设置中，选择**我的组织使用的 Api**。 将显示列表，其中包含你的目录中公开的 Api 的应用程序。
4. 在搜索框或滚动要查找的代理应用程序中发布的类型[步骤 1:发布代理应用程序](#step-1-publish-your-proxy-application)，然后选择代理应用程序。
5. 在中**应用程序需要哪种类型的权限？** 标题下方，选择权限类型。 如果本机应用程序需要访问作为已登录用户的代理应用程序 API，请选择**委派权限**。 如果本机应用程序运行时作为后台服务或后台程序已登录用户的情况下，选择**应用程序权限**。
6. 在中**选择权限**标题下方，选择所需的权限，然后选择**添加权限**。 **API 权限**页上的本机应用程序现在显示代理已添加的应用程序和权限 API。

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

中的示例代码所需的信息可在 Azure AD 门户中，按如下所示：

| 所需的信息 | 如何在 Azure AD 门户中找到它 |
| --- | --- |
| \<租户 ID > | **Azure Active Directory** > **属性** > **目录 ID** |
| \<代理应用的外部 Url > | **企业应用程序** > *代理应用程序* > **应用程序代理** > **外部 Url** |
| \<本机应用的应用程序 ID > | **企业应用程序** > *本机应用程序* > **属性** > **应用程序 ID** |
| \<本机应用程序的重定向 URI > | **Azure Active Directory** > **应用注册** > *本机应用程序* > **重定向 Uri** |
| \<代理应用 API Url > | **Azure Active Directory** > **应用注册** > *本机应用程序* > **API 权限**  >  **API / 权限名称** |

在编辑这些参数与 ADAL 后，用户可以进行身份验证本机客户端应用程序即使处于公司网络外部的。

## <a name="next-steps"></a>后续步骤

有关本机应用程序流的详细信息，请参阅[Azure Active Directory 中的本机应用](../develop/native-app.md)。

了解如何设置[单一登录方式登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
