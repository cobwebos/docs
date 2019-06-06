---
title: 访问本地与 Azure AD 应用程序代理的 Api
description: Azure Active Directory 的应用程序代理允许安全地访问 Api 的本机应用和业务逻辑在本地托管或云 Vm 上。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 47f6678f8d18d734176d964f18a6febecea957ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481409"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>安全地访问本地 Api 使用 Azure AD 应用程序代理

您可能具有业务逻辑在内部运行的 Api，或在云中的虚拟机上托管。 本机 Android、 iOS、 Mac 或 Windows 应用程序需要与要使用的数据，或提供用户交互的 API 终结点进行交互。 Azure AD 应用程序代理和[Azure Active Directory 身份验证库 (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries)让本机应用程序安全地访问本地 Api。 Azure Active Directory 应用程序代理是一个更快、 更安全的解决方案，比打开防火墙端口和控制身份验证和授权在应用层。 

本文将引导您完成设置过程用于本机应用程序可以访问 web API 服务托管的 Azure AD 应用程序代理解决方案。 

## <a name="overview"></a>概述

下图显示了传统的方法来发布的本地 Api。 这种方法需要打开传入端口 80 和 443。

![传统的 API 访问权限](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

下图显示了如何使用 Azure AD 应用程序代理可以安全地将 Api 发布而无需打开任何传入的端口：

![Azure AD 应用程序代理 API 访问权限](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 应用程序代理窗体的主干的解决方案，充当 API 访问权限的公共终结点和提供身份验证和授权。 可以从大量的平台访问你的 Api，通过使用[ADAL](/azure/active-directory/develop/active-directory-authentication-libraries)库。 

由于 Azure AD 每生成 Azure AD 应用程序代理身份验证和授权，可以使用 Azure AD 条件访问以确保仅受信任的设备可以访问通过应用程序代理发布的 Api。 使用 Azure AD Join 或适用于桌面，Azure AD 混合加入和 Intune 托管的设备。 此外可以充分利用 Azure 多重身份验证等 Azure Active Directory Premium 功能和机器学习提供支持的安全性[Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)。

## <a name="prerequisites"></a>必备组件

若要执行本演练，需要：

- 对 Azure 目录，可创建和注册应用程序的帐户的管理员访问权限
- 示例 web API 和本机客户端应用程序程序 [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>发布 API 的应用程序代理

若要发布你通过应用程序代理在 intranet 外部 API，请执行相同的模式与发布的 web 应用。 有关详细信息，请参阅[教程：在 Azure Active Directory 中添加用于通过应用程序代理远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

若要发布应用程序代理通过 SecretAPI web API:

1. 生成并发布示例 SecretAPI 项目作为本地计算机或 intranet 上的 ASP.NET web 应用。 请确保可以访问本地 web 应用。 
   
1. 在中[Azure 门户](https://portal.azure.com)，选择**Azure Active Directory**左侧导航窗格中。 然后，在**概述**页上，选择**企业应用程序**。
   
1. 在顶部**企业应用程序-所有应用程序**页上，选择**新的应用程序**。
   
1. 上**添加应用程序**页面上，在**添加你自己的应用**，选择**本地应用程序**。 
   
1. 如果您没有安装应用程序代理连接器，将提示您进行安装。 选择**下载应用程序代理连接器**下载并安装连接器。 
   
1. 在安装应用程序代理连接器后,**添加自己的本地应用程序**页：
   
   1. 输入*SecretAPI*旁边**名称**。
      
   1. 输入用于访问在 intranet 中的 API 旁边的 URL**内部 Url**。 
      
   1. 请确保**预身份验证**设置为**Azure Active Directory**。 
      
   1. 选择**添加**顶部的页上，并等待要创建的应用。
   
   ![添加 API 应用](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. 上**企业应用程序-所有应用程序**页上，选择**SecretAPI**应用。 
   
1. 上**SecretAPI-概述**页上，选择**属性**左侧导航窗格中。
   
1. 不想要向最终用户提供的 Api **MyApps**面板中，因此将**对用户可见**到**否**底部**属性**页上，然后依次**保存**。
   
   ![对用户不可见](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
已发布 web API 通过 Azure AD 应用程序代理。 现在，添加有权访问该应用程序的用户。 

1. 上**SecretAPI-概述**页上，选择**用户和组**左侧导航窗格中。
   
1. 上**用户和组**页上，选择**添加用户**。  
   
1. 上**添加分配**页上，选择**用户和组**。 
   
1. 上**用户和组**页上，搜索并选择可以访问应用程序中，至少包括您自己的用户。 选择后的所有用户，请选择**选择**。 
   
   ![选择并分配用户](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. 重新**添加分配**页上，选择**分配**。 

> [!NOTE]
> 使用集成 Windows 身份验证的 Api 可能需要[额外的步骤](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)。

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>注册本机应用程序和 api 授予访问权限

本机应用程序是为在特定平台或设备上使用而开发的程序。 本机应用程序可以连接并访问 API 之前，必须在 Azure AD 中注册它。 以下步骤说明如何注册的本机应用，并授予它对 web API 通过应用程序代理发布的访问权限。

若要注册 AppProxyNativeAppSample 本机应用：

1. 在 Azure Active Directory**概述**页上，选择**应用注册**，并在顶部**应用注册**窗格中，选择**新注册**.
   
1. 上**注册应用程序**页：
   
   1. 下**名称**，输入*AppProxyNativeAppSample*。 
      
   1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。   
      
   1. 下**重定向 URL**、 下拉列表中，选择**公共客户端 （移动和桌面）** ，然后输入*https:\//appproxynativeapp*。 
      
   1. 选择**注册**，并等待成功注册应用。 
      
      ![新建应用程序注册](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
现在已在 Azure Active Directory 中注册 AppProxyNativeAppSample 应用。 若要对本机应用授予访问到 SecretAPI web API:

1. 在 Azure Active Directory**概述** > **应用注册**页上，选择**AppProxyNativeAppSample**应用。 
   
1. 上**AppProxyNativeAppSample**页上，选择**API 权限**左侧导航窗格中。 
   
1. 上**API 的权限**页上，选择**添加权限**。
   
1. 在第一天**请求 API 的权限**页上，选择**我的组织使用的 Api**选项卡，然后搜索并选择**SecretAPI**。 
   
1. 下一步**请求 API 的权限**页上，选中复选框旁边**user_impersonation**，然后选择**添加权限**。 
   
    ![选择 API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. 重新**API 的权限**页上，可以选择**授予公司为 Contoso 的管理员同意**以防止其他用户无需单独许可的应用程序。 

## <a name="configure-the-native-app-code"></a>配置本机应用程序代码

最后一步是配置本机应用。 中的以下代码片段*Form1.cs* NativeClient 示例应用程序中的文件导致的 ADAL 库来获得请求的 API 调用，令牌，并将其作为持有者附加到应用程序标头。 
   
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
   
若要配置本机应用程序连接到 Azure Active Directory 和调用 API 应用程序代理，请更新中的占位符值*App.config* NativeClient 示例应用程序与 Azure AD 中的值的文件： 

- 粘贴**目录 （租户） ID**中`<add key="ida:Tenant" value="" />`字段。 您可以查找并复制此值 (GUID) 从**概述**您的应用程序的任何一个的页。 
  
- 粘贴 AppProxyNativeAppSample**应用程序 （客户端） ID**中`<add key="ida:ClientId" value="" />`字段。 您可以查找并复制此值 (GUID) 从 AppProxyNativeAppSample**概述**页。
  
- 粘贴 AppProxyNativeAppSample**重定向 URI**中`<add key="ida:RedirectUri" value="" />`字段。 您可以查找并复制此值 (URI) 从 AppProxyNativeAppSample**身份验证**页。 
  
- 粘贴 SecretAPI**应用程序 ID URI**中`<add key="todo:TodoListResourceId" value="" />`字段。 您可以查找并复制此值 (URI) 从 SecretAPI**公开一个 API**页。
  
- 粘贴 SecretAPI **Home Page URL**中`<add key="todo:TodoListBaseAddress" value="" />`字段。 您可以查找并复制此值 (URL) 从 SecretAPI**品牌**页。

配置参数后，生成并运行的本机应用。 当选择**Sign In**按钮时，此应用程序允许你登录，并显示成功屏幕以确认它已成功连接到 SecretAPI。

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure Active Directory 中添加用于通过应用程序代理远程访问的本地应用程序](application-proxy-add-on-premises-application.md)
- [快速入门：配置客户端应用程序以访问 web Api](../develop/quickstart-configure-app-access-web-apis.md)
- [如何使本机客户端应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
