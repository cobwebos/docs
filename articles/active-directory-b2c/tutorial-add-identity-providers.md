---
title: 教程 - 将标识提供程序添加到应用程序 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何使用 Azure 门户向 Azure Active Directory B2C 应用程序添加标识提供程序。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.openlocfilehash: 2a1843f941c6abc46928b38a66025fa87c4bcea5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757309"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>教程：将标识提供程序添加到 Azure Active Directory B2C 应用程序

在应用程序中，不妨允许用户使用不同的标识提供程序登录。 标识提供程序创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 可使用 Azure 门户向[用户流](active-directory-b2c-reference-policies.md)添加 Azure Active Directory (Azure AD) B2C 支持的标识提供程序。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建标识提供程序应用程序
> * 向租户添加标识提供程序
> * 向用户流添加标识提供程序

通常在应用程序中只使用一个标识提供程序，但可以视需要添加更多标识提供程序。 本教程介绍了如何将 Azure AD 标识提供程序和 Facebook 标识提供程序添加到应用程序。 视需要将这两个标识提供程序添加到应用程序。 也可以添加其他标识提供程序，如 [Amazon](active-directory-b2c-setup-amzn-app.md)、[Github](active-directory-b2c-setup-github-app.md)、[Google](active-directory-b2c-setup-goog-app.md)、[LinkedIn](active-directory-b2c-setup-li-app.md)、[Microsoft](active-directory-b2c-setup-msa-app.md) 或 [Twitter](active-directory-b2c-setup-twitter-app.md)。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。 

## <a name="create-applications"></a>创建应用程序

标识提供程序应用程序提供标识符和密钥，以启用与 Azure AD B2C 租户之间的通信。 在本教程的这一部分中，创建 Azure AD 应用程序和 Facebook 应用程序，从中可获取用于将标识提供程序添加到租户中的标识符和密钥。 若要只添加其中一个标识提供程序，只需为相应提供程序创建应用程序。

### <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

若要让用户从 Azure AD 登录，需要在 Azure AD 租户中注册应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请务必使用包含 Azure AD 租户的目录，具体方法是单击顶部菜单中的“目录和订阅筛选器”，再选择包含 Azure AD 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”。
4. 选择“新建应用程序注册”。
5. 输入应用程序的名称。 例如，`Azure AD B2C App`。
6. 对于“应用程序类型”，选择 `Web app / API`。
7. 对于“登录 URL”，输入以下 URL（全部为小写字母），并将 `your-B2C-tenant-name` 替换为你的 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    例如，`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
    
    现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。

8. 单击“创建”。 复制“应用程序 ID”供后续使用。
9. 选择应用程序，然后选择“设置”。
10. 选择“密钥”，输入密钥说明，选择持续时间，然后单击“保存”。 复制密钥值，以便稍后在本教程中使用。

### <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

必须创建 Facebook 应用程序，才能将 Facebook 帐户用作 Azure AD B2C 中的标识提供程序。 如果还没有 Facebook 帐户，可以在 [https://www.facebook.com/](https://www.facebook.com/) 获取。

1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
2. 如果以前没有登录过，需要以 Facebook 开发人员身份注册。 若要注册，请选择页面右上角的“注册”，接受 Facebook 策略，并完成注册步骤。
3. 选择“我的应用”，然后单击“添加新应用”。 
4. 输入“显示名称”和有效的“联系人电子邮件”。
5. 单击“创建应用程序 ID”。 系统可能要求你接受 Facebook 平台策略，并完成联机安全检查。
6. 选择“设置” > “基本”。
7. 选择“类别”，例如 `Business and Pages`。 此值是 Facebook 必需的，但不用于 Azure AD B2C。
8. 在页面底部，选择“添加平台”，然后选择“网站”。
9. 在“站点 URL”中，输入 `https://your-tenant-name.b2clogin.com/`，将 `your-tenant-name` 替换为你的租户名称。 为“隐私策略 URL”输入一个 URL，例如 `http://www.contoso.com`。 策略 URL 是继续提供应用程序的隐私信息的页面。
10. 选择“保存更改”。
11. 在页面的顶部，复制“应用 ID”的值。 
12. 单击“显示”，并复制“应用程序密码”的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据。
13. 选择“产品”，然后在“Facebook 登录”下选择“设置”。
14. 在“Facebook 登录”下选择“设置”。
15. 在“有效的 OAuth 重定向 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 单击页面底部的“保存更改”。
16. 若要让 Facebook 应用程序对 Azure AD B2C 可用，请单击页面右上角的“状态”选择器，并将它设置为“开”。 单击“确认”。 此时，“状态”应从“开发”变为“实时”。

## <a name="add-the-identity-providers"></a>添加标识提供程序

为要添加的标识提供程序创建应用程序后，将标识提供程序添加到租户。

### <a name="add-the-azure-active-directory-identity-provider"></a>添加 Azure Active Directory 标识提供程序

1. 请务必使用包含 Azure AD B2C 租户的目录，具体方法是单击顶部菜单中的“目录和订阅筛选器”，再选择包含 Azure AD B2C 租户的目录。
2. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
3. 选择“标识提供者”，然后选择“添加”。
4. 输入“名称”。 例如，输入“Contoso Azure AD”。
5. 选择“标识提供者类型”，选择“Open ID 连接(预览版)”并单击“确定”。
6. 单击“设置此标识提供者”
7. 对于“元数据 URL”，输入以下 URL，并将 `your-AD-tenant-domain` 替换为 Azure AD 租户的域名。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如，`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`。

8. 对于“客户端 ID”，输入先前记录的应用程序 ID；对于“客户端密码”，输入先前记录的密钥值。
9. （可选）输入 Domain_hint 的值。 例如，`ContosoAD`。 
10. 单击“确定”。
11. 选择“映射此标识提供者的声明”并设置以下声明：
    
    - 在“用户 ID”中输入 `oid`。
    - 在“显示名称”中输入 `name`。
    - 在“名字”中输入 `given_name`。
    - 在“姓氏”中输入 `family_name`。
    - 在“电子邮件”中输入 `unique_name`。

12. 单击“确定”，并单击“创建”保存配置。

### <a name="add-the-facebook-identity-provider"></a>添加 Facebook 标识提供程序

1. 选择“标识提供者”，然后选择“添加”。
2. 输入“名称”。 例如，输入“Facebook”。
3. 选择“标识提供者类型”，选择“Facebook”，并单击“确定”。
4. 选择“创建此标识提供程序”，并输入先前记录为“客户端 ID”的应用程序 ID。 输入先前记录为“客户端密码”的应用程序密码。
5. 单击“确定”，并单击“创建”以保存 Facebook 配置。

## <a name="update-the-user-flow"></a>更新用户流

在本教程中，你已完成先决条件的一部分，创建了名为“B2C_1_signupsignin1”的注册和登录用户流。 在此部分中，将标识提供程序添加到 B2C_1_signupsignin1 用户流。

1. 依次选择“用户流(策略)”和“B2C_1_signupsignin1”用户流。
2. 依次选择“标识提供程序”和所添加的“Facebook”和“Contoso Azure AD”标识提供程序。
3. 选择“保存”。

### <a name="test-the-user-flow"></a>测试用户流

1. 在创建的用户流的“概述”页上，选择“运行用户流”。
2. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
3. 单击“运行用户流”，再使用前面添加的标识提供程序登录。
4. 对添加的其他标识提供程序重复执行第 1 步到第 3 步。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建标识提供程序应用程序
> * 向租户添加标识提供程序
> * 向用户流添加标识提供程序

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中自定义应用程序的用户界面](tutorial-customize-ui.md)