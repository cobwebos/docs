---
title: "使用 Azure Active Directory 授权开发人员帐户 - Azure API 管理 | Microsoft 文档"
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
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户

本指南介绍如何为 Azure Active Directory 中的用户启用对开发人员门户的访问。 本指南还介绍如何通过添加包含 Azure Active Directory 用户的外部组管理 Azure Active Directory 用户组。

> [!WARNING]
> 仅在[开发人员、标准和高级](https://azure.microsoft.com/pricing/details/api-management/)层中提供 Azure Active Directory 集成。

## <a name="prerequisites"></a>先决条件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>如何使用 Azure Active Directory 授权开发人员帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. Select ![箭头](./media/api-management-howto-aad/arrow.png)。
3. 在搜索框中键入“api”。
4. 单击“API 管理服务”。
5. 选择 APIM 服务实例。
6. 在“安全性”下，选择“标识”。

    ![外部标识](./media/api-management-howto-aad/api-management-with-aad001.png)
7. 从顶部单击“+添加”。

    此时将在右侧显示“添加标识提供者”窗口。
8. 在“提供者类型”下，选择 **Azure Active Directory**。

    此时将在窗口中显示用于输入其他必要信息的控件。 控件包括：**客户端 ID**、**客户端密码**（可在本教程的后面部分获得相关信息）。
9. 记下**重定向 URL**。  
10. 在浏览器中，打开另一个标签页。 
11. 打开 [Azure 门户](https://portal.azure.com)。
12. Select ![箭头](./media/api-management-howto-aad/arrow.png)。
13. 键入“active”，将显示 **Azure Active Directory**。
14. 选择“Azure Active Directory”。
15. 在“管理”下，选择“应用注册”。

    ![应用注册](./media/api-management-howto-aad/api-management-with-aad002.png)
16. 单击“新建应用程序注册”。

    此时将在右侧显示“创建”窗口。 可以在其中输入 AAD 应用相关信息。
17. 输入应用程序的名称。
18. 对于应用程序类型，选择“Web 应用/API”。
19. 对于“登录 URL”，输入开发人员门户的登录 URL。 在此示例中，登录 URL 是：https://apimwithaad.portal.azure-api.net/signin。
20. 单击“创建”以创建应用程序。
21. 若要查找应用，请选择“应用注册”并按名称搜索。

    ![应用注册](./media/api-management-howto-aad/find-your-app.png)
22. 注册该应用程序后，请转到“答复 URL”并确保“重定向 URL”设置为从步骤 9 中获得的值。 
23. 如果要配置应用程序（例如，更改**应用 ID URL**），请选择“属性”。

    ![应用注册](./media/api-management-howto-aad/api-management-with-aad004.png)

    如果将为此应用程序使用多个 Azure Active Directory，请针对“应用程序是多租户的”单击“是”。 默认值为“否”。
24. 通过选择“所需权限”来设置应用程序权限。
25. 选择应用程序并选中“读取目录数据”和“登录并读取用户个人资料”。

    ![应用注册](./media/api-management-howto-aad/api-management-with-aad005.png)

    有关应用程序和委托的权限的详细信息，请参阅[访问图形 API][Accessing the Graph API]。
26. 在左侧窗口中，复制**应用程序 ID** 值。

    ![应用注册](./media/api-management-howto-aad/application-id.png)
27. 切换回 API 管理应用程序。 应显示“添加标识提供者”窗口。 <br/>在“客户端 ID”框中粘贴**应用程序 ID** 值。
28. 切换回 Azure Active Directory 配置，然后单击“密钥”。
29. 通过指定名称和持续时间来创建新密钥。 
30. 单击“ **保存**”。 此时将生成密钥。

    将该密钥复制到剪贴板。

    ![应用注册](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > 记下此密钥。 关闭 Azure Active Directory 配置窗口后，无法再次显示密钥。
    > 
    > 
31. 切换回 API 管理应用程序。 <br/>在“添加标识提供者”窗口中，将密钥粘贴到“客户端密码”文本框中。
32. “添加标识提供者”窗口包含“允许的租户”文本框，可在其中指定哪些目录可以访问 API 管理服务实例的 API。 <br/>指定要授予访问权限的 Azure Active Directory 实例的域。 可使用换行符、空格或逗号分隔多个域。

    可在“允许的租户”部分中指定多个域。 在任何用户可以从注册应用程序的原始域以外的其他域登录之前，不同域的全局管理员必须先授予权限以使应用程序访问目录数据。 要授予权限，全局管理员应转到 `https://<URL of your developer portal>/aadadminconsent`（例如，https://contoso.portal.azure-api.net/aadadminconsent），键入他们要授予访问权限的 Active Directory 租户的域名，并单击“提交”。 在以下示例中，`miaoaad.onmicrosoft.com` 中的全局管理员想要授予对此特定开发人员门户的权限。 

33. 指定所需配置后，单击“添加”。

    ![应用注册](./media/api-management-howto-aad/api-management-with-aad007.png)

保存更改后，指定的 Azure Active Directory 中的用户便可按照[使用 Azure Active Directory 帐户登录开发人员门户](#log_in_to_dev_portal)中的步骤登录到开发人员门户。

![权限](./media/api-management-howto-aad/api-management-aad-consent.png)

在下一个屏幕中，会提示全局管理员确认授予权限。 

![权限](./media/api-management-howto-aad/api-management-permissions-form.png)

如果非全局管理员在得到全局管理员授权之前尝试登录，登录尝试会失败，并显示错误屏幕。

## <a name="how-to-add-an-external-azure-active-directory-group"></a>如何添加外部 Azure Active Directory 组

在为 Azure Active Directory 中的用户启用访问之前，可将 Azure Active Directory 组添加到 API 管理中，以便更轻松地管理具有所需产品的组中的开发人员关联。

若要配置外部 Azure Active Directory 组，必须先按照之前部分中的过程在“标识”选项卡中配置 Azure Active Directory。 

外部 Azure Active Directory 组将从 API 管理实例的“组”选项卡添加。

![组](./media/api-management-howto-aad/api-management-with-aad008.png)

1. 选择“组”选项卡。
2. 单击“添加 AAD 组”按钮。
3. 选择要添加的组。
4. 按“选择”按钮。

创建 Azure Active Directory 组后，可以在添加外部组后查看和配置外部组的属性，方法是从“组”选项卡中单击组名称。

从此处，可编辑组的“名称”和“说明”。
 
来自已配置 Azure Active Directory 的用户可按照以下部分中的说明登录开发人员门户并查看和订阅任何他们拥有可见性的组。

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>如何使用 Azure Active Directory 帐户登录开发人员门户

要使用之前部分中配置的 Azure Active Directory 帐户登录开发人员门户，请使用来自 Active Directory 应用程序配置的“登录 URL”打开新浏览器，并单击“Azure Active Directory”。

![开发人员门户][api-management-dev-portal-signin]

在 Azure Active Directory 中输入用户之一的凭据，并单击“登录”。

![登录][api-management-aad-signin]

如果需要其他信息，可能出现注册表单的提示。 完成注册表单并单击“登录”。

![注册][api-management-complete-registration]

用户现已登录到 API 管理服务实例的开发人员门户中。

![注册完成][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
