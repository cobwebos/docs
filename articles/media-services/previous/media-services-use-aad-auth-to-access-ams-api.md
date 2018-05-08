---
title: 通过 Azure Active Directory 身份验证访问 Azure 媒体服务 API | Microsoft Docs
description: 了解使用 Azure Active Directory (Azure AD) 验证对 Azure 媒体服务 API 的访问的相关概念和步骤。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: a7f20c22b39458134d3dcd42b7e13860c03bad58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>通过 Azure AD 身份验证访问 Azure 媒体服务 API
 
Azure 媒体服务 API 是 RESTful API。 可用于通过 REST API 或可用的客户端 SDK 对媒体资源执行操作。 Azure 媒体服务提供了适用于 Microsoft.NET 的媒体服务客户端 SDK。 若要有权访问媒体服务资源和媒体服务 API，必须先进行身份验证。 

媒体服务支持 [Azure Active Directory (Azure AD) 身份验证](../../active-directory/active-directory-whatis.md)。 Azure 媒体 REST 服务要求，发出 REST API 请求的用户或应用程序必须具有参与者或所有者角色，才能访问资源。 有关详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../../role-based-access-control/overview.md)。  

> [!IMPORTANT]
> 目前，媒体服务支持 Azure 访问控制服务身份验证模型。 不过，访问控制授权将于 2018 年 6 月 1 日弃用。 建议尽快迁移到 Azure AD 身份验证模型。

本文档概述了如何使用 REST 或 .NET API 访问媒体服务 API。

## <a name="access-control"></a>访问控制

若要让 Azure 媒体 REST 请求成功，对于尝试访问的媒体服务帐户，调用用户必须具有参与者或所有者角色。  
只有具有所有者角色的用户，才能向新用户或应用程序授予媒体资源（帐户）访问权限。 参与者角色只能访问媒体资源。
未授权的请求失败，状态代码为 401。 如果看到此错误代码，请检查用户是否具有针对用户媒体服务帐户分配的参与者或所有者角色。 可以在 Azure 门户中检查角色分配。 搜索媒体帐户，再单击“访问控制”选项卡。 

![“访问控制”选项卡](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>身份验证类型 
 
将 Azure AD 身份验证与 Azure 媒体服务结合使用时，可以选择下列两个身份验证选项：

- **用户身份验证**： 验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应先提示用户输入用户凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 
- **服务主体身份验证**： 验证服务。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用程序。 例如，Web 应用程序、函数应用程序、逻辑应用程序、API 和微服务。

### <a name="user-authentication"></a>用户身份验证 

应使用用户身份验证方法的应用程序是负责管理或监视的本机应用程序：移动应用、Windows 应用程序和控制台应用程序。 若要在以下情形之一让用户与服务进行交互，此类解决方案十分有用：

- 编码作业的监视仪表板。
- 实时传送视频流的监视仪表板。
- 面向桌面或移动用户的管理应用程序，用于管理媒体服务帐户中的资源。

> [!NOTE]
> 这种身份验证方法不得用于面向使用者的应用程序。 

本机应用程序必须先从 Azure AD 获取访问令牌，再使用此令牌对媒体服务 REST API 发出 HTTP 请求。 将访问令牌添加到请求头。 

下图展示了典型的交互式应用程序身份验证流： 

![本机应用程序图](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

在上图中，数字表示按时间顺序的请求流。

> [!NOTE]
> 使用用户身份验证方法时，所有应用程序共用同一（默认）本机应用程序客户端 ID 和本机应用程序重定向 URI。 

1. 提示用户输入凭据。
2. 请求获取包含以下参数的 Azure AD 访问令牌：  

    * Azure AD 租户终结点。

        可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名之上。
    * 媒体服务资源 URI。 

        对于同一 Azure 环境中的媒体服务帐户，此 URI 相同（例如，https://rest.media.azure.net)。

    * 媒体服务（本机）应用程序客户端 ID。
    * 媒体服务（本机）应用程序重定向 URI。
    * REST 媒体服务的资源 URI。
        
        该 URI 表示 REST API 终结点（例如，https://test03.restv2.westus.media.azure.net/api/)。

    若要获取这些参数的值，请参阅[使用 Azure 门户访问 Azure AD 身份验证设置](media-services-portal-get-started-with-aad.md)（使用用户身份验证选项）。

3. Azure AD 访问令牌发送到客户端。
4. 客户端使用 Azure AD 访问令牌向 Azure 媒体 REST API 发送请求。
5. 客户端获取媒体服务返回的数据。

若要了解如何通过 Azure AD 身份验证使用媒体服务 .NET 客户端 SDK 与 REST 请求进行通信，请参阅[通过 Azure AD 身份验证使用 .NET 访问媒体服务 API](media-services-dotnet-get-started-with-aad.md)。 

如果未使用媒体服务 .NET 客户端 SDK，必须使用第 2 步所述参数，手动创建 Azure AD 访问令牌请求。 有关详细信息，请参阅[如何使用 Azure AD 身份验证库获取 Azure AD 令牌](../../active-directory/develop/active-directory-authentication-libraries.md)。

### <a name="service-principal-authentication"></a>服务主体身份验证

常常使用这种身份验证方法的应用程序是运行中间层服务和计划作业的应用程序：Web 应用程序、函数应用程序、逻辑应用程序、API 或微服务。 这种身份验证方法还适用于交互式应用程序，即可能需要使用服务帐户管理资源的应用程序。

使用服务主体身份验证方法生成使用者方案时，通常在中间层（通过一些 API）处理身份验证，而不直接在移动或桌面应用程序中处理。 

若要使用这种方法，请在它自己的租户中创建 Azure AD 应用程序和服务主体。 创建应用程序后，向应用程序授予对媒体服务帐户的参与者或所有者角色访问权限。 为此，可以使用 Azure 门户、Azure CLI 或 PowerShell 脚本。 也可以使用现有 Azure AD 应用程序。 可以[在 Azure 门户中](media-services-portal-get-started-with-aad.md)注册和管理 Azure AD 应用程序和服务主体。 也可以使用 [Azure CLI 2.0](media-services-use-aad-auth-to-access-ams-api.md) 或 [PowerShell](media-services-powershell-create-and-configure-aad-app.md) 执行此操作。 

![中间层应用](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

创建 Azure AD 应用程序后，将会获得下列设置的值。 需要使用这些值进行身份验证：

- 客户端 ID 
- 客户端机密 

在上图中，数字表示按时间顺序的请求流：
    
1. 中间层应用（Web API 或 Web 应用程序）请求获取包含以下参数的 Azure AD 访问令牌：  

    * Azure AD 租户终结点。

        可以在 Azure 门户中检索租户信息。 将光标悬停在右上角的登录用户名之上。
    * 媒体服务资源 URI。 

        对于位于同一 Azure 环境中的媒体服务帐户，此 URI 相同（例如，https://rest.media.azure.net)。

    * REST 媒体服务的资源 URI。

        该 URI 表示 REST API 终结点（例如，https://test03.restv2.westus.media.azure.net/api/)。

    * Azure AD 应用程序值：客户端 ID和客户端密码。
    
    若要获取这些参数的值，请参阅[使用 Azure 门户访问 Azure AD 身份验证设置](media-services-portal-get-started-with-aad.md)（使用服务主体身份验证选项）。

2. Azure AD 访问令牌发送到中间层。
4. 中间层使用 Azure AD 令牌向 Azure 媒体 REST API 发送请求。
5. 中间层获取媒体服务返回的数据。

若要详细了解如何通过 Azure AD 身份验证使用媒体服务 .NET 客户端 SDK 与 REST 请求进行通信，请参阅[通过 Azure AD 身份验证使用 .NET 访问媒体服务 API](media-services-dotnet-get-started-with-aad.md)。 

如果未使用媒体服务 .NET 客户端 SDK，必须使用第 1 步所述参数，手动创建 Azure AD 访问令牌请求。 有关详细信息，请参阅[如何使用 Azure AD 身份验证库获取 Azure AD 令牌](../../active-directory/develop/active-directory-authentication-libraries.md)。

## <a name="troubleshooting"></a>故障排除

异常：“远程服务器返回了错误: (401) 未授权。”

解决方案：若要让媒体服务 REST 请求成功，对于尝试访问的媒体服务帐户，调用用户必须具有参与者或所有者角色。 有关详细信息，请参阅[访问控制](media-services-use-aad-auth-to-access-ams-api.md#access-control)部分。

## <a name="resources"></a>资源

下面的文章概述了 Azure AD 身份验证概念： 

- [Azure AD 解决的身份验证方案](../../active-directory/develop/active-directory-authentication-scenarios.md#basics-of-authentication-in-azure-ad)
- [在 Azure AD 中添加、更新或删除应用程序](../../active-directory/develop/active-directory-integrating-applications.md)
- [使用 PowerShell 配置和管理基于角色的访问控制](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 门户[访问 Azure AD 身份验证，以使用 Azure 媒体服务 API](media-services-portal-get-started-with-aad.md)。
* 通过 Azure AD 身份验证[使用 .NET 访问Azure 媒体服务 API](media-services-dotnet-get-started-with-aad.md)。

