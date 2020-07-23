---
title: 从 Azure AD 应用库配置 OpenID/OAuth 应用程序 | Microsoft Docs
description: 从 Azure AD 应用库配置 OpenID/OAuth 应用程序的步骤。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 13c3a7f8376d4c852a74be75e323c6bb042b5407
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610983"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>从 Azure AD 应用库配置 OpenID/OAuth 应用程序

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>从库添加 OpenID 应用程序的过程

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”按钮](common/select-azuread.png))

2. 转到“企业应用程序”   > “所有应用程序”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 选择对话框顶部的“新建应用程序”。 

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入应用程序的名称。 在结果面板中选择所需的应用程序并注册该应用程序。

    ![结果列表中的 Openid](common/search-new-app.png)

    > [!NOTE]
    > 对于 OpenID Connect 和 OAuth 应用，“添加”按钮默认处于禁用状态。  在这里，租户管理员应该选择“注册”按钮并向应用程序提供许可。 该应用程序随即会添加到客户租户中，可在租户中进行配置。 无需显式添加应用程序。

    ![“添加”按钮](./media/openidoauth-tutorial/addbutton.png)

5. 选择注册链接时，会重定向到 Azure Active Directory (Azure AD) 页以便提供登录凭据。

6. 身份验证成功后，请在许可页上接受许可。 随后会显示应用程序主页。

    > [!NOTE]
    > 只能添加一个应用程序实例。 如果已添加了一个实例，然后尝试再次提供许可，则不会在租户中再次添加实例。 因此逻辑上，只能在租户中使用一个应用实例。

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID Connect 的身份验证流

最基本的登录流包含以下步骤：

![使用 OpenID Connect 的身份验证流](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>多租户应用程序
多租户应用程序预定在许多组织中使用，而不仅是在单个组织中使用。 它们通常是由独立软件供应商 (ISV) 编写的软件即服务 (SaaS) 应用程序。

多租户应用程序需要在使用它们的每个目录中预配。 它们需要用户或管理员的许可才能注册。 当在目录中注册应用程序并向其授予对 Graph API 或者另一可能的 Web API 的访问权限时，此许可过程即已开始。 当其他组织的用户或管理员注册使用应用程序时，某个对话框会显示应用程序所需的权限。

然后，用户或管理员可以向应用程序提供许可。 这种许可使应用程序能够访问指定的数据，并最终在目录中注册该应用程序。

> [!NOTE]
> 若要使应用程序可供多个目录中的用户使用，需要一种机制来确定用户在哪个租户中。 单租户应用程序只需在其自身的目录中查找用户。 多租户应用程序需要从 Azure AD 中的所有目录来识别特定用户。
>
> 为此，Azure AD 提供了一个任何多租户应用程序都可以在其中对登录请求进行定向的通用身份验证终结点，而不是提供特定于租户的终结点。 对于 Azure AD 中的所有目录，此终结点为 `https://login.microsoftonline.com/common`。 特定于租户的终结点可能为 `https://login.microsoftonline.com/contoso.onmicrosoft.com`。
>
> 在开发应用程序时考虑通用终结点尤为重要。 在登录、注销和令牌验证期间需要使用必要的逻辑来处理多租户。

默认情况下，Azure AD 提倡使用多租户应用程序。 此类应用程序可跨组织轻松访问，在接受许可后即可轻松使用。

## <a name="consent-framework"></a>同意框架

可以使用 Azure AD 许可框架来开发多租户 Web 应用程序和本机客户端应用程序。 这些应用程序允许与应用程序所注册到的租户不同的 Azure AD 租户中的用户帐户登录。 它们可能还需要访问 Web API，例如：
- Microsoft 图形 API（以便访问 Azure AD、Intune，以及 Office 365 中的服务）。
- 其他 Microsoft 服务的 API。
- 你自己的 Web API。

该框架基于某个用户或管理员，该用户或管理员许可某个应用程序在其目录中注册。 注册可能涉及到访问目录数据。 获得许可后，客户端应用程序可以代表该用户调用 Microsoft 图形 API，并根据需要使用信息。

[Microsoft 图形 API](https://developer.microsoft.com/graph/) 可用于访问 Office 365 中的数据，例如：

- 来自 Exchange 的日历和邮件。
- 来自 SharePoint 的站点和列表。
- 来自 OneDrive 的文档。
- 来自 OneNote 的笔记本。
- 来自 Planner 的任务。
- 来自 Excel 的工作簿。

图形 API 来可用于访问 Azure AD 中的用户和组，以及其他 Microsoft 云服务中的其他数据对象。

以下步骤说明应用程序开发人员和用户如何使用许可体验：

1. 假设某个 Web 客户端应用程序需要请求资源或 API 的特定访问权限。 Azure 门户用于在配置时声明权限请求。 这些配置与其他配置设置一样，将会成为应用程序的 Azure AD 注册的一部分。 对于权限请求路径，需要按照以下步骤操作：

    a. 单击菜单左侧的“应用注册”  ，然后在搜索框中键入应用程序名称以打开应用程序。

    ![图形 API](./media/openidoauth-tutorial/application.png)

    b. 单击“查看 API 权限”  。

    ![图形 API](./media/openidoauth-tutorial/api-permission.png)

    c. 单击“添加权限”  。

    ![图形 API](./media/openidoauth-tutorial/add-permission.png)

    d. 单击“Microsoft Graph”  。

    ![图形 API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. 从“委托的权限”  和  “应用程序权限”中选择所需的选项。

    ![图形 API](./media/openidoauth-tutorial/graphapi.png)

2. 假设应用程序的权限已更新。 该应用程序正在运行，并且某个用户即将首次使用该应用程序。 首先，该应用程序需要从 Azure AD 的 /authorize 终结点获取授权代码。 然后，可以使用该授权代码获取新的访问令牌和刷新令牌。

3. 如果用户尚未经过身份验证，Azure AD 的 /authorize 终结点会提示登录。

    ![身份验证](./media/openidoauth-tutorial/authentication.png)

4. 用户登录后，Azure AD 将决定是否要向该用户显示许可页。 此决定基于该用户（或其组织的管理员）是否已授予应用程序许可。

   如果尚未授予许可，Azure AD 会提示用户授予许可，并显示运行该应用程序所需的权限。 许可对话框中显示的权限与在 Azure 门户中的委托权限中选择的权限相匹配。

    ![同意页](./media/openidoauth-tutorial/consentpage.png)

有些权限可由普通用户许可， 有些权限则需要租户管理员许可。

## <a name="difference-between-admin-consent-and-user-consent"></a>管理员许可与用户许可的区别

作为管理员，还可以代表租户中的所有用户同意应用程序的委派权限。 管理许可可防止针对租户中的每个用户显示许可对话框。 具有管理员角色的用户可在 Azure 门户中提供许可。 在应用程序的“设置”页中，选择“所需权限” > “授予管理员同意”。   

![“授予权限”按钮](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> 使用 ADAL.js 的单页应用程序 (SPA) 目前要求使用“授予管理员同意”按钮授予显式许可  。 否则，在请求访问令牌时应用程序会失败。

仅限应用的权限始终需要租户管理员的同意。 如果应用程序请求仅限应用的权限，当用户尝试登录应用程序时，会显示一条错误消息。 该消息指出该用户无法许可。

如果应用程序使用需要管理员许可的权限，则需要提供某种表示，例如可供管理员启动操作的按钮或链接。 应用程序针对此操作发送的请求是一个普通的 OAuth2/OpenID Connect 授权请求。 此请求包含 *prompt=admin_consent* 查询字符串参数。

在管理员已许可且系统已在客户的租户中创建服务主体之后，后续登录请求就不再需要 *prompt=admin_consent* 参数。 由于管理员已确定可接受请求的权限，因此从该时间点之后，不再提示租户中的任何其他用户许可。

租户管理员可以禁用普通用户同意应用程序的能力。 如果禁用此功能，则始终需要管理员同意，才能在租户中使用应用程序。 如果想要在禁用最终用户许可的情况下测试应用程序，可以在 [Azure 门户](https://portal.azure.com/)中找到配置开关。 该开关位于“企业应用程序”下的[用户设置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)部分。 

*prompt=admin_consent* 参数还可以由请求不需要管理员许可的权限的应用程序使用。 例如，某个应用程序需要如下所述的体验：租户管理员“注册”一次，在此之后不再提示其他用户确认许可。

假设某个应用程序需要管理员许可并且管理员登录而没有发送 *prompt=admin_consent* 参数。 当管理员成功地许可该应用程序时，该许可仅适用于其用户帐户。 普通用户仍然无法登录或许可该应用程序。 如果想要让租户管理员浏览应用程序，然后允许其他用户访问，则此功能就很有用。
