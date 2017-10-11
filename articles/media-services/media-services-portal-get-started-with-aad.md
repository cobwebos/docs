---
title: "通过 Azure 门户开始使用 Azure AD 身份验证 | Microsoft Docs"
description: "了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 829e0759f8aeb8758f6b8895b88b60b66ffb22ed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure AD 身份验证

了解如何使用 Azure 门户访问 Azure Active Directory (Azure AD) 身份验证设置，以便使用 Azure 媒体服务 API。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，请从 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)入手。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。
- 务必查看[通过 Azure AD 身份验证访问 Azure 媒体服务 API 概述](media-services-use-aad-auth-to-access-ams-api.md)。 

将 Azure AD 身份验证与 Azure 媒体服务结合使用时，可以选择下列两个身份验证选项：

- **用户身份验证**： 验证使用应用程序与媒体服务资源进行交互的用户。 交互式应用程序应先提示用户输入凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。 
- **服务主体身份验证**： 验证服务。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用程序：Web 应用程序、函数应用程序、逻辑应用程序、API 或微服务。

> [!IMPORTANT]
> 目前，媒体服务支持 Azure 访问控制服务身份验证模型。 不过，访问控制授权将于 2018 年 6 月 1 日弃用。 建议尽快迁移到 Azure AD 身份验证模型。

## <a name="select-the-authentication-method"></a>选择身份验证方法

1. 在 [Azure 门户](https://portal.azure.com/)中，选择媒体服务帐户。
2. 选择与媒体服务 API 的连接方式。

    ![用于选择连接方法的页面](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>用户身份验证

若要使用用户身份验证选项连接媒体服务 API，客户端应用程序必须请求获取包含以下参数的 Azure AD 令牌：  

* Azure AD 租户终结点
* 媒体服务资源 URI
* 媒体服务（本机）应用程序客户端 ID 
* 媒体服务（本机）应用程序重定向 URI 
* REST 媒体服务的资源 URI

可以在“通过用户身份验证连接媒体服务 API”页上获取这些参数的值。 

![“通过用户身份验证连接媒体服务 API”页](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

如果使用媒体服务 Microsoft.NET SDK 连接媒体服务 API，可以在 SDK 中获取相应值。 有关详细信息，请参阅[通过 Azure AD 身份验证使用 .NET 访问 Azure 媒体服务 API](media-services-dotnet-get-started-with-aad.md)。

如果未使用媒体服务 .NET 客户端 SDK，必须使用上述参数，手动创建 Azure AD 访问令牌请求。 有关详细信息，请参阅[如何使用 Azure AD 身份验证库获取 Azure AD 令牌](../active-directory/develop/active-directory-authentication-libraries.md)。

## <a name="service-principal-authentication"></a>服务主体身份验证

若要使用服务主体选项连接媒体服务 API，中间层应用程序（Web API 或 Web 应用程序）必须请求获取包含以下参数的 Azure AD 令牌：  

* Azure AD 租户终结点
* 媒体服务资源 URI 
* REST 媒体服务的资源 URI
* Azure AD 应用程序值：客户端 ID和客户端密钥

可以在“通过服务主体选项连接媒体服务 API”页上获取这些参数的值。 在此页上，可以新建 Azure AD 应用程序，也可以选择现有应用程序。 选择 Azure AD 应用程序后，可以获取客户端 ID（应用程序 ID），并生成客户端密码（密钥）值。 

![“通过服务主体选项连接媒体服务 API”页](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

在打开的“服务主体”边栏选项卡上，选择首个满足以下条件的 Azure AD 应用程序：

- 已注册的 Azure AD 应用程序。
- 对帐户拥有基于参与者或所有者角色的访问控制权限。

创建或选择 Azure AD 应用程序后，可以创建并复制客户端密码（密钥）和客户端 ID（应用程序 ID）。 在此方案中，必须有客户端密码和客户端 ID，才能获取访问令牌。

如果无权在域中创建 Azure AD 应用程序，在边栏选项卡上看到的就不是 Azure AD 应用程序控件，而是警告消息。

如果使用媒体服务 .NET SDK 连接媒体服务 API，请参阅[通过 Azure AD 身份验证使用 .NET 访问 Azure 媒体服务 API](media-services-dotnet-get-started-with-aad.md)。

如果未使用媒体服务 .NET 客户端 SDK，必须使用上述参数，手动创建 Azure AD 访问令牌请求。 有关详细信息，请参阅[如何使用 Azure AD 身份验证库获取 Azure AD 令牌](../active-directory/develop/active-directory-authentication-libraries.md)。

### <a name="get-the-client-id-and-client-secret"></a>获取客户端 ID 和客户端密码

选择现有 Azure AD 应用程序或选择新建应用程序后，将看到以下按钮：

![“管理权限”按钮和“管理应用程序”按钮](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

若要打开 Azure AD 应用程序边栏选项卡，请单击“管理应用程序”。 在“管理应用程序”边栏选项卡上，可以获取应用程序的客户端 ID（应用程序 ID）。 若要生成客户端密码（密钥），请选择“密钥”。

![“管理应用程序”边栏选项卡上的“密钥”选项](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>管理权限和应用程序

选择 Azure AD 应用程序后，可以管理应用程序和权限。 若要将 Azure AD 应用程序设置为访问其他应用程序，请单击“管理权限”。 若要执行管理任务（如更改密钥和回复 URL）或编辑应用程序清单，请单击“管理应用程序”。

### <a name="edit-the-apps-settings-or-manifest"></a>编辑应用程序设置或清单

若要编辑应用程序设置或清单，请单击“管理应用程序”。

![“管理应用程序”页](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
