---
title: 如何确定要使用哪种单一登录方法 | Microsoft Docs
description: 了解 Azure AD 支持的单一登录模式，以及如何为感兴趣的应用程序选择模式。
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 79fe30fbe0baed9d62e6bc3328e754958516ef70
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "36335401"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>如何确定要使用哪种单一登录方法

本文介绍 Azure AD 支持的单一登录模式，以及如何为感兴趣的应用程序选择模式。

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>特定应用程序类型支持的单一登录和预配模式

下表介绍了以上每种应用程序类型支持的单一登录和预配模式。 可使用此表帮助了解为支持特定目标而需添加的应用程序。

  ![Ap 类型表](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>如何选择单一登录模式

下面是 Azure AD 应用程序支持的**单一登录**模式。

-   **禁用 Azure AD 单一登录** - 如果尚未准备好将此应用程序与使用 Azure AD 的单一登录集成，或只是要进行测试，则选择禁用 Azure AD 单一登录**单一登录模式**

-   **链接登录** - 如果具有已连接到现有单一登录解决方案的应用程序，或只是想在用户的[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)或 [Office 365 应用程序启动器](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)中为其发布简单链接，则选择[链接登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)**单一登录模式**

-   **基于密码的登录** - 如果应用程序呈现 HTML 用户名和密码字段，且想要安全存储该用户名和密码，以便稍后将其重播到应用程序，则选择[基于密码的登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)**单一登录模式**

-   **基于 SAML 的登录** - 如果应用程序支持 SAML 或 OpenID Connect 协议，或希望能够根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色，则选择[基于 SAML 的登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work)单一登录模式（**注意：*如果为应用程序配置了应用程序代理，则此选项不可用）*

-   **基于标头的登录** - 如果具有一个应用程序，该程序使用支持基于 HTTP 头的身份验证的 PingAccess，且想要对此身份验证执行单一登录，则选择此[基于标头的登录](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad)单一登录模式（**注意：*仅当为应用程序配置了应用程序代理和 PingAccess 时，此选项才可用）*

-   **集成 Windows 身份验证** - 公开要对之执行单一登录的本地 WIA 应用程序时，选择[集成 Windows 身份验证](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)单一登录模式（**注意：*为应用程序配置应用程序代理时，此选项才可用）*

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

6.  选择要配置单一登录的应用程序

7.  加载应用程序后，在应用程序的左侧导航菜单中，单击“单一登录”。

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

