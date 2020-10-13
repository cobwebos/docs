---
title: '了解 Azure Active Directory 中应用的基于密码的单一登录 (SSO) '
description: '了解 Azure Active Directory 中应用的基于密码的单一登录 (SSO) '
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: 9b48bc62fc0548c0c4f431e71598fdfa6850de13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598341"
---
# <a name="understand-password-based-single-sign-on"></a>了解基于密码的单一登录

在应用程序管理的 [快速入门系列](view-applications-portal.md) 中，已了解如何使用 Azure AD 作为标识提供程序 (IdP) 应用程序。 在快速入门指南中，你将配置基于 SAML 或 OIDC 的 SSO。 另一种方法是基于密码的 SSO。 本文更详细地介绍了基于密码的 SSO 选项。 

此选项可用于包含 HTML 登录页的任何网站。 基于密码的 SSO 也称为密码存储。 通过基于密码的 SSO，你可以管理不支持联合身份验证的 web 应用程序的用户访问权限和密码。 在多个用户需要共享单个帐户（例如，组织的社交媒体应用帐户）时，这也很有用。

基于密码的 SSO 非常适合于快速将应用程序集成到 Azure AD，并使你能够：

- 通过安全地存储和重播用户名和密码，为用户启用单一登录

- 支持需要多个登录字段的应用程序，这适用于不只需要用户名和密码字段才能登录的应用程序

- 自定义用户在输入其凭据时在 [我的应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 上看到的 "用户名" 和 "密码" 字段的标签

- 允许用户为手动键入的任何现有应用程序帐户提供自己的用户名和密码。

- 允许业务组的成员使用[自助应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许管理员指定个人或组在使用更新凭据功能登录到应用程序时使用的用户名和密码 

## <a name="before-you-begin"></a>在开始之前

使用 Azure AD 作为标识提供者 (IdP) 并配置单一登录 (SSO) 可以简单或复杂，这取决于所使用的应用程序。 某些应用程序只能用几个操作进行配置。 其他人需要深入配置。 若要快速增加知识，请在应用程序管理中演练 [快速入门系列](view-applications-portal.md) 。 如果要添加的应用程序很简单，则可能不需要阅读本文。 如果要添加的应用程序需要自定义配置，并且你需要使用基于密码的 SSO，则本文适用于你。

> [!IMPORTANT] 
> 在某些情况下，" **单一登录** " 选项将不会出现在 " **企业应用**程序" 中的应用程序的导航中。 
>
> 如果使用 **应用注册** 注册了应用程序，则默认情况下，单一登录功能将配置为使用 OIDC OAuth。 在这种情况下，"**企业应用程序**" 下的导航中将不会显示 "**单一登录**" 选项。 使用 **应用注册** 添加自定义应用时，将在清单文件中配置选项。 若要了解有关清单文件的详细信息，请参阅 [Azure Active Directory 应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要了解有关 SSO 标准的详细信息，请参阅 [使用 Microsoft 标识平台进行身份验证和授权](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 当应用程序托管在另一个租户中，或者如果你的帐户没有服务主体) 的所需权限 (全局管理员、云应用程序管理员、应用程序管理员或所有者时，导航中将缺少 **单一登录** 。 权限还可能会导致出现这样的情况：你可以打开 **单一登录** 但无法保存。 若要详细了解 Azure AD 管理角色，请参阅 (https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。


## <a name="basic-configuration"></a>基本配置

在 [快速入门系列](view-applications-portal.md)中，你已了解如何将应用添加到你的租户，以便 Azure AD 知道该应用程序是否被用作应用程序 (IdP) 的标识提供者。 某些应用已预先配置，它们显示在 Azure AD 库中。 其他应用不在库中，您必须创建一个通用应用并进行手动配置。 基于密码的 SSO 选项可能不可用，具体取决于应用程序。 如果在应用的 "单一登录" 页上看不到 "基于密码的选项" 列表，则它将不可用。

> [!IMPORTANT]
> 基于密码的 SSO 需要 "我的应用" 浏览器扩展。 若要了解详细信息，请参阅 [计划应用部署](access-panel-deployment-plan.md)。

基于密码的 SSO 的配置页面非常简单。 它仅包含应用程序使用的登录页的 URL。 此字符串必须是包含 "用户名" 输入字段的页面。

输入 URL 后，选择 " **保存**"。 Azure AD 分析用户名和密码输入字段的登录页的 HTML。 如果尝试成功，则已完成。
 
下一步是 [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)。 分配用户和组后，你可以提供凭据，以便在用户登录到应用程序时使用。 选择 " **用户和组**"，选中用户或组所在行的复选框，然后选择 " **更新凭据**"。 最后，输入要用于该用户或组的用户名和密码。 否则，系统将在启动时提示用户输入凭据。
 

## <a name="manual-configuration"></a>手动配置

如果 Azure AD 的分析尝试失败，你可以手动配置登录。

1. 在 " ** \<application name> 配置**" 下，选择 "**配置 \<application name> 密码" "单一登录设置**" 以显示 "**配置登录**" 页。 

2. 选择 **"手动检测登录字段"**。 将显示说明手动检测登录字段的其他说明。

   ![手动配置基于密码的单一登录](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 选择 **"捕获登录字段"**。 此时将在新选项卡中打开 "捕获状态" 页，显示消息 " **元数据捕获当前正在进行**"。

4. 如果新选项卡中显示了 " **需要应用扩展** " 框，请选择 " **立即安装** " 以安装 " **我的应用安全登录扩展** 浏览器扩展"。  (浏览器扩展需要 Microsoft Edge、Chrome 或 Firefox。 ) 然后安装、启动和启用该扩展，并刷新 "捕获状态" 页。

   然后，浏览器扩展将打开一个显示输入的 URL 的选项卡。
5. 在包含所输入 URL 的选项卡中，完成登录过程。 填写 "用户名" 和 "密码" 字段，然后尝试登录。  (不必提供正确的密码。 ) 

   会出现一个提示，要求您保存捕获的登录字段。
6. 选择“确定”  。 浏览器扩展更新 "捕获状态" 页，其中包含 **为应用程序更新的消息元数据**。 "浏览器" 选项卡关闭。

7. 在 Azure AD **配置登录** "页上，选择 **" 确定 "，我可以成功登录到应用**。

8. 选择“确定”  。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置用户帐户自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
