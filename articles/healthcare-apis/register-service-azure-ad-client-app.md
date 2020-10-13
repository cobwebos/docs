---
title: 在 Azure AD 中注册服务应用 - Azure API for FHIR
description: 了解如何在 Azure Active Directory 中注册服务客户端应用程序。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975818"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册服务客户端应用程序

本文介绍如何在 Azure Active Directory 中注册服务客户端应用程序。 客户端应用程序注册是可用于身份验证和获取令牌的应用程序的 Azure Active Directory 表示形式。 服务客户端旨在供应用程序用来获取访问令牌，而无需用户进行交互式身份验证。 服务客户端拥有特定的应用程序权限，在获取访问令牌时使用应用程序机密（密码）。

按照以下步骤创建新的服务客户端。

## <a name="app-registrations-in-azure-portal"></a>Azure 门户中的应用注册

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 " **Azure Active Directory**"。

2. 选择 **“应用注册”**。

    ![Azure 门户。 新建应用注册。](media/how-to-aad/portal-aad-new-app-registration.png)

3. 选择“新注册”。

4. 为服务客户端指定显示名称。 服务客户端应用程序通常不使用回复 URL。

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure 门户。新的服务客户端应用注册。":::

5. 选择“注册”  。

## <a name="api-permissions"></a>API 权限

现在，你已注册应用程序，你将需要选择此应用程序应该能够代表用户请求的 API 权限：

1. 选择“API 权限”****。
1. 选择“添加权限”。

    如果你使用的是用于 FHIR 的 Azure API，则需要通过在**我的组织使用的 api**下搜索**azure 医疗保健 Api**来添加 azure 医疗保健 api 的权限。 

    如果引用的是其他资源应用程序，请选择之前在 **"我的 api**" 下创建的[FHIR API 资源应用程序注册](register-resource-azure-ad-client-app.md)。

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Azure 门户。新的服务客户端应用注册。" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. 选择 "作用域" (权限) 机密应用程序应该能够代表用户请求：

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Azure 门户。新的服务客户端应用注册。":::

1. 向应用程序授予许可。 如果你没有所需的权限，请咨询 Azure Active Directory 管理员：

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Azure 门户。新的服务客户端应用注册。":::

## <a name="application-secret"></a>应用程序密码

服务客户端需要机密 (密码) 才能获取令牌。

1. 选择“证书和机密”。
2. 选择“新建客户端机密”。 

    ![Azure 门户。 服务客户端机密](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. 提供机密 (1 年、2年或永不) 的密码的描述和持续时间。

4. 一旦生成了机密，就只能在门户中显示一次。 记下它并安全地存储。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Active Directory 中注册服务客户端应用程序。 接下来，使用 Postman 测试对 FHIR 服务器的访问。
 
>[!div class="nextstepaction"]
>[使用 Postman 访问 Azure API for FHIR](access-fhir-postman-tutorial.md)
