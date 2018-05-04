---
title: 发布本机客户端应用 - Azure AD | Microsoft Docs
description: 介绍如何使本机客户端应用与 Azure AD 应用程序代理连接器通信，以提供本地应用的安全远程访问。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 823939e76bcf982d37b58595795dca84c1830e19
ms.sourcegitcommit: 4bc2aa08a116a0acca0af3b02b5c1d144d3cf9b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2018
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>如何使本机客户端应用与代理应用程序交互

除了 Web 应用程序以外，还可以使用 Azure Active Directory 应用程序代理来发布已使用 Azure AD 身份验证库 (ADAL) 配置的本机客户端应用。 本机客户端应用不同于 Web 应用，因为前者安装在设备上，而后者需通过浏览器进行访问。 

应用程序代理接受标头中发送的、由 Azure AD 颁发的令牌，因此支持本机客户端应用。 应用程序代理服务代表用户执行身份验证。 此解决方案不使用应用程序令牌进行身份验证。 

![最终用户、Azure Active Directory 和已发布应用程序之间的关系](./media/active-directory-application-proxy-native-client/richclientflow.png)

使用 Azure AD 身份验证库（负责处理身份验证并支持许多客户端环境）来发布本机应用程序。 应用程序代理适合[本机应用程序到 Web API 方案](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)。 

本文引导读者完成使用应用程序代理和 Azure AD 身份验证库发布本机应用程序的四个步骤。 

## <a name="step-1-publish-your-application"></a>步骤 1：发布应用程序
像发布任何其他应用程序一样发布代理应用程序，并分配可访问该应用程序的用户。 有关详细信息，请参阅[使用应用程序代理发布应用程序](active-directory-application-proxy-publish.md)。

## <a name="step-2-configure-your-application"></a>步骤 2：配置应用程序
按如下方式配置本机应用程序：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “应用注册”。
3. 选择“新建应用程序注册”。
4. 指定应用程序的名称，选择“本机”作为应用程序类型，并提供应用程序的重定向 URI。 

   ![创建新的应用注册](./media/active-directory-application-proxy-native-client/create.png)
5. 选择**创建**。

有关创建新应用注册的更多详细信息，请参阅[将应用程序与 Azure Active Directory 集成](.//develop/active-directory-integrating-applications.md)。


## <a name="step-3-grant-access-to-other-applications"></a>步骤 3：向其他应用程序授予访问权限
使本机应用程序可以向目录中的其他应用程序公开：

1. 仍在“应用注册”中，选择刚创建的新本机应用程序。
2. 选择“所需权限”。
3. 选择 **添加** 。
4. 打开第一个步骤，即“选择 API”。
5. 使用搜索栏查找在第一部分中发布的应用程序代理应用。 选择该应用并单击“选择”。 

   ![搜索代理应用](./media/active-directory-application-proxy-native-client/select_api.png)
6. 打开第二个步骤，即“选择权限”。
7. 使用复选框授予本机应用程序对代理应用程序的访问权限，并单击“选择”。

   ![授予对代理应用的访问权限](./media/active-directory-application-proxy-native-client/select_perms.png)
8. 选择“完成”。


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

应按如下所示替换示例代码中的变量：

* 可在 Azure 门户中找到“租户 ID”。 导航到“Azure Active Directory” > “属性”并复制目录 ID。 
* “外部 URL”是在代理应用程序中输入的前端 URL。 若要查找此值，请导航到代理应用的“应用程序代理”部分。
* 可在本机应用程序的“属性”页上找到本机应用的“应用 ID”。
* 可在本机应用程序的“重定向 URI”页上找到**本机应用的重定向 URI**。

使用这些参数编辑 ADAL 后，用户应能向本机客户端应用进行身份验证，即使他们在企业网络之外也是如此。 

## <a name="next-steps"></a>后续步骤

有关本机应用程序流的详细信息，请参阅[本机应用程序到 Web API](develop/active-directory-authentication-scenarios.md#native-application-to-web-api)

了解如何设置[应用程序代理的单一登录](application-proxy-sso-overview.md)
