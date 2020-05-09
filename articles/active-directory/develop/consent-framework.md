---
title: Azure AD 同意框架
titleSuffix: Microsoft identity platform
description: 了解 Azure Active Directory 中的许可框架，以及如何使用它轻松开发多租户 Web 应用程序和本机客户端应用程序。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e706c0eeb848b6cd14a3c14de821ca59a9c52ee9
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611358"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory 许可框架

使用 Azure Active Directory (Azure AD) 许可框架可以轻松开发多租户 Web 应用程序和本机客户端应用程序。 这些应用程序允许用户帐户从与应用程序所注册到的租户不同的 Azure AD 租户登录。 这些帐户除了需要访问你自己的 Web API 以外，可能还需要访问 Microsoft Graph API 等 Web API（以访问 Azure AD、Intune，以及 Office 365 中的服务）和其他 Microsoft 服务 API。

该框架基于某个用户或管理员，该用户或管理员允许某个应用程序在其目录中注册，这可能涉及到访问目录数据。 例如，如果某个 Web 客户端应用程序需要从 Office 365 中读取关于用户的日历信息，则该用户首先需要许可该客户端应用程序。 同意后，该客户端应用程序能够代表该用户调用 Microsoft Graph API，并根据需要使用日历信息。 [Microsoft Graph API](https://developer.microsoft.com/graph) 可用来访问 Office 365 中的数据（例如来自 Exchange 的日历和邮件、来自 SharePoint 的站点和列表、来自 OneDrive 的文档、来自 OneNote 的笔记本、来自 Planner 的任务以及来自 Excel 的工作簿）、Azure AD 中的用户和组以及更多 Microsoft 云服务中的其他数据对象。

同意框架使用公共或机密客户端，建立在 OAuth 2.0 及其各种流程的基础之上，例如，代码授权和客户端凭据授权。 通过使用 OAuth 2.0，Azure AD 可生成多种不同类型的客户端应用程序（例如手机、平板电脑、服务器上的客户端应用程序或 Web 应用程序），并获取对所需资源的访问权限。

有关将许可框架与 OAuth2.0 授权配合使用的详细信息，请参阅[使用 OAuth 2.0 和 Azure AD 授权访问 Web 应用程序](v2-oauth2-auth-code-flow.md)及 [Azure AD 的身份验证方案](authentication-scenarios.md)。 有关通过 Microsoft Graph 获取 Office 365 的授权访问权限的信息，请参阅[使用 Microsoft Graph 进行应用身份验证](https://developer.microsoft.com/graph/docs/authorization/auth_overview)。

## <a name="consent-experience---an-example"></a>同意体验 - 示例

以下步骤说明应用程序开发人员和用户如何使用同意体验。

1. 假设某个 Web 客户端应用程序需要请求资源/API 的特定访问权限。 下一部分将介绍如何执行此配置，但实质上，配置时需使用 Azure 门户来声明权限请求。 这些配置与其他配置设置一样，将会成为应用程序的 Azure AD 注册的一部分：

    ![针对其他应用程序的权限](./media/consent-framework/permissions.png)

1. 考虑已更新应用程序的权限，该应用程序正在运行，并且某个用户即将首次使用该应用程序。 首先，应用程序需要从 Azure AD 的 `/authorize` 终结点获取授权代码。 然后，可以使用该授权代码获取新的访问令牌和刷新令牌。

1. 如果用户尚未经过身份验证，Azure AD 的 `/authorize` 终结点会提示用户登录。

    ![用户或管理员登录到 Azure AD](./media/consent-framework/usersignin.png)

1. 用户登录后，Azure AD 将决定是否要向该用户显示同意页。 此决定基于该用户（或其组织的管理员）是否已授予应用程序许可。 如果尚未授予许可，Azure AD 会提示用户授予许可，并显示运行该应用程序所需的权限。 许可对话框中显示的权限集与在 Azure 门户中的“委托权限”  中选择的权限集相匹配。

    ![显示“同意”对话框中显示的权限的示例](./media/consent-framework/consent.png)

1. 用户授予许可后，授权代码会返回到应用程序，应用程序可凭此获取访问令牌和刷新令牌。 有关此流的详细信息，请参阅 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

1. 作为管理员，还可以代表租户中的所有用户同意应用程序的委派权限。 管理许可可防止针对租户中的每个用户显示许可对话框，可通过具有管理员角色的用户在 [Azure 门户](https://portal.azure.com)中执行。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure AD 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

    **同意应用的委托权限**

   1. 转到应用程序的“API 权限”  页
   1. 单击“授予管理员同意”  按钮。

      ![授予显式管理许可权限](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > 使用 ADAL.js 的单页应用程序 (SPA) 目前要求使用“授予权限”按钮授予显式许可  。 否则，在请求访问令牌时应用程序会失败。

## <a name="next-steps"></a>后续步骤

* 请参阅[如何将应用转换为多租户应用](howto-convert-app-to-be-multi-tenant.md)
* 有关详细信息，请参阅[在授权代码授权流期间如何在 OAuth 2.0 协议层支持同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
