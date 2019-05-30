---
title: 在国家/地区云的 Microsoft 标识平台中使用 Microsoft 身份验证库 (MSAL)
description: Microsoft 身份验证库 (MSAL) 使应用程序开发人员若要获取的令牌，以便调用受保护的 web Api。 这些 web Api 可以是 Microsoft Graph、 其他 Microsoft Api、 合作伙伴 web Api 或你自己的 web API。 MSAL 支持多个应用程序体系结构和平台。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235271"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>在国家/地区云环境中使用 MSAL

[国家/地区云](authentication-national-cloud.md)是物理隔离的 Azure 实例。 Azure 的这些区域帮助确保数据驻留、 主权和符合性要求将遵循在地理边界内。

除了 Microsoft 全球云，Microsoft 身份验证库 (MSAL) 使应用程序开发人员国家/地区云中，若要获取令牌以进行身份验证并调用受保护的 web Api。 这些 web Api 可以是 Microsoft Graph 或其他 Microsoft Api。

全局云，包括 Azure Active Directory (Azure AD) 被部署在以下国家/地区云：  

- Azure Government 
- Azure 中国世纪互联
- Azure 德国

本指南演示如何登录以进行工作和学校帐户、 获取访问令牌，以及调用 Microsoft Graph API [Azure 政府版云中](https://azure.microsoft.com/global-infrastructure/government/)环境。

## <a name="prerequisites"></a>必备组件

在开始之前，请确保满足这些先决条件。

### <a name="choose-the-appropriate-identities"></a>选择相应的标识

[Azure 政府版](https://docs.microsoft.com/azure/azure-government/)应用程序可以使用 Azure AD 政府标识和 Azure AD 公共标识用户进行身份验证。 因为您可以使用任何这些标识，您需要决定为自己的方案应选择哪个颁发机构终结点：

- Azure AD 公共：如果你的组织已具有到支持 Office 365 （公共或 gcc 高级版） 的 Azure AD 公共租户或另一个应用程序通常使用。
- Azure AD 政府：如果你的组织已具有 Azure AD 政府租户支持 Office 365 （Gcc 或 DoD） 或在 Azure AD 政府版中创建新租户，通常使用。

确定后，一个特殊的考虑事项是在其中执行的应用注册。 如果为你的 Azure 政府版应用程序选择 Azure AD 公共标识，则必须在公共 Azure AD 租户中注册应用程序。

### <a name="get-an-azure-government-subscription"></a>获取 Azure 政府版订阅

若要获取 Azure 政府版订阅，请参阅[管理和连接到你的订阅在 Azure 政府版中](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)。

如果还没有 Azure 政府版订阅，创建[免费帐户](https://azure.microsoft.com/global-infrastructure/government/request/)在开始之前。

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

1. 登录到 [Azure 门户](https://portal.azure.us/)。
    
   若要查找其他国家/地区云 Azure 门户终结点，请参阅[应用注册终结点](authentication-national-cloud.md#app-registration-endpoints)。

1. 如果你的帐户有权访问多个租户，在右上角中，选择你的帐户和将门户会话设置为所需的 Azure AD 租户。
1. 转到[应用注册](https://aka.ms/ra/ff)开发人员的 Microsoft 标识平台上的页。
1. “注册应用程序”页显示后，请输入应用程序的名称。 
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户”。  
1. 在中**重定向 URI**部分中，选择**Web**平台和应用程序的 URL 的值根据您的 web 服务器设置。 有关如何设置和获取 Visual Studio 和节点中的重定向 URL，请参阅有关说明下, 一步部分。
1. 选择“注册”  。
1. 在应用的“概述”页上，记下“应用程序(客户端) ID”值。  
1. 本教程要求你启用[隐式授权流](v2-oauth2-implicit-grant-flow.md)。 在已注册的应用程序的左侧窗格中，选择**身份验证**。
1. 在中**高级设置**下**隐式授权**，选择**ID 令牌**并**访问令牌**复选框。 ID 令牌和访问令牌需要，因为此应用程序需要在用户登录并调用一个 API。
1. 选择“保存”。 

### <a name="step-2--set-up-your-web-server-or-project"></a>步骤 2：设置您的 web 服务器或项目

- [下载项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)对于本地 web 服务器，例如节点。

  或

- [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。

然后跳到[配置 JavaScript SPA](#step-4-configure-your-javascript-spa)运行它之前配置的代码示例。

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>步骤 3：使用 Microsoft 身份验证库来使用户登录

按照步骤[JavaScript 教程](tutorial-v2-javascript-spa.md#create-your-project)若要创建项目，并将与登录用户的 MSAL 集成。

### <a name="step-4-configure-your-javascript-spa"></a>步骤 4：配置 JavaScript SPA

在设置项目期间创建的 `index.html` 文件中，添加应用程序注册信息。 在 `index.html` 文件正文中的 `<script></script>` 标记顶部添加以下代码：

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

在该代码：

- `Enter_the_Application_Id_here` 是**应用程序 （客户端） ID**你注册的应用程序的值。
- `Enter_the_Tenant_Info_Here` 设置为以下选项之一：
    - 如果你的应用程序支持**此组织的目录中的帐户**，此值替换为租户 ID 或租户名称 (例如，contoso.microsoft.com)。
    - 如果你的应用程序支持**任何组织的目录中的帐户**，将此值替换为`organizations`。
    
    若要查找所有国家/地区云身份验证终结点，请参阅[Azure AD 身份验证终结点](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)。

    > [!NOTE]
    > 在国家云中不支持个人 Microsoft 帐户。
  
- `graphEndpoint` 是用于美国政府的 Microsoft 云的 Microsoft Graph 终结点。

   若要查找所有国家/地区云的 Microsoft Graph 终结点，请参阅[国家/地区云中的 Microsoft Graph 终结点](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)。

## <a name="net"></a>.NET

可以使用 MSAL.NET 用户登录、 获取令牌，并在国家/地区云中调用 Microsoft Graph API。

以下教程演示如何生成.NET Core 2.2 MVC Web 应用。 该应用使用 OpenID Connect 与所在国家/地区云的组织中的工作和学校帐户登录用户。

- 若要在用户登录和获取令牌，请按照[本教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)。
- 若要调用 Microsoft Graph API，请按照[本教程](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure 中国世纪互联](https://docs.microsoft.com/azure/china/)
- [Azure 德国](https://docs.microsoft.com/azure/germany/)