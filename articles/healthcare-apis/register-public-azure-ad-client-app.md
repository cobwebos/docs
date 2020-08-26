---
title: 在 Azure AD 中注册公共客户端应用 - Azure API for FHIR
description: 本文介绍如何在 Azure Active Directory 中注册公共客户端应用程序，以准备好在 Azure 中部署 FHIR API。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: df1e6159baafc11c4b73c33feaf936784c05469e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853035"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册公共客户端应用程序

本文介绍如何在 Azure Active Directory 中注册公共应用程序。  

客户端应用程序注册是可以代表用户完成身份验证和请求 API 权限的应用程序的 Azure Active Directory 表示形式。 公共客户端是无法将机密保密的应用程序，例如移动应用程序和单页 JavaScript 应用程序。 此过程类似于[注册机密客户端](register-confidential-azure-ad-client-app.md)，但由于不信任公共客户端能够保守应用程序的机密，因此无需添加此类客户端。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。 

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册”：  

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击“新建注册”。 

## <a name="application-registration-overview"></a>应用程序注册概述

1. 指定应用程序的显示名称。

2. 提供回复 URL。 将在回复 URL 中向客户端应用程序返回身份验证代码。 以后可以添加更多的回复 URL 以及编辑现有的回复 URL。

    ![Azure 门户。 新的公共应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API 权限

与[机密客户端应用程序](register-confidential-azure-ad-client-app.md)一样，需要选择此应用程序应该能够代表用户请求哪些 API 权限：

1. 打开“API 权限”。 

    如果使用 Azure API for FHIR，可以通过搜索“我的组织使用的 API”下的 Azure 医疗保健 API，来添加对 Azure 医疗保健 API 的权限（如下图所示）。 
    
    如果引用了其他资源应用程序，请在“我的 API”下选择以前创建的 [FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)： 

    ![Azure 门户。 新的公共 API 权限 - Azure API for FHIR 默认值](media/public-client-app/api-permissions.png)


2. 选择希望应用程序能够请求的权限：![Azure 门户。 应用权限](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>验证 FHIR 服务器颁发机构
如果在本文中注册的应用程序和你的 FHIR 服务器在同一 Azure AD 租户中，则可以继续执行后续步骤。

如果在不包含你的 FHIR 服务器的其他 Azure AD 租户中配置客户端应用程序，则需要更新颁发机构。  在 Azure API for FHIR 中的“设置”->“身份验证”下设置颁发机构。 将您的权限设置为 **https://login.microsoftonline.com/\<TENANT-ID>** 。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何在 Azure Active Directory 中注册公共客户端应用程序。 接下来，请使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
