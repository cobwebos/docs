---
title: 在 Azure AD 中注册机密客户端应用 - Azure API for FHIR
description: 在 Azure Active Directory 中注册可以代表用户进行身份验证并请求访问资源应用程序的机密客户端应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852542"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册机密客户端应用程序

本教程介绍如何在 Azure Active Directory 中注册机密客户端应用程序。 

客户端应用程序注册是可以代表用户进行身份验证并请求访问[资源应用程序](register-resource-azure-ad-client-app.md)的应用程序的 Azure Active Directory 表示形式。 机密客户端应用程序是信任其可以保守机密，并在请求访问令牌时可以提供该机密的应用程序。 例如，服务器端应用程序就是机密应用程序。

若要在门户中注册新的机密应用程序，请执行以下步骤。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册”：  

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击“新建注册”。 

## <a name="register-a-new-application"></a>注册新应用程序

1. 指定应用程序的显示名称。

2. 提供回复 URL。 以后可以更改这些详细信息，但如果你知道应用程序的回复 URL，请现在就输入。

    ![新建机密客户端应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API 权限

接下来添加 API 权限：

1. 打开“API 权限”： 

    ![机密客户端。 API 权限](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. 单击“添加权限” 

3. 选择相应的资源 API：

    对于 Azure API for FHIR（托管服务），请单击“我的组织使用的 API”，然后搜索“Azure 医疗保健 API”。  对于开源的适用于 Azure 的 FHIR 服务器，请选择你的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)：

    ![机密客户端。 我的 API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. 选择机密应用程序应该能够代表用户请求的范围（权限）：

    ![机密客户端。 委派的权限](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>应用程序机密

1. 创建应用程序机密（客户端机密）：

    ![机密客户端。 应用程序机密](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 提供机密的说明和持续时间。

3. 生成机密后，它只会在门户中显示一次。 请记下并安全存储该机密。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册机密客户端应用程序。 接下来可以部署 [Azure API for FHIR](fhir-paas-powershell-quickstart.md)。

部署 Azure API for FHIR 后，可以查看其他可用设置。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)