---
title: 添加应用程序时如何选择要使用的应用程序类型 | Microsoft Docs
description: 了解可以与 Azure AD 集成的应用程序支持类型及其相关的配置选项
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 5236e9620d68d2ac4dcc544482d244c1e998be9d
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365366"
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>添加应用程序时如何选择要使用的应用程序类型

本文将帮助你了解可以与 Azure AD 集成的四种主要应用程序类型：

* 支持的应用程序类型
* 可能选择某种应用程序的原因
* 如何配置这些应用程序的核心属性，如**预配**用户的方式，或要使用的**单一登录**技术。

## <a name="supported-application-types-in-azure-ad"></a>Azure AD 中支持的应用程序类型

Azure AD 支持使用“企业应用程序”下的“添加”功能添加的四种主要应用程序类型。 其中包括：

-   **Azure AD 库应用程序** - 已为启用单一登录而预集成到 Azure AD 的应用程序。

-   **应用程序代理应用程序** - 要从外部向其提供单一登录且在本地环境中运行的应用程序。

-   **以自定义方式开发的应用程序** - 组织要在 Azure AD 应用程序开发平台上开发的应用程序，但可能尚不存在。

-   **非库应用程序** - 引入自己的应用程序！ 任何所需的 Web 链接或任何呈现“用户名”和“密码”字段的应用程序，都支持 SAML 或 OpenID Connect 协议，或支持为实现单一登录而预集成到 Azure AD 的 SCIM。

## <a name="features-and-capabilities-supported-by-all-the-preceding-application-types"></a>上述所有应用程序类型都支持的特性和功能

Azure AD 中的上述四种应用程序类型都支持以下功能：

-   **快速入门** - 通过遵循[简单的部署步骤](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)快速开始使用应用程序

-   **常规属性管理** - 获取对应用程序的[直接的深层链接](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users)、[自定义应用程序的外观方案](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal)，或对所有用户[禁用应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)。

-   **用户和组管理** - 向应用程序[分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)用户和组，或[删除](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)应用程序的用户和组，并根据需要分配这些用户和组有权访问的特定应用程序角色

-   **自助服务应用程序访问** - 使用户可以通过直接添加应用程序或[加入启用自助服务的组](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)，从其应用程序访问面板对应用程序请求[自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)，在此过程中，可以选择性地要求进行业务批准

-   **登录日志** - 请参阅[对一个应用程序的所有登录](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)或对所有应用程序的所有登录。

-   **审核日志** - 请参阅[有关对一个应用程序（或所有应用程序）的修改的详细审核日志](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

-   **基于风险的条件性访问** -设置强大的[基于条件的访问规则](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)，当用户尝试登录到特定应用程序时，会强制执行该规则

-   **权限视图** - 查看目录中应用程序有权从单个位置访问的任何 [OAuth2 权限](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>特定应用程序类型支持的单一登录和预配模式

下表介绍了以上每种应用程序类型支持的单一登录和预配模式。 可使用此表帮助了解为支持特定目标而需添加的应用程序。

  ![应用类型表](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>如何选择单一登录模式

下面是 Azure AD 应用程序支持的**单一登录**模式。

-   **禁用 Azure AD 单一登录** - 如果尚未准备好将此应用程序与使用 Azure AD 的单一登录集成，或只是要进行测试，则选择禁用 Azure AD 单一登录**单一登录模式**

-   **链接登录** - 如果具有已连接到现有单一登录解决方案的应用程序，或只是想在用户的[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)或 [Office 365 应用程序启动器](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)中为其发布简单链接，则选择[链接登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)**单一登录模式**

-   **基于密码的登录** - 如果应用程序呈现 HTML 用户名和密码字段，且想要安全存储该用户名和密码，以便稍后将其重播到应用程序，则选择[基于密码的登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)**单一登录模式**

-   **基于 SAML 的登录** - 如果应用程序支持 SAML 或 OpenID Connect 协议，或希望能够根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色，则选择[基于 SAML 的登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)单一登录模式*

   >[!NOTE]
   >如果已为应用程序配置了应用程序代理，则此选项不可用。
   >
   >

-   **基于标头的登录** - 如果具有一个应用程序，该程序使用支持基于 HTTP 头的身份验证的 PingAccess，且想要对此身份验证执行单一登录，则选择此[基于标头的登录](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad)单一登录模式 

   >[!NOTE]
   >仅当为应用程序配置了应用程序代理和 PingAccess 时，此选项才可用。
   >
   >

-   **集成 Windows 身份验证** - 公开要对之执行单一登录的本地 WIA 应用程序时，选择[集成 Windows 身份验证](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)单一登录模式 

   >[!NOTE]
   >为应用程序配置应用程序代理时，此选项才可用。
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>适用于以自定义方式开发的应用程序的单一登录模式

通过[以自定义方式开发的应用程序](#_Custom-Developed_Applications)中的过程以自定义方式开发的应用程序还支持前面未列出的其他单一登录模式，这包括：

-   基于 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 的登录

-   基于 [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 的登录

-   基于 [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) 的登录

-   基于 [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) 的登录

阅读 [Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)，深入了解如何创建支持这些单一登录模式的以自定义方式开发的应用程序。

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>如何设置应用程序的单一登录模式

若要设置应用程序的“单一登录”模式，请按照以下说明进行操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要为其配置单一登录的应用程序。

7.  加载应用程序后，在应用程序的左侧导航菜单中，单击“单一登录”。

## <a name="how-to-choose-a-provisioning-mode"></a>如何选择预配模式

-   **手动预配** - 如果已有帐户，或想要在 Azure AD 之外管理此应用程序的帐户，请选择[手动](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes)预配模式。

-   **自动预配** - 如果想要对此应用程序的用户帐户启用自动的基于 API 的预配和/或取消预配，请选择[自动](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning)**预配模式** 

   >[!NOTE]
   >此选项仅对 [Azure AD 应用程序库](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery)中**特色**类别中的应用程序可用。
   >
   >

-   **基于 SCIM 的自动预配** - 如果应用程序支持 SCIM 协议以检测用户和组的更改，请使用[基于 SCIM 的自动预配](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning)，该预配可针对与 Azure AD 集成的任何应用程序的更改自动发出 

   >[!NOTE]
   >此选项未列为特定预配模式，但默认情况下为与 Azure AD 集成的所有应用程序启用。
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>如何设置应用程序的预配模式

若要设置应用程序的**预配**模式，请按照以下说明进行操作：

若要设置应用程序的“单一登录”模式，请按照以下说明进行操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要为其配置预配的应用程序。

7.  加载应用程序后，在应用程序的左侧导航菜单中，单击“预配”。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](manage-apps/what-is-application-management.md)
