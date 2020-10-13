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
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826216"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册机密客户端应用程序

本教程介绍如何在 Azure Active Directory 中注册机密客户端应用程序。 

客户端应用程序注册是可以代表用户进行身份验证并请求访问[资源应用程序](register-resource-azure-ad-client-app.md)的应用程序的 Azure Active Directory 表示形式。 机密客户端应用程序是信任其可以保守机密，并在请求访问令牌时可以提供该机密的应用程序。 例如，服务器端应用程序就是机密应用程序。

若要在门户中注册新的机密应用程序，请执行以下步骤。

## <a name="register-a-new-application"></a>注册新应用程序

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 " **Azure Active Directory**"。

1. 选择 **“应用注册”**。

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

1. 选择“新注册”。

1. 为应用程序指定一个显示名称。

1. 提供答复 URL。 稍后可以更改这些详细信息，但如果你知道应用程序的回复 URL，请立即输入。

    ![新的机密客户端应用注册。](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. 选择“注册”。

## <a name="api-permissions"></a>API 权限

现在，你已注册应用程序，你将需要选择此应用程序应该能够代表用户请求的 API 权限：

1. 选择“API 权限”****。

    ![机密客户端。 API 权限](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. 选择“添加权限”。

    如果你使用的是用于 FHIR 的 Azure API，则需要通过在**我的组织使用的 api**下搜索**azure 医疗保健 Api**来添加 azure 医疗保健 api 的权限。 如果已 [部署了用于 FHIR 的 AZURE API](fhir-paas-powershell-quickstart.md)，你将只能找到此文件。

    如果引用的是其他资源应用程序，请选择之前在 **"我的 api**" 下创建的[FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="机密客户端。我的组织 Api" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. 选择 "作用域" (权限) 机密应用程序应该能够代表用户请求：

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="机密客户端。我的组织 Api":::

## <a name="application-secret"></a>应用程序密码

1. 选择“证书和机密”。
1. 选择“新建客户端机密”。  

    ![机密客户端。 应用程序密码](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. 提供机密 (1 年、2年或永不) 的密码的描述和持续时间。

3. 生成后，只会在门户中显示一次。 记下它，并将其安全地存储。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册机密客户端应用程序。 接下来，可以使用 Postman 访问 FHIR 服务器
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
