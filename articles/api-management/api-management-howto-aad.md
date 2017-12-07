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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 3faa6c1867808436a66a2b33ea1a9d79ede2c8fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> 仅在[开发人员和高级](https://azure.microsoft.com/en-us/pricing/details/api-management/)层提供 Azure Active Directory 集成。

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户
## <a name="overview"></a>概述
本指南介绍如何为 Azure Active Directory 中的用户启用对开发人员门户的访问。 本指南还介绍如何通过添加包含 Azure Active Directory 用户的外部组管理 Azure Active Directory 用户组。

> 若要完成本指南中的步骤，必须先有一个 Azure Active Directory，用于在其中创建应用程序。
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>如何使用 Azure Active Directory 授权开发人员帐户
若要开始，请单击 API 管理服务的 Azure 门户中的“发布者门户”。 这会转到 API 管理发布者门户。

![发布者门户][api-management-management-console]

> 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。
> 
> 

单击左侧“API 管理”菜单中的“安全”，并单击“外部标识”。

![外部标识][api-management-security-external-identities]

单击“Azure Active Directory”。 记下“重定向 URL”并在 Azure 经典门户中切换到 Azure Active Directory。

![外部标识][api-management-security-aad-new]

单击“添加”按钮创建新的 Azure Active Directory 应用程序，并选择“添加我的组织正在开发的应用程序”。

![添加新的 Azure Active Directory 应用程序][api-management-new-aad-application-menu]

为应用程序输入一个名称，选择“Web 应用程序”和/或“Web API”，并单击“下一步”按钮。

![新的 Azure Active Directory 应用程序][api-management-new-aad-application-1]

对于“登录 URL”，输入开发人员门户的登录 URL。 在此示例中，“登录 URL”为 `https://aad03.portal.current.int-azure-api.net/signin`。 

对于“应用 ID URL”，输入 Azure Active Directory 的默认域或自定义域，并向其追加一个唯一字符串。 在此示例中，**https://contoso5api.onmicrosoft.com** 的默认域与指定的 **/api** 的后缀一起使用。

![新的 Azure Active Directory 应用程序属性][api-management-new-aad-application-2]

单击复选按钮保存并创建应用程序，然后切换到“配置”选项卡配置新应用程序。

![创建的新 Azure Active Directory 应用程序][api-management-new-aad-app-created]

如果将为此应用程序使用多个 Azure Active Directory，请针对“应用程序是多租户的”单击“是”。 默认值为“否”。

![应用程序属于多租户型][api-management-aad-app-multi-tenant]

从发布者门户中“外部标识”选项卡的“Azure Active Directory”部分中复制“重定向 URL”，然后将其复制到“回复 URL”文本框中。 

![回复 URL][api-management-aad-reply-url]

滚动到配置选项卡的底部、选择“应用程序权限”下拉列表，并选中“读取目录数据”。

![应用程序权限][api-management-aad-app-permissions]

选择“委派权限”下拉菜单，并选中“启用登录并读取用户配置文件”。

![委派的权限][api-management-aad-delegated-permissions]

> 有关应用程序和委托的权限的详细信息，请参阅[访问图形 API][Accessing the Graph API]。
> 
> 

将“客户端 ID”复制到剪贴板。

![客户端 ID][api-management-aad-app-client-id]

切换回发布者门户并粘贴从 Azure Active Directory 应用程序配置中复制的“客户端 ID”。

![客户端 ID][api-management-client-id]

切换回 Azure Active Directory 配置，并单击“密钥”部分中的“选择持续时间”下拉列表并指定间隔。 在此示例中使用“1 年”。

![密钥][api-management-aad-key-before-save]

单击“保存”保存配置并显示密钥。 将该密钥复制到剪贴板。

> 记下此密钥。 关闭 Azure Active Directory 配置窗口后，无法再次显示密钥。
> 
> 

![键][api-management-aad-key-after-save]

切换回发布者门户并将密钥粘贴到“客户端机密”文本框中。

![客户端机密][api-management-client-secret]

“允许的租户”指定哪些目录有权访问 API 管理服务实例的 API。 指定要授予访问权限的 Azure Active Directory 实例的域。 可使用换行符、空格或逗号分隔多个域。

![允许的租户][api-management-client-allowed-tenants]


指定所需配置后，单击“保存”。

![保存][api-management-client-allowed-tenants-save]

保存更改后，指定的 Azure Active Directory 中的用户可按照[使用 Azure Active Directory 帐户登录开发人员门户][Log in to the Developer portal using an Azure Active Directory account]中的步骤登录到开发人员门户中。

可在“允许的租户”部分中指定多个域。 在任何用户可以从注册应用程序的原始域以外的其他域登录之前，不同域的全局管理员必须先授予权限以使应用程序访问目录数据。 要授予权限，全局管理员应转到 `https://<URL of your developer portal>/aadadminconsent`（例如，https://contoso.portal.azure-api.net/aadadminconsent），键入他们要授予访问权限的 Active Directory 租户的域名，并单击“提交”。 在以下示例中，`miaoaad.onmicrosoft.com` 中的全局管理员想要授予对此特定开发人员门户的权限。 

![权限][api-management-aad-consent]

在下一个屏幕中，会提示全局管理员确认授予权限。 

![权限][api-management-permissions-form]

> 如果非全局管理员在得到全局管理员授权之前尝试登录，登录尝试会失败，并显示错误屏幕。
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>如何添加外部 Azure Active Directory 组
在为 Azure Active Directory 中的用户启用访问之前，可将 Azure Active Directory 组添加到 API 管理中，以便更轻松地管理具有所需产品的组中的开发人员关联。

> 若要配置外部 Azure Active Directory 组，必须先按照之前部分中的过程在“标识”选项卡中配置 Azure Active Directory。 
> 
> 

从希望授予外部 Azure Active Directory 组访问权限的产品的“可见性”选项卡中添加该组。 单击“属性”，并单击所需产品的名称。

![配置产品][api-management-configure-product]

切换到“可见性”选项卡，并单击“从 Azure Active Directory 添加组”。

![添加组][api-management-add-groups]

从下拉列表中选择“Azure Active Directory 租户”，并在“要添加的组”文本框中键入所需组的名称。

![选择组][api-management-select-group]

此组可在 Azure Active Directory 的“组”列表中找到，如以下示例所示。

![Azure Active Directory 组列表][api-management-aad-groups-list]

单击“添加”验证组名称并添加组。 在此示例中，将添加 **Contoso 5 开发人员**外部组。 

![添加的组][api-management-aad-group-added]

单击“保存”保存新组选择。

从一个产品配置 Azure Active Directory 组后，可针对 API 管理服务实例中的其他产品在“可见性”选项卡上检查它。

若要查看和配置外部组的属性，添加它们后，从“组”选项卡中单击组名称。

![管理组][api-management-groups]

从此处，可编辑组的“名称”和“说明”。

![编辑组][api-management-edit-group]

来自已配置 Azure Active Directory 的用户可按照以下部分中的说明登录开发人员门户并查看和订阅任何他们拥有可见性的组。

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>如何使用 Azure Active Directory 帐户登录开发人员门户
要使用之前部分中配置的 Azure Active Directory 帐户登录开发人员门户，请使用来自 Active Directory 应用程序配置的“登录 URL”打开新浏览器，并单击“Azure Active Directory”。

![开发人员门户][api-management-dev-portal-signin]

在 Azure Active Directory 中输入用户之一的凭据，并单击“登录”。

![登录][api-management-aad-signin]

如果需要其他信息，可能出现注册表单的提示。 完成注册表单并单击“登录”。

![注册][api-management-complete-registration]

用户现已登录到 API 管理服务实例的开发人员门户中。

![注册完成][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
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
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
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

