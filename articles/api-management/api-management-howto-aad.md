---
title: "使用 Azure Active Directory 授权开发人员帐户 - Azure API 管理 | Microsoft Docs"
description: "了解如何在 API 管理中使用 Azure Active Directory 授权用户。"
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d89257cba70fb82d56fb1beef8a8efe66a8af02d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户

本文介绍如何为 Azure Active Directory (Azure AD) 中的用户启用对开发人员门户的访问。 本指南还介绍如何通过添加包含用户的外部组管理 Azure AD 用户组。

> [!NOTE]
> 仅在[开发人员、标准和高级](https://azure.microsoft.com/pricing/details/api-management/)层中提供 Azure AD 集成。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 为开发人员帐户授权

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. Select ![箭头](./media/api-management-howto-aad/arrow.png)。
3. 在搜索框中键入 **api**。
4. 选择“API 管理服务”。
5. 选择自己的 API 管理服务实例。
6. 在“安全性”下，选择“标识”。

7. 在顶部选择“+添加”。

    此时将在右侧显示“添加标识提供者”窗格。
8. 在“提供者类型”下，选择 **Azure Active Directory**。

    此时将在窗格中显示用于输入其他必要信息的控件。 控件包括“客户端 ID”和“客户端机密”。 （本文稍后将介绍有关这些控件的信息。）
9. 记下“重定向 URL”的内容。
    
   ![在 Azure 门户中添加标识提供者的步骤](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 在浏览器中，打开另一个标签页。 
11. 转到 [Azure 门户](https://portal.azure.com)。
12. Select ![箭头](./media/api-management-howto-aad/arrow.png)。
13. 键入 **active**。 此时会显示“Azure Active Directory”窗格。
14. 选择“Azure Active Directory”。
15. 在“管理”下，选择“应用注册”。
16. 选择“新建应用程序注册”。

    ![用于创建新应用注册的选项](./media/api-management-howto-aad/api-management-with-aad002.png)

    此时将在右侧显示“创建”窗格。 可以在其中输入 Azure AD 应用相关信息。
17. 输入应用程序的名称。
18. 对于应用程序类型，选择“Web 应用/API”。
19. 对于“登录 URL”，输入开发人员门户的登录 URL。 在此示例中，登录 URL 是：https://apimwithaad.portal.azure-api.net/signin。
20. 选择“创建”以创建应用程序。
21. 若要查找应用，请选择“应用注册”并按名称搜索。

    ![应用搜索框](./media/api-management-howto-aad/find-your-app.png)
22. 注册该应用程序后，请转到“答复 URL”并确保“重定向 URL”设置为从步骤 9 中获得的值。 
23. 如果要配置应用程序（例如，更改“应用 ID URL”），请选择“属性”。

    ![打开“属性”窗格](./media/api-management-howto-aad/api-management-with-aad004.png)

    如果将为此应用程序使用多个 Azure AD 实例，请针对“多租户”选择“是”。 默认值为“否”。
24. 通过选择“所需权限”来设置应用程序权限。
25. 选择应用程序，并选中“读取目录数据”和“登录并读取用户个人资料”复选框。

    ![权限复选框](./media/api-management-howto-aad/api-management-with-aad005.png)

    有关应用程序权限和委托权限的详细信息，请参阅[访问图形 API][Accessing the Graph API]。
26. 在左侧窗格中，复制“应用程序 ID”值。

    ![“应用程序 ID”值](./media/api-management-howto-aad/application-id.png)
27. 切换回 API 管理应用程序。 

    在“添加标识提供者”窗口中，将“应用程序 ID”值粘贴到“客户端 ID”框中。
28. 切换回到 Azure AD 配置，并选择“密钥”。
29. 通过指定名称和持续时间来创建新密钥。 
30. 选择“保存”。 此时将生成密钥。

    将该密钥复制到剪贴板。

    ![用于创建密钥的选项](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > 记下此密钥。 关闭 Azure AD 配置窗格后，无法再次显示密钥。
    > 
    > 
31. 切换回 API 管理应用程序。 

    在“添加标识提供者”窗口中，将密钥粘贴到“客户端机密”文本框中。
32. “添加标识提供者”窗口还包含“允许的租户”文本框。 可在此框中指定要授予 API 管理服务实例的 API 对哪些 Azure AD 实例域的访问权限。 可使用换行符、空格或逗号分隔多个域。

    可在“允许的租户”部分中指定多个域。 在任何用户可以从注册应用程序的原始域以外的其他域登录之前，不同域的全局管理员必须先授予权限以使应用程序访问目录数据。 若要授予权限，全局管理员应：
    
    a. 转到 `https://<URL of your developer portal>/aadadminconsent`（例如 https://contoso.portal.azure-api.net/aadadminconsent）。
    
    b. 键入他们想要授权访问的 Azure AD 租户域名。
    
    c. 选择“提交”。 
    
    在以下示例中，miaoaad.onmicrosoft.com 中的全局管理员想要授予对此特定开发人员门户的权限。 

33. 指定所需配置后，选择“添加”。

    ![“添加标识提供者”窗格中的“添加”按钮](./media/api-management-howto-aad/api-management-with-aad007.png)

保存更改后，指定的 Azure AD 实例中的用户便可按照[使用 Azure AD 帐户登录开发人员门户](#log_in_to_dev_portal)中的步骤登录到开发人员门户。

![输入 Azure AD 租户的名称](./media/api-management-howto-aad/api-management-aad-consent.png)

在下一个屏幕中，会提示全局管理员确认授予权限。 

![确认分配权限](./media/api-management-howto-aad/api-management-permissions-form.png)

如果非全局管理员在得到全局管理员授权之前尝试登录，登录尝试会失败，并显示错误屏幕。

## <a name="add-an-external-azure-ad-group"></a>添加外部 Azure AD 组

在为 Azure AD 实例中的用户启用访问之后，可将 Azure AD 组添加到 API 管理中。 然后，可以更轻松地管理具有所需产品的组中的开发人员关联。

若要配置外部 Azure AD 组，必须先按照之前部分中的过程在“标识”选项卡中配置 Azure AD 实例。 

可从 API 管理实例的“组”选项卡添加外部 Azure AD 组。

1. 选择“组”选项卡。
2. 选择“添加 AAD 组”按钮。
   ![“添加 AAD 组”按钮](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 选择要添加的组。
4. 按“选择”按钮。

创建外部 Azure AD 组之后，可以查看和配置其属性。 从“组”选项卡中选择该组的名称。在此处，可以编辑该组的“名称”和“说明”信息。
 
配置的 Azure AD 实例中的用户现在可以登录开发人员门户。 他们可以查看和订阅可见的任何组。

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>使用 Azure AD 帐户登录开发人员门户

使用前面部分中配置的 Azure AD 帐户登录开发人员门户：

1. 使用 Active Directory 应用程序配置中的登录 URL 打开新的浏览器窗口，并选择“Azure Active Directory”。

   ![登录页][api-management-dev-portal-signin]

2. 输入 Azure Active Directory 中某个用户的凭据，并选择“登录”。

   ![使用用户名和密码登录][api-management-aad-signin]

3. 如果需要其他信息，可能出现注册表单的提示。 完成注册表单并选择“登录”。

   ![注册窗体中的“注册”按钮][api-management-complete-registration]

用户现已登录到 API 管理服务实例的开发人员门户。

![完成注册后的开发人员门户][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
