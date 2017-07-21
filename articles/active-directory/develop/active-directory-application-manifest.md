---
title: "了解 Azure Active Directory 应用程序清单 | Microsoft Docs"
description: "详细介绍 Azure Active Directory 应用程序清单，该清单表示 Azure AD 租户中的应用程序标识配置，并方便实现 OAuth 授权、许可体验和其他功能。"
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.translationtype: Human Translation
ms.sourcegitcommit: 7d6525f4614c6301f0ddb621b0483da70842a71b
ms.openlocfilehash: 2dc166a346c58d43e9ed60332f47619c1de89816
ms.contentlocale: zh-cn
ms.lasthandoff: 02/11/2017

---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>了解 Azure Active Directory 应用程序清单
与 Azure Active Directory (AD) 集成的应用程序必须向 Azure AD 租户注册，提供应用程序的持久性标识配置。 在运行时查阅此配置，启用允许应用程序通过 Azure AD 外部和代理身份验证/授权的方案。 有关 Azure AD 应用程序模型的详细信息，请参阅[添加、更新和删除应用程序][ADD-UPD-RMV-APP]一文。

## <a name="updating-an-applications-identity-configuration"></a>更新应用程序的标识配置
实际上有多个可用的选项可以更新应用程序的标识配置属性，这些选项因功能与难度而有所不同，包括：

* **[Azure 门户][AZURE-PORTAL] 的 Web 用户界面**可让你更新应用程序的最常见属性。 这是更新应用程序属性最快且最不容易出错的方法，但无法像下面两种方法一样提供对所有属性的完全访问权限。
* 对于需要在其中更新 Azure 经典门户中未公开的属性的更高级方案，可以修改**应用程序清单**。 这是本文的重点，将在下一部分中开始详细讨论。
* 还可以**编写使用[图形 API][GRAPH-API] 的应用程序**来更新应用程序，这是最费力的方法。 如果要编写管理软件或需要自动定期更新应用程序属性，这可能是个不错的选择。

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>使用应用程序清单更新应用程序的标识配置
借助 [Azure 门户][AZURE-PORTAL]，可以使用内联清单编辑器通过更新应用程序清单来管理应用程序的标识配置。 还可以下载应用程序清单并将其作为 JSON 文件上传。 不会将实际的文件存储在目录中。 应用程序清单只是 Azure AD 图形 API 应用程序实体上的 HTTP GET 操作，上载是应用程序实体上的 HTTP PATCH 操作。

因此，若要了解应用程序清单的格式和属性，需要参考图形 API [应用程序实体][APPLICATION-ENTITY]文档。 可通过应用程序清单上载执行的更新示例包括：

* **声明 Web API 所公开的权限范围 (oauth2Permissions)**。 有关使用 oauth2Permissions 委派权限范围实现用户模拟的信息，请参阅[将应用程序与 Azure Active Directory 集成][INTEGRATING-APPLICATIONS-AAD]中的“向其他应用程序公开 Web API”主题。 如前所述，图形 API [Entity and Complex Type][APPLICATION-ENTITY]（实体和复杂类型）参考文章中介绍了应用程序实体属性，包括属于 [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 类型集合的 oauth2Permissions 属性。
* **声明应用公开的应用程序角色 (appRoles)**。 应用程序实体的 appRoles 属性是 [AppRole][APPLICATION-ENTITY-APP-ROLE] 类型的集合。 请参阅[使用 Azure AD 在云应用程序中执行基于角色的访问控制][RBAC-CLOUD-APPS-AZUREAD]一文获取实现示例。
* **声明已知的客户端应用程序 (knownClientApplications)**，可让你以逻辑方式将指定客户端应用程序的许可绑定到资源/Web API。
* **请求 Azure AD 对登录用户发出组成员资格声明** (groupMembershipClaims)。  还可配置为发出有关用户目录角色成员资格的声明。 请参阅[使用 AD 组在云应用程序中执行授权][AAD-GROUPS-FOR-AUTHORIZATION]一文获取实现示例。
* **允许应用程序支持 OAuth 2.0 隐式授权**流 (oauth2AllowImplicitFlow)。 这种类型的授权流可用于嵌入式 JavaScript 网页或单页应用程序 (SPA)。 有关隐式授权许可的详细信息，请参阅 [Understanding the OAuth2 implicit grant flow in Azure Active Directory][IMPLICIT-GRANT]（了解 Azure Active Directory 中的 OAuth2 隐式授予流）。
* **允许使用 X509 证书作为机密密钥** (keyCredentials)。 有关实现示例，请参阅文章 [Build service and daemon apps in Office 365][O365-SERVICE-DAEMON-APPS]（在 Office 365 中构建服务和守护程序应用）和 [Developer’s guide to auth with Azure Resource Manager API][DEV-GUIDE-TO-AUTH-WITH-ARM]（使用 Azure Resource Manager API 进行身份验证的开发人员指南）。
* 为应用程序（标识符 URI[]）**添加新的应用 ID URI**。 应用 ID URI 用于唯一标识其 Azure AD 租户中的应用程序（或是通过已验证的自定义域限定多个租户方案时跨多个 Azure AD 租户中的应用程序）。 在请求资源应用程序的权限，或获取资源应用程序的访问令牌时使用应用程序 ID URI。 更新此元素时，相应的服务主体的 servicePrincipalNames[] 集合将做出同样的更新，该集合位于应用程序的主租户中。

使用应用程序清单还能很好地跟踪应用程序注册状态。 由于它可以 JSON 格式提供，因此文件表示形式可以签入源代码管理，以及应用程序的源代码。

## <a name="step-by-step-example"></a>分步示例
现在，让我们逐步了解通过应用程序清单更新应用程序标识配置的步骤。 我们将重点演示上述示例之一，介绍如何在资源应用程序中声明新的权限范围：

1. 登录到 [Azure 门户][AZURE-PORTAL]。
2. 通过身份验证后，在页面右上角选择 Azure AD 租户。
3. 从左侧导航窗格中选择“Azure Active Directory”扩展，然后单击“应用注册”。
4. 在列表中查找想要更新的应用程序并单击此应用程序。
5. 在应用程序页面中，单击“清单”打开内联清单编辑器。 
6. 可使用此编辑器直接编辑清单。 请注意，如前文所述，清单遵从[应用程序实体][APPLICATION-ENTITY]的架构：例如，假设要在资源应用程序 (API) 中实现/公开名为“Employees.Read.All”的新权限，则只需将新的/第二个元素添加到 oauth2Permissions 集合，如下所示：
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    条目必须唯一，因此必须为 `"id"` 属性生成新的全局唯一 ID (GUID)。 在本例中，由于我们指定了 `"type": "User"`，此权限可由资源/API 应用程序注册所在的 Azure AD 租户所验证的任何帐户同意。 这授予代表帐户进行访问的客户端应用程序权限。 说明和显示名称字符串将在同意期间使用，并显示在 Azure 门户中。
6. 更新清单完成后，请单击“保存”，保存清单。  
   
现在，保存清单时，可以向注册客户端应用程序提供之前添加的新权限的访问权限。 此时可以使用 Azure 门户的 Web UI，而不是编辑客户端应用程序的清单：  

1. 首先转到想要为其添加访问新 API 权限的客户端应用程序的“设置”边栏选项卡，然后单击“所需的权限”并选择“选择一个 API”。
2. 屏幕中会显示租户中已注册的资源应用程序 (API) 的列表。 单击资源应用程序将其选中，或在搜索框中键入应用程序名称。 找到应用程序后，单击“选择”。  
3. 随后将转到“选择权限”页，此页显示可用于资源应用程序的“应用程序权限”和“委托权限”列表。 选择新权限，以将其添加到客户端请求的权限列表。 此新权限将存储在客户端应用程序标识配置的“requiredResourceAccess”集合属性中。


就这么简单。 现在，你的应用程序将使用其新标识配置来运行。

## <a name="next-steps"></a>后续步骤
* 有关 Azure AD 中应用程序的应用程序对象与服务主体对象之间关系的详细信息，请参阅 [Application and service principal objects in Azure AD][AAD-APP-OBJECTS]（Azure AD 中的应用程序对象和服务主体对象）。
* 请参阅 [Azure AD developer glossary][AAD-DEVELOPER-GLOSSARY]（Azure AD 开发人员术语表），了解某些核心的 Azure Active Directory (AD) 开发人员概念的定义。

欢迎使用下方的意见部分提供反馈，帮助我们优化内容。

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/


