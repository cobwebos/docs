---
title: 使用 Azure Active Directory 授权开发人员帐户 - Azure API 管理 | Microsoft Docs
description: 了解如何在 API 管理中使用 Azure Active Directory 授权用户。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 067d4488b064ede572a4b3ad94c94fb1552c827d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454449"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户

本文介绍如何为 Azure Active Directory (Azure AD) 中的用户启用对开发人员门户的访问。 本指南还介绍如何通过添加包含用户的外部组管理 Azure AD 用户组。

## <a name="prerequisites"></a>必备组件

- 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 为开发人员帐户授权

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. Select ![箭头](./media/api-management-howto-aad/arrow.png).
3. 在搜索框中键入 **api**。
4. 选择“API 管理服务”。
5. 选择自己的 API 管理服务实例。
6. Under **Security**, select **Identities**.
7. 在顶部选择“+添加”。

    此时将在右侧显示“添加标识提供者”窗格。
8. 在“提供者类型”下，选择 **Azure Active Directory**。

    此时将在窗格中显示用于输入其他必要信息的控件。 控件包括“客户端 ID”和“客户端机密”。 （本文稍后将介绍有关这些控件的信息。）
9. Make a note of the content of **Redirect URL**.
    
   ![在 Azure 门户中添加标识提供者的步骤](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 在浏览器中，打开另一个标签页。 
11. Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) to register an app in Active Directory.
12. Under **Manage**, select **App registrations**.
13. 选择“新注册”。 On the **Register an application** page, set the values as follows:
    
* Set **Name** to a meaningful name. e.g., *developer-portal*
* Set **Supported account types** to **Accounts in this organizational directory only**. 
* Set **Redirect URI** to the value you got from step 9. 
* Choose **Register**. 

14.  After the application is registered, copy the **Application (client) ID** from the **Overview** page. 
15. Go back to your API Management instance. In the **Add identity provider** window, paste the **Application (client) ID** value into the **Client ID** box.
16. Switch back to the Azure AD configuration, Select **Certificates & secrets** under **Manage**. Select the **New client secret** button. Enter a value in **Description**, select any option for **Expires** and choose **Add**. Copy the client secret value before leaving the page. 在下一步中将要使用它。 
17. Under **Manage**, select **Authentication** and then select **ID tokens** under **Implicit Grant**
18. Go back to your API Management instance, paste the secret into the **Client secret** box.

    > [!IMPORTANT]
    > 请确保在密钥过期前更新“客户端机密”。 
    >  
    >

19. “添加标识提供者”窗口还包含“允许的租户”文本框。 可在此框中指定要授予 API 管理服务实例的 API 对哪些 Azure AD 实例域的访问权限。 可使用换行符、空格或逗号分隔多个域。

> [!NOTE]
> 可在“允许的租户”部分中指定多个域。 在任何用户可以从注册应用程序的原始域以外的其他域登录之前，不同域的全局管理员必须先授予权限以使应用程序访问目录数据。 To grant permission, the global administrator should: a. 转到 `https://<URL of your developer portal>/aadadminconsent`（例如， https://contoso.portal.azure-api.net/aadadminconsent) 。
> b. 键入他们想要授权访问的 Azure AD 租户域名。
> c. 选择“提交”。 

20.  指定所需配置后，选择“添加”。

保存更改后，指定的 Azure AD 实例中的用户便可按照[使用 Azure AD 帐户登录开发人员门户](#log_in_to_dev_portal)中的步骤登录到开发人员门户。

## <a name="add-an-external-azure-ad-group"></a>添加外部 Azure AD 组

After you enable access for users in an Azure AD tenant, you can add Azure AD groups into API Management. As a result, you can control product visibility using Azure AD groups.

To add an external Azure AD group into APIM, you must first complete the previous section. Additionally, the application you registered must be granted access to the Azure Active Directory Graph API with `Directory.ReadAll` permission by following below steps: 

1. Go back to your App Registration that was created in the previous section
2. Click on the **API Permissions** tab, then click **+Add a permission** button 
3. In the **Request API Permissions** pane, select the **Microsoft APIs** tab, and scroll to the bottom to find the **Azure Active Directory Graph** tile under the Supported Legacy APIs section and click it. Then click **APPLICATION Permissions** button, and select **Directory.ReadAll** permission and then add that permission using button at the bottom. 
4. Click the **Grant admin consent for {tenantname}** button so that you grant access for all users in this directory. 

Now you can add external Azure AD groups from the **Groups** tab of your API Management instance.

1. 选择“组”选项卡。
2. 选择“添加 AAD 组”按钮。
   ![“添加 AAD 组”按钮](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 选择要添加的组。
4. 按“选择”按钮。

创建外部 Azure AD 组之后，可以查看和配置其属性。 Select the name of the group from the **Groups** tab. From here, you can edit **Name** and **Description** information for the group.
 
配置的 Azure AD 实例中的用户现在可以登录开发人员门户。 他们可以查看和订阅可见的任何组。

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/> Developer portal - add Azure AD account authentication

In the developer portal, sign-in with AAD is possible with the **OAuth buttons** widget. The widget is already included on the sign-in page of the default developer portal content.

![AAD buttons widget](./media/api-management-howto-aad/portal-oauth-widget.png)

Although a new account will be automatically created whenever a new user signs in with AAD, you may consider adding the same widget to the sign-up page.

> [!IMPORTANT]
> You need to [republish the portal](api-management-howto-developer-portal-customize.md#publish) for the AAD changes to take effect.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Legacy developer portal - how to sign in with Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

使用前面部分中配置的 Azure AD 帐户登录开发人员门户：

1. 使用 Active Directory 应用程序配置中的登录 URL 打开新的浏览器窗口，并选择“Azure Active Directory”。

   ![登录页][api-management-dev-portal-signin]

1. 输入 Azure Active Directory 中某个用户的凭据，并选择“登录”。

   ![使用用户名和密码登录][api-management-aad-signin]

1. 如果需要其他信息，可能出现注册表单的提示。 完成注册表单并选择“登录”。

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
