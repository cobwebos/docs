---
title: 在 Azure AD 中注册资源应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册资源（或 API）应用，使客户端应用程序能够在身份验证时请求对资源的访问权限。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: e92fab392dc73d8de0b7b2547e38b3f345562930
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975852"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册资源应用程序

本文介绍如何在 Azure Active Directory 中注册资源（或 API）应用程序。 资源应用程序是 FHIR 服务器 API 本身的 Azure Active Directory 表示形式，客户端应用程序在身份验证时可以请求对资源的访问权限。 在 OAuth 行话中，资源应用程序也称为“受众”。 

## <a name="azure-api-for-fhir"></a>适用于 FHIR 的 Azure API

如果使用 Azure API for FHIR，在部署服务时，系统会自动创建资源应用程序。 只要在应用程序所部署到的同一个 Azure Active Directory 租户中使用 Azure API for FHIR，就可以跳过本操作指南，并改为部署 Azure API for FHIR 开始操作。

如果使用不同的 Azure Active Directory 租户（与订阅不关联），可以使用 PowerShell 将 Azure API for FHIR 资源应用程序导入到租户中：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

或者，可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>适用于 Azure 的 FHIR 服务器

如果使用开源的适用于 Azure 的 FHIR 服务器，请遵循以下步骤注册资源应用程序。

### <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册”：  

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击“新建注册”。 

### <a name="add-a-new-application-registration"></a>添加新的应用程序注册

填写新应用程序的详细信息。 在显示名称方面没有特定的要求，但将它设置为 FHIR 服务器的 URI 可以方便地找到它：

![新建应用程序注册](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>设置标识符 URI 并定义范围

资源应用程序具有标识符 URI（应用程序 ID URI），客户端在请求资源访问权限时可以使用该 URI。 此值将填充访问令牌的 `aud` 声明。 建议将此 URI 设置为 FHIR 服务器的 URI。 对于 SMART on FHIR 应用，假设受众是 FHIR 服务器的 URI。 

1. 单击“公开 API” 

2. 单击“应用程序 ID URI”旁边的“设置”。  

3. 输入标识符 URI，然后单击“保存”。  适当的标识符 URI 是 FHIR 服务器的 URI。

4. 单击“添加范围”，并添加要为 API 定义的任何范围。  至少需要添加一个范围，将来才能向资源应用程序授予权限。 如果你没有任何可添加的特定范围，可以添加 user_impersonation 作为范围。

![受众和范围](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>定义应用程序角色

Azure API for FHIR 和 OSS 适用于 Azure 的 FHIR 服务器使用 [Azure Active Directory 应用程序角色](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles)来实现基于角色的访问控制。 若要定义哪些角色可供 FHIR 服务器 API 使用，请打开资源应用程序的[清单](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)：

1. 单击“清单”： 

    ![应用程序角色](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. 在 `appRoles` 属性中，添加希望用户或应用程序具有的角色：

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册资源应用程序。 接下来，注册你的机密客户端应用程序。
 
>[!div class="nextstepaction"]
>[注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)