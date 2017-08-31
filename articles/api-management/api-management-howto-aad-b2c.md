---
title: "使用 Azure Active Directory B2C 为开发人员帐户授权 - Azure API 管理 | Microsoft 文档"
description: "了解如何在 API 管理中使用 Azure Active Directory B2C 为用户授权。"
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eb7deb1a79d9db9ac5cfbea69b8d3c564eb55577
ms.contentlocale: zh-cn
ms.lasthandoff: 03/09/2017

---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory B2C 为开发人员帐户授权
## <a name="overview"></a>概述
Azure Active Directory B2C 是一种云标识管理解决方案，适用于消费型 Web 和移动应用程序。 可以使用它来管理对开发人员门户的访问。 本指南说明为了与 Azure Active Directory B2C 集成，需要在 API 管理中完成哪些配置。 有关使用经典 Azure Active Directory 启用对开发人员门户的访问的信息，请参阅[如何使用 Azure Active Directory 为开发人员帐户授权]。

> [!NOTE]
> 若要完成本指南中的步骤，必须先获取一个可在其中创建应用程序的 Azure Active Directory B2C 租户。 此外，需要准备好注册和登录策略。 有关详细信息，请参阅 [Azure Active Directory B2C 概述]。

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 为开发人员帐户授权

1. 若要开始，请单击 API 管理服务的 Azure 门户中的“发布者门户”。 这将转到 API 管理发布者门户。

   ![发布者门户][api-management-management-console]

   > [!NOTE]
   > 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。

2. 在“API 管理”菜单中单击“安全性”。 在“标识”选项卡中选择“Azure Active Directory B2C”。

  ![外部标识 1][api-management-howto-aad-b2c-security-tab]

3. 记下“重定向 URL”，然后在 Azure 门户中切换到 Azure Active Directory B2C。

  ![外部标识 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. 单击“应用程序”按钮。

  ![注册新应用程序 1][api-management-howto-aad-b2c-portal-menu]

5. 单击“添加”按钮创建新的 Azure Active Directory B2C 应用程序。

  ![注册新应用程序 2][api-management-howto-aad-b2c-add-button]

6. 在“新建应用程序”边栏选项卡中，输入应用程序的名称。 在“Web 应用/Web API”下面选择“是”，在“允许隐式流”下面选择“是”。 然后，从发布者门户中“标识”选项卡的“Azure Active Directory B2C”部分中复制“重定向 URL”，并将其粘贴到“回复 URL”文本框中。

  ![注册新应用程序 3][api-management-howto-aad-b2c-app-details]

7. 单击“创建”  按钮。 创建应用程序后，它会显示在“应用程序”边栏选项卡。 单击应用程序的名称可查看其详细信息。

  ![注册新应用程序 4][api-management-howto-aad-b2c-app-created]

8. 在“属性”边栏选项卡中，将“应用程序 ID”复制到剪贴板。

  ![应用程序 ID 1][api-management-howto-aad-b2c-app-id]

9. 切换回发布者门户，将该 ID 粘贴到“客户端 ID”文本框中。

  ![应用程序 ID 2][api-management-howto-aad-b2c-client-id]

10. 切换回 Azure 门户，单击“密钥”按钮，然后单击“生成密钥”。 单击“保存”保存配置并显示“应用密钥”。 将该密钥复制到剪贴板。

  ![应用密钥 1][api-management-howto-aad-b2c-app-key]

11. 切换回发布者门户并将密钥粘贴到“客户端机密”文本框中。

  ![应用密钥 2][api-management-howto-aad-b2c-client-secret]

12. 在“允许的租户”中指定 Azure Active Directory B2C 租户的域名。

  ![允许的租户][api-management-howto-aad-b2c-allowed-tenant]

13. 指定“注册策略”和“登录策略”。 或者，也可以提供“配置文件编辑策略”和“密码重置策略”。

  ![策略][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > 有关策略的详细信息，请参阅 [Azure Active Directory B2C：可扩展的策略框架]。

14. 指定所需的配置后，单击“保存”。

  保存更改后，开发人员可以使用 Azure Active Directory B2C 创建新帐户并登录到开发人员门户。

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 注册开发人员帐户

1. 若要使用 Azure Active Directory B2C 注册开发人员帐户，请打开新的浏览器窗口并转到开发人员门户。 单击“注册”按钮。

   ![开发人员门户 1][api-management-howto-aad-b2c-dev-portal]

2. 选择使用 **Azure Active Directory B2C** 注册。

   ![开发人员门户 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. 你将重定向到在上一部分中配置的注册策略。 选择使用电子邮件地址或现有的某个社交帐户注册。

   > [!NOTE]
   > 如果“Azure Active Directory B2C”是发布者门户上“标识”选项卡中启用的唯一选项，你将直接重定向到注册策略。

   ![开发人员门户][api-management-howto-aad-b2c-dev-portal-b2c-options]

   注册完成后，你将重定向回到开发人员门户。 现已登录到 API 管理服务实例的开发人员门户。

    ![注册完成][api-management-registration-complete]

## <a name="next-steps"></a>后续步骤

*  [Azure Active Directory B2C 概述]
*  [Azure Active Directory B2C：可扩展的策略框架]
*  [将 Microsoft 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 Google 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 LinkedIn 帐户用作 Azure Active Directory B2C 中的标识提供者]
*  [将 Facebook 帐户用作 Azure Active Directory B2C 中的标识提供者]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C 概述]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[如何使用 Azure Active Directory 为开发人员帐户授权]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C：可扩展的策略框架]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[将 Microsoft 帐户用作 Azure Active Directory B2C 中的标识提供者]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[将 Google 帐户用作 Azure Active Directory B2C 中的标识提供者]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[将 Facebook 帐户用作 Azure Active Directory B2C 中的标识提供者]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[将 LinkedIn 帐户用作 Azure Active Directory B2C 中的标识提供者]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

