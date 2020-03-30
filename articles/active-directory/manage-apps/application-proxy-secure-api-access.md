---
title: 使用 Azure AD 应用程序代理访问本地 API
description: Azure 活动目录的应用程序代理允许本机应用安全地访问您在本地或云 VM 上托管的 API 和业务逻辑。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166013"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理安全地访问本地 API

您可能有业务逻辑 API 在本地运行，或托管在云中的虚拟机上。 您的本机 Android、iOS、Mac 或 Windows 应用需要与 API 终结点进行交互才能使用数据或提供用户交互。 Azure AD 应用程序代理和[Azure 活动目录身份验证库 （ADAL）](/azure/active-directory/develop/active-directory-authentication-libraries)允许本机应用安全地访问本地 API。 与在应用层打开防火墙端口和控制身份验证和授权相比，Azure 活动目录应用程序代理是一个更快、更安全的解决方案。 

本文将引导您设置 Azure AD 应用程序代理解决方案，以便托管本机应用可以访问的 Web API 服务。 

## <a name="overview"></a>概述

下图显示了发布本地 API 的传统方法。 此方法需要打开传入端口 80 和 443。

![传统 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下图显示了如何使用 Azure AD 应用程序代理在不打开任何传入端口的情况下安全地发布 API：

![Azure AD 应用程序代理 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 应用程序代理构成解决方案的主干，用作 API 访问的公共终结点，并提供身份验证和授权。 您可以使用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)库从大量平台访问 API。 

由于 Azure AD 应用程序代理身份验证和授权构建在 Azure AD 之上，因此可以使用 Azure AD 条件访问来确保只有受信任的设备才能访问通过应用程序代理发布的 API。 对桌面使用 Azure AD 联接或 Azure AD 混合联接，对设备使用 Intune 管理。 您还可以利用 Azure 活动目录高级功能（如 Azure 多重身份验证）和机器学习支持的[Azure 标识保护](/azure/active-directory/active-directory-identityprotection)安全性。

## <a name="prerequisites"></a>先决条件

要遵循本演练，您需要：

- 管理员访问 Azure 目录，具有可创建和注册应用的帐户
- 示例 Web API 和本机客户端应用来自[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>通过应用程序代理发布 API

要通过应用程序代理在 Intranet 外部发布 API，请遵循与发布 Web 应用相同的模式。 有关详细信息，请参阅[教程：在 Azure 活动目录中添加用于远程访问的应用程序](application-proxy-add-on-premises-application.md)。

要通过应用程序代理发布秘密 API：

1. 将示例 SecretAPI 项目生成并发布为本地计算机或 Intranet 上的ASP.NET Web 应用。 确保可以在本地访问 Web 应用。 
   
1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”****。 然后选择**企业应用程序**。
   
1. 在**企业应用程序 - 所有应用程序**页面的顶部，选择 **"新应用程序**"。
   
1. 在"**添加应用程序**"页上，选择**本地应用程序**。 将显示 **"添加您自己的本地应用程序**"页。
   
1. 如果未安装应用程序代理连接器，系统将提示您安装它。 选择 **"下载应用程序代理连接器**"以下载并安装连接器。 
   
1. 在 **"添加您自己的本地应用程序**"页上安装应用程序代理连接器后：
   
   1. 在**名称**旁边，输入*秘密 API*。
      
   1. 在**内部 URL**旁边，输入用于从 Intranet 内访问 API 的 URL。
      
   1. 确保**预身份验证**已设置为 Azure**活动目录**。 
      
   1. 选择 **"在**页面顶部添加"，然后等待创建应用。
   
   ![添加 API 应用](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 在**企业应用程序 - 所有应用程序**页面上，选择**SecretAPI**应用。 
   
1. 在 **"机密 API - 概述"** 页上，选择左侧导航中**的属性**。
   
1. 您不希望 API 可供 **"MyApps"** 面板中的最终用户使用，因此在 **"属性"** 页的底部将 **"用户可见"** 设置为 **"否**"，然后选择"**保存**"。
   
   ![用户看不到](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
您已经通过 Azure AD 应用程序代理发布了 Web API。 现在，添加可以访问应用的用户。 

1. 在 **"机密 API - 概述"** 页上，选择左侧导航中的 **"用户"和"组**"。
   
1. 在 **"用户和组**"页上，选择 **"添加用户**"。  
   
1. 在"**添加分配"** 页上，选择 **"用户"和"组**"。 
   
1. 在 **"用户和组**"页上，搜索并选择可以访问应用的用户，至少包括您自己。 选择所有用户后，选择 **"选择**"。 
   
   ![选择和分配用户](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 返回"**添加分配"** 页，选择 **"分配**"。 

> [!NOTE]
> 使用集成 Windows 身份验证的 API 可能需要[其他步骤](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>注册本机应用并授予对 API 的访问权限

本机应用是开发用于特定平台或设备上的程序。 在本机应用可以连接和访问 API 之前，必须在 Azure AD 中注册它。 以下步骤演示如何注册本机应用，并授予它对通过应用程序代理发布的 Web API 的访问权限。

要注册 AppProxyNativeAppSample 本机应用：

1. 在 Azure 活动目录**概述**页上，选择**应用注册**，并在**应用注册**窗格的顶部选择 **"新建注册**"。
   
1. 在 **"注册应用程序**"页上：
   
   1. 在**名称**下，输入*应用程序代理本机Appsample*。 
      
   1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。******** 
      
   1. 在**重定向 URL**下，下拉并选择**公共客户端（移动&桌面），** 然后输入*https：/\/应用程序本机应用程序*。 
      
   1. 选择 **"注册**"，然后等待应用成功注册。 
      
      ![新建应用程序注册](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
您现在已注册了 Azure 活动目录中的应用代理应用。 要授予本机应用对秘密 API Web API 的访问权限，请进行以下访问：

1. 在 Azure 活动目录**概述** > **应用注册页上**，选择**AppProxyNativeAppSample**应用。 
   
1. 在**AppProxyNativeAppSample**页上，在左侧导航中选择**API 权限**。 
   
1. 在**API 权限**页上，选择 **"添加权限**"。
   
1. 在第一个**请求 API 权限**页上，选择**组织使用的 API**选项卡，然后搜索并选择**SecretAPI**。 
   
1. 在下一个**请求 API 权限**页上，选择**user_impersonation**旁边的复选框，然后选择 **"添加权限**"。 
   
    ![选择 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 回到**API 权限**页，您可以选择**Contoso 授予管理员同意，** 以防止其他用户单独同意应用。 

## <a name="configure-the-native-app-code"></a>配置本机应用代码

最后一步是配置本机应用。 NativeClient 示例应用中*Form1.cs*文件中的以下片段导致 ADAL 库获取请求 API 调用的令牌，并将其作为承载器附加到应用标头。 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
要将本机应用配置为连接到 Azure 活动目录并调用 API 应用代理，请使用 Azure AD 中的值更新本机客户端示例应用的*App.config*文件中的占位符值： 

- 将**目录（租户）ID**粘贴到`<add key="ida:Tenant" value="" />`字段中。 您可以在任一应用的 **"概述"** 页中查找和复制此值（GUID）。 
  
- 将 AppProxyNativeApp 示例**应用程序（客户端）ID** `<add key="ida:ClientId" value="" />`粘贴到字段中。 您可以在 AppProxyNativeApp 示例**概述**页面查找和复制此值（GUID）。
  
- 将应用代理原点应用示例**重定向 URI**粘贴`<add key="ida:RedirectUri" value="" />`到字段中。 您可以在 AppProxyNativeApp 示例**身份验证**页面查找和复制此值（URI）。 
  
- 将机密 API**应用程序 ID** `<add key="todo:TodoListResourceId" value="" />` URI 粘贴到字段中。 可以从 SecretAPI**公开 API**页中查找和复制此值（URI）。
  
- 将 SecretAPI**主页 URL** `<add key="todo:TodoListBaseAddress" value="" />`粘贴到字段中。 您可以在 SecretAPI**品牌**页面查找和复制此值（URL）。

配置参数后，生成并运行本机应用。 当您选择"**登录**"按钮时，应用允许您登录，然后显示一个成功屏幕，以确认它已成功连接到 SecretAPI。

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure 活动目录中添加本地应用程序，以便通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)
- [快速入门：配置客户端应用程序以访问 Web API](../develop/quickstart-configure-app-access-web-apis.md)
- [如何启用本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
