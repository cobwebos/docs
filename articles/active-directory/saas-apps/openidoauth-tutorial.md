---
title: 从 Azure AD 应用库配置 OpenID/OAuth 应用程序的步骤 | Microsoft Docs
description: 从 Azure AD 应用库配置 OpenID/OAuth 应用程序的步骤。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 69e9d66458409bbc744416a58ceb508349418a76
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019547"
---
# <a name="steps-to-configure-an-openidoauth-application-from-azure-ad-app-gallery"></a>从 Azure AD 应用库配置 OpenID/OAuth 应用程序的步骤

## <a name="process-of-open-id-application-addition-from-gallery"></a>从库中添加 Open ID 应用程序的过程

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮](./media/openidoauth-tutorial/tutorial_general_01.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡](./media/openidoauth-tutorial/tutorial_general_02.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](./media/openidoauth-tutorial/tutorial_general_03.png)

4. 在搜索框中，键入**应用程序名称**，从结果面板中选择**所需的应用程序**并注册到应用程序。

    ![添加应用程序](./media/openidoauth-tutorial/addfromgallery.png)

    > [!NOTE]
    > 对于 Open ID Connect 和 OAuth 应用，“添加”按钮默认处于禁用状态。 在这里，租户管理员应该单击“注册”按钮并向应用程序提供同意。 接着应用程序就会添加到客户租户中（无需显式添加），并进行配置。

    ![“添加”按钮](./media/openidoauth-tutorial/addbutton.png)

5. 单击“注册”链接时，系统会将你重定向到 Azure AD 页以便你提供登录凭据。

6. 身份验证成功后，用户必须接受同意页上的同意事项，之后将显示应用程序主页。

    > [!NOTE]
    > 客户仅可以添加一个应用程序实例。 如果已添加了一个，并尝试再次提供同意，则不会在租户中再次添加。 因此逻辑上，客户只能在租户中使用一个应用实例。

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID Connect 的身份验证流

最基本的登录流包含以下步骤 - 下面详细描述了每个步骤。

![使用 OpenID Connect 的身份验证流](./media/openidoauth-tutorial/authenticationflow.png)

* **多租户应用程序** - 多租户应用程序预定在许多组织中使用，而不仅是在单个组织中使用。 它们通常是由独立软件供应商 (ISV) 编写的软件即服务 (SaaS) 应用程序。 多租户应用程序需要设置在将使用它们的每个目录中，需要经过用户或管理员许可才能注册它们。 当在目录中注册应用程序并向其授予对 Graph API 或者另一可能的 Web API 的访问权限时，此许可过程即已开始。 当其他组织的用户或管理员注册使用应用程序时，会向他们显示一个对话框，其中显示了应用程序要求的权限。 然后，用户或管理员可以许可应用程序的要求，这会向应用程序授予对指定数据的访问权限，并最终在其目录中注册该应用程序。

    > [!NOTE]
    > 如果要使应用程序可供多个目录中的用户使用，需要一种机制来确定用户在哪个租户中。 单租户应用程序只需要在其自己的目录中查找用户，而多租户应用程序需要从 Azure AD 中的所有目录来识别特定用户。 为此，Azure AD 提供了一个任何多租户应用程序都可以在其中对登录请求进行定向的通用身份验证终结点，而不是提供特定于租户的终结点。 对于 Azure AD 中的所有目录，此终结点为 [https://login.microsoftonline.com/common](https://login.microsoftonline.com/common)，而特定于租户的终结点可能为 [https://login.microsoftonline.com/contoso.onmicrosoft.com](https://login.microsoftonline.com/contoso.onmicrosoft.com)。 在开发应用程序时考虑通用终结点尤为重要，因为在登录、注销和令牌验证期间需要必要的逻辑来处理多租户。

默认情况下，Azure AD 团队推出多租户应用程序，因为该应用程序可以跨各种组织轻松地访问，并且可在接受同意后方便使用。

## <a name="what-is-consent-framework"></a>什么是同意框架？

使用 Azure AD 许可框架可以轻松开发多租户 Web 应用程序和本机客户端应用程序。 这些应用程序允许与应用程序所注册到的租户不同的 Azure AD 租户中的用户帐户登录。 这些帐户除了需要访问你自己的 Web API 以外，可能还需要访问 Microsoft 图形 API 等 Web API（以访问 Azure Active Directory、Intune，以及 Office 365 中的服务）和其他 Microsoft 服务 API。 该框架基于某个用户或管理员，该用户或管理员允许某个应用程序在其目录中注册，这可能涉及到访问目录数据。 获得同意后，该客户端应用程序将能够代表该用户调用 Microsoft Graph API，并根据需要使用信息。

[Microsoft Graph API](https://graph.microsoft.io/) 可用来访问 Office 365 中的数据（例如日历、来自 Exchange 的邮件、来自 SharePoint 的站点和列表、来自 OneDrive 的文档、来自 OneNote 的笔记本、来自 Planner 的任务、来自 Excel 的工作簿，等等）、Azure AD 中的用户和组以及更多 Microsoft 云服务中的其他数据对象。

以下步骤说明应用程序开发人员和用户如何使用许可体验。

1. 假设某个 Web 客户端应用程序需要请求资源/API 的特定访问权限。 Azure 门户用于在配置时声明权限请求。 这些配置与其他配置设置一样，将会成为应用程序的 Azure AD 注册的一部分：

    ![图形 API](./media/openidoauth-tutorial/graphapi.png)

2. 考虑已更新应用程序的权限，该应用程序正在运行，并且某个用户即将首次使用该应用程序。 首先，应用程序需要从 Azure AD 的 /authorize 终结点获取授权代码。 然后，可以使用该授权代码获取新的访问令牌和刷新令牌。

3. 如果用户尚未经过身份验证，Azure AD 的 /authorize 终结点会提示登录。

    ![身份验证](./media/openidoauth-tutorial/authentication.png)

4. 用户登录后，Azure AD 将决定是否要向该用户显示同意页。 此决定基于该用户（或其组织的管理员）是否已授予应用程序许可。 如果尚未授予许可，Azure AD 会提示用户授予许可，并显示运行该应用程序所需的权限。 同意对话框中显示的权限集与在 Azure 门户中的“委托权限”中选择的权限集相匹配。

    ![同意页](./media/openidoauth-tutorial/consentpage.png)

有些权限可由普通用户同意，有些则需要租户管理员同意。

## <a name="whats-the-difference-between-admin-consent-and-user-consent"></a>管理员同意和用户同意的区别是什么？

作为管理员，还可以代表租户中的所有用户同意应用程序的委派权限。 管理同意可防止针对租户中的每个用户显示同意对话框，可由具有管理员角色的用户在 Azure 门户中执行。 在应用程序的“设置”页中，单击“所需权限”，再单击“授予权限”按钮。

![授予权限](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> 使用 ADAL.js 的单页应用程序 (SPA) 目前要求使用“授予权限”按钮授予显式许可。 否则，在请求访问令牌时应用程序会失败。

仅限应用的权限始终需要租户管理员的同意。 如果应用程序请求仅限应用的权限，当用户尝试登录应用程序时，会显示一条错误消息，指出该用户无法同意。

如果应用程序使用需要管理员同意的权限，则需要提供某种表示，例如可供管理员发起操作的按钮或链接。 应用程序针对此操作发送的请求是一个普通的 OAuth2/OpenID Connect 授权请求，但此请求也包含 prompt=admin_consent 查询字符串参数。 在管理员已同意且系统已在客户的租户中创建服务主体之后，后续登录请求就不再需要 prompt=admin_consent 参数。 由于管理员已确定可接受请求的权限，因此从该时间点之后，不再提示租户中的任何其他用户同意。 租户管理员可以禁用普通用户同意应用程序的能力。 如果禁用此功能，则始终需要管理员同意，才能在租户中使用应用程序。 如果想要在禁用最终用户许可的情况下测试应用程序，可以在 [Azure 门户](https://portal.azure.com/)的“企业应用程序”下的[用户设置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)部分中找到配置开关

prompt=admin_consent 参数还可以由请求不需要管理员同意的权限的应用程序使用。 何时会使用此功能的一个示例是当应用程序需要如下所述的体验时，即：租户管理员“注册”一次，在此之后不再提示其他用户确认同意。

如果某个应用程序需要管理员同意并且管理员登录而没有发送 prompt=admin_consent 参数，则当管理员成功地向该应用程序表示同意时，它仅适用于其用户帐户。 普通用户仍然无法登录或同意该应用程序。 如果想要让租户管理员浏览应用程序，然后允许其他用户访问，则此功能就很有用。