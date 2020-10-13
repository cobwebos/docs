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
ms.openlocfilehash: f39fb5766965e3881068bd6d2fd3a8142f9eb2ac
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975903"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册公共客户端应用程序

本文介绍如何在 Azure Active Directory 中注册公共应用程序。  

客户端应用程序注册是可以代表用户完成身份验证和请求 API 权限的应用程序的 Azure Active Directory 表示形式。 公共客户端是无法将机密保密的应用程序，例如移动应用程序和单页 JavaScript 应用程序。 此过程类似于[注册机密客户端](register-confidential-azure-ad-client-app.md)，但由于不信任公共客户端能够保守应用程序的机密，因此无需添加此类客户端。

快速入门提供了有关如何 [使用 Microsoft 标识平台注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的一般信息。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”。****

2. 在 " **Azure Active Directory** " 边栏选项卡中，单击 **应用注册**：

    ![Azure 门户。 新应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 单击 " **新注册**"。

## <a name="application-registration-overview"></a>应用程序注册概述

1. 为应用程序指定一个显示名称。

2. 提供答复 URL。 回复 URL 是将身份验证代码返回到客户端应用程序的位置。 稍后可以添加更多的答复 Url 并编辑现有的 Url。

    ![Azure 门户。 新的公共应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


若要将 [桌面](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)、 [移动](https://docs.microsoft.com/azure/active-directory/develop/scenario-mobile-app-registration) 或 [单页](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) 应用程序配置为公共应用程序：

1. 在 [Azure 门户](https://portal.azure.com)的 **应用注册**中，选择你的应用，然后选择 " **身份验证**"。

2. 选择 "**高级设置**" "  >  **默认客户端类型**"。 对于 " **将应用程序视为公共客户端**"，请选择 **"是"**。

3. 对于单页应用程序，选择 " **访问令牌** " 和 " **ID 令牌** " 以启用隐式流。

   - 如果你的应用程序登录了用户，请选择“ID 令牌”。
   - 如果你的应用程序也需调用受保护的 Web API，请选择“访问令牌”。

## <a name="api-permissions"></a>API 权限

与 [机密客户端应用程序](register-confidential-azure-ad-client-app.md)类似，你将需要选择此应用程序应该能够代表用户请求的 API 权限：

1. 打开 **API 权限**。

    如果你使用的是用于 FHIR 的 Azure API，则需要通过在 **我的组织使用的 api**下搜索 Azure 医疗保健 api 来添加 Azure 医疗保健 api 的权限。 如果已 [部署了用于 FHIR 的 AZURE API](fhir-paas-powershell-quickstart.md)，你将只能找到此文件。

    
    如果引用的是其他资源应用程序，请选择先前在 **"我的 api**" 下创建的[FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)：

    ![Azure 门户。 新公共 API 权限-适用于 FHIR 的 Azure API 默认值](media/public-client-app/api-permissions.png)


2. 选择希望应用程序能够请求的权限： " ![ Azure 门户"。 应用权限](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>验证 FHIR 服务器证书颁发机构
如果在本文中注册的应用程序和 FHIR 服务器处于相同的 Azure AD 租户中，则可以继续执行后续步骤。

如果你在 FHIR 服务器的其他 Azure AD 租户中配置客户端应用程序，你将需要更新该 **颁发机构**。 在用于 FHIR 的 Azure API 中，请在 "设置" 下设置颁发机构-> 身份验证。 将您的权限设置为 **https://login.microsoftonline.com/\<TENANT-ID>** 。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册公用客户端应用程序。 接下来，使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
