---
title: 使用 Azure AD 应用程序代理访问本地 Api
description: Azure Active Directory 的应用程序代理允许本机应用安全地访问你在本地或云 Vm 上托管的 Api 和业务逻辑。
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
ms.custom: has-adal-ref
ms.openlocfilehash: 74c6951a718d15a9ca7b84e92662272ba1bfd182
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610286"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理安全访问本地 Api

你可以在本地运行业务逻辑 Api，也可以在云中的虚拟机上托管。 本机 Android、iOS、Mac 或 Windows 应用需要与 API 终结点交互以使用数据或提供用户交互。 通过 Azure AD 应用程序代理和[Azure Active Directory 身份验证库（ADAL）](/azure/active-directory/develop/active-directory-authentication-libraries) ，本地应用可以安全访问本地 api。 Azure Active Directory 应用程序代理比在应用层打开防火墙端口并控制身份验证和授权更安全、更安全。

本文逐步讲解如何设置 Azure AD 应用程序代理解决方案，以便托管本机应用可以访问的 web API 服务。

## <a name="overview"></a>概述

下图显示了发布本地 Api 的传统方法。 此方法需要打开传入端口80和443。

![传统 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下图显示了如何使用 Azure AD 应用程序代理安全地发布 Api，而无需打开任何传入端口：

![Azure AD 应用程序代理 API 访问](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 应用程序代理形成解决方案的主干，作为 API 访问的公共终结点，并提供身份验证和授权。 可以通过使用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)库，从大量平台访问 api。

由于 Azure AD 应用程序代理身份验证和授权建立在 Azure AD 之上，因此你可以使用 Azure AD 条件访问来确保只有受信任的设备才能访问通过应用程序代理发布的 Api。 使用连接到桌面的 Azure AD 联接或 Azure AD 混合，并为设备管理 Intune。 还可以利用 Azure 多重身份验证等 Azure Active Directory Premium 功能，以及支持机器学习的[Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)安全性。

## <a name="prerequisites"></a>先决条件

若要执行本演练，你需要：

- 使用可以创建和注册应用程序的帐户来管理对 Azure 目录的访问权限
- 示例 web API 和本机客户端应用[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>通过应用程序代理发布 API

若要通过应用程序代理在 intranet 外部发布 API，需要遵循与发布 web 应用相同的模式。 有关详细信息，请参阅[教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

若要通过应用程序代理发布 SecretAPI web API：

1. 生成示例 SecretAPI 项目并将其发布为本地计算机或 intranet 上的 ASP.NET web 应用。 请确保可以在本地访问 web 应用。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”****。 然后选择 "**企业应用程序**"。

1. 在 "**企业应用程序-所有应用程序**" 页的顶部，选择 "**新建应用程序**"。

1. 在 "**添加应用程序**" 页上，选择 **"本地应用程序**"。 此时将显示 "**添加自己的本地应用程序**" 页。

1. 如果尚未安装应用程序代理连接器，系统将提示你安装它。 选择 "**下载应用程序代理连接器**"，下载并安装连接器。

1. 安装应用程序代理连接器后，请在 "**添加自己的本地应用程序**" 页上：

   1. 在 "**名称**" 旁边，输入 " *SecretAPI*"。

   1. 在 "**内部 Url**" 旁边，输入你的 intranet 中用于访问 API 的 Url。

   1. 请确保**预身份验证**设置为**Azure Active Directory**。

   1. 选择页面顶部的 "**添加**"，并等待创建应用。

   ![添加 API 应用](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. 在 "**企业应用程序-所有应用程序**" 页上，选择**SecretAPI**应用程序。

1. 在**SecretAPI**页上，选择左侧导航栏中的 "**属性**"。

1. 您不希望在**MyApps**面板中向最终用户提供 api，因此请在 "**属性**" 页的底部将 "**对用户可见**" 设置为 "**否**"，然后选择 "**保存**"。

   ![对用户不可见](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

已通过 Azure AD 应用程序代理发布了 web API。 现在，添加可访问应用的用户。

1. 在**SecretAPI**页上，选择左侧导航栏中的 "**用户和组**"。

1. 在 "**用户和组**" 页上，选择 "**添加用户**"。

1. 在 "**添加分配**" 页上，选择 "**用户和组**"。

1. 在 "**用户和组**" 页上，搜索并选择可以访问应用的用户，至少包括你自己的用户。 选择 "所有用户" 后，选择 "**选择**"。

   ![选择并分配用户](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. 返回 "**添加分配**" 页，选择 "**分配**"。

> [!NOTE]
> 使用集成 Windows 身份验证的 Api 可能需要[额外的步骤](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>注册本机应用并授予对 API 的访问权限

本机应用是开发用于在特定平台或设备上使用的程序。 在本机应用可以连接和访问 API 之前，必须在 Azure AD 中注册它。 以下步骤演示如何注册本机应用，并向其授予对通过应用程序代理发布的 web API 的访问权限。

注册 AppProxyNativeAppSample 本机应用程序：

1. 在 "Azure Active Directory**概述**" 页上，选择 "**应用注册**"，然后在 "**应用注册**" 窗格的顶部，选择 "**新注册**"。

1. 在 "**注册应用程序**" 页上：

   1. 在 "**名称**" 下输入*AppProxyNativeAppSample*。

   1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。  

   1. 在 "**重定向 URL**" 下，删除并选择 "**公用客户端（移动 & 桌面）**"，然后输入*https：\//appproxynativeapp*。

   1. 选择 "**注册**"，并等待应用成功注册。

      ![新建应用程序注册](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

你现在已在 Azure Active Directory 中注册了 AppProxyNativeAppSample 应用。 若要为本机应用程序授予对 SecretAPI web API 的访问权限：

1. 在 Azure Active Directory**概述** > **应用注册**"页上，选择**AppProxyNativeAppSample**应用。

1. 在 " **AppProxyNativeAppSample** " 页上，选择左侧导航栏中的 " **API 权限**"。

1. 在 " **API 权限**" 页上，选择 "**添加权限**"。

1. 在第一个 "**请求 api 权限**" 页上，选择 "**我的组织使用的 api** " 选项卡，然后搜索并选择 " **SecretAPI**"。

1. 在 "下一**请求 API 权限**" 页上，选中 " **user_impersonation**" 旁边的复选框，然后选择 "**添加权限**"。

    ![选择 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. 返回 " **API 权限**" 页，可以选择 "**为 Contoso 授予管理员许可**"，以防止其他用户必须单独同意该应用。

## <a name="configure-the-native-app-code"></a>配置本机应用代码

最后一步是配置本机应用。 Nativeclient-ios 示例应用程序中*Form1.cs*文件的以下代码片段使 ADAL 库获取用于请求 API 调用的令牌，并将其作为持有者附加到应用标头。

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

若要配置本机应用以连接到 Azure Active Directory 并调用 API 应用代理，请在 Nativeclient-ios 示例应用的*app.config*文件中更新占位符值，并将其值从 Azure AD：

- 将**目录（租户） ID**粘贴到`<add key="ida:Tenant" value="" />`字段中。 你可以从你的应用程序的 "**概述**" 页中查找并复制此值（GUID）。

- 将 AppProxyNativeAppSample**应用程序（客户端） ID**粘贴`<add key="ida:ClientId" value="" />`到字段中。 可以从 "AppProxyNativeAppSample**概述**" 页中查找并复制此值（GUID）。

- 将 AppProxyNativeAppSample**重定向 URI**粘贴到`<add key="ida:RedirectUri" value="" />`字段中。 你可以从 AppProxyNativeAppSample **Authentication** （URI）页中查找并复制此值（URI）。

- 将 SecretAPI**应用程序 ID URI**粘贴到`<add key="todo:TodoListResourceId" value="" />`字段中。 可以**从 SecretAPI 中**查找并复制此值（URI）。

- 将 SecretAPI**主页 URL**粘贴到`<add key="todo:TodoListBaseAddress" value="" />`字段中。 可以从 SecretAPI**品牌**页中查找并复制此值（URL）。

配置参数后，生成并运行本机应用。 当你选择 "**登录**" 按钮时，应用程序允许你登录，然后显示一条成功屏幕以确认它已成功连接到 SecretAPI。

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)
- [快速入门：配置客户端应用程序以访问 Web API](../develop/quickstart-configure-app-access-web-apis.md)
- [如何允许本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
