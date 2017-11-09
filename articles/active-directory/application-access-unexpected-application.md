---
title: "应用程序列表中意料之外的应用程序 | Microsoft Docs"
description: "如何查看租户中的全部应用程序，并了解应用程序如何显示在“企业应用程序”下的“所有应用程序”列表中"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f8136cb066fa6e3e4a8dd06e34b9f866e3916f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-application-in-my-applications-list"></a>应用程序列表中意料之外的应用程序

本文介绍应用程序如何显示在“企业应用程序”下的“所有应用程序”列表中。 

## <a name="how-to-see-all-applications-in-your-tenant"></a>如何查看租户中的全部应用程序

若要查看租户中的所有应用程序，需要使用“筛选器”控件，在“所有应用程序”列表下显示“所有应用程序”。 为此，请执行以下步骤：

1.  打开[“Azure 门户”](https://portal.azure.com/)，以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

6.  在“所有应用程序列表”顶部，单击以使用“筛选器”控件。

7.  在“筛选器”边栏选项卡上，将“显示”选项设为“所有应用程序”。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>为什么在所有应用程序列表中出现某个特定应用程序？

筛选为“所有应用程序”时，“所有应用程序列表”会显示租户中的每个服务主体对象。 服务主体对象以多种方式出现在此列表中：

1.  添加应用程序库的任何应用程序时，包括：

   1. **Azure AD 库应用程序** - 已为启用单一登录而预集成到 Azure AD 的应用程序。

   2. **应用程序代理应用程序** - 要从外部向其提供单一登录且在本地环境中运行的应用程序。

   3. **以自定义方式开发的应用程序** - 组织要在 Azure AD 应用程序开发平台上开发的应用程序，但可能尚不存在。

   4. **非库应用程序** - 引入自己的应用程序！ 任何所需的 Web 链接或任何呈现“用户名”和“密码”字段的应用程序，都支持 SAML 或 OpenID Connect 协议，或支持为实现单一登录而预集成到 Azure AD 的 SCIM。

2.  注册或登录与 Azure Active Directory 集成的<sup></sup>第三方应用程序时。 相应的一个例子是 [Smartsheet](https://app.smartsheet.com/b/home) 或 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)。

3.  注册第一方应用程序或者将用户或组的许可证添加到其中时，如 [Microsoft Office 365](http://products.office.com/)。

4.  当通过使用[应用程序注册表](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)创建以自定义方式开发的应用程序，来添加新应用程序注册之时。

5.  当通过使用 [V2.0 应用程序注册门户](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal)创建以自定义方式开发的应用程序，来添加新应用程序注册之时。

6.  添加正在使用 Visual Studio 的 [ASP.net 身份验证方法](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)或[连接的服务](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)开发的应用程序时。

7.  使用 [Azure AD PowerShell 模块](/powershell/azure/install-adv2?view=azureadps-2.0)创建服务主体对象时。

8.  以管理员身份[同意某应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)使用租户中的数据时。

9.  [用户同意某应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)使用租户中的数据时。

10. 启用某些在租户中存储数据的服务时。 相应的一个示例是密码重置，密码重置是作为服务主体进行建模的，以便安全存储密码重置策略。

要详细深入地了解如何将应用添加到目录，请参阅[如何以及为何将应用程序添加到 Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我要删除特定用户或组到应用程序的分配

若要删除用户或组对应用程序的分配，请按照[在 Azure Active Directory 中从企业应用删除用户或组分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)一文中所列的步骤进行操作。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>我要禁用每个用户对应用程序的所有访问权限

若要禁用所有用户对应用程序的登录，请遵循[在 Azure Active Directory 中对企业应用禁用用户登录](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)一文中所列出的步骤。

## <a name="i-want-to-delete-an-application-entirely"></a>我要彻底删除应用程序

若要“删除应用程序”，请按照以下说明进行操作：

1.  打开[“Azure 门户”](https://portal.azure.com/)，以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要删除的应用程序。

7.  加载应用程序后，在应用程序顶部的“概述”边栏选项卡中，单击“删除”图标。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我要禁用用户未来针对应用程序的所有同意操作

针对整个目录禁用用户同意操作，可防止最终用户同意任何应用程序。 管理员仍可代表用户执行同意操作。 若要深入了解应用程序同意，以及为何要或不这样操作，请参阅[了解用户和管理员同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)。

若要**禁用用户未来在整个目录中执行的同意操作**，请根据以下说明进行操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，以**全局管理员**身份登录

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“用户设置”。

6.  通过将“用户可以允许应用访问其数据”切换为“否”来禁用用户未来的所有同意操作，并单击“保存”按钮。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](active-directory-enable-sso-scenario.md)
