---
title: 在 Azure Active Directory 中配置链接登录
description: 在 Azure AD 中配置链接登录。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 2269a8f7f58d35fee5e2ca30a77af5e8cba83678
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459328"
---
# <a name="configure-linked-sign-on"></a>配置链接登录

在应用程序管理的[快速入门系列](view-applications-portal.md)中，已了解如何使用 Azure AD 作为应用程序的标识提供者（IdP）。 在快速入门指南中，可以设置基于 SAML 的 SSO。 其他选项已**链接**。 本文详细介绍了链接选项。

使用 "**链接**" 选项，可以在用户在组织的["我的应用](https://myapplications.microsoft.com/)" 或 "Office 365 门户" 中选择应用时配置目标位置。

Link 选项非常有用的一些常见方案包括：
- 添加指向当前使用联合的自定义 web 应用程序的链接，例如 Active Directory 联合身份验证服务（AD FS）。
- 添加指向特定 SharePoint 页面或您只想在用户的访问面板上显示的其他网页的深层链接。
- 添加指向不需要身份验证的应用程序的链接。 
 
 "**链接**" 选项不通过 Azure AD 凭据提供登录功能。 但你仍可以使用**企业应用程序**的某些其他功能。 例如，可以使用审核日志并添加自定义徽标和应用名称。

## <a name="before-you-begin"></a>开始之前

若要快速加速，请在应用程序管理上演练[快速入门系列](view-applications-portal.md)。 在快速入门中配置单一登录的情况下，你还会找到**链接**选项。 

"**链接**" 选项不通过 Azure AD 提供登录功能。 当用户在[我的应用](https://myapplications.microsoft.com/)程序或 Microsoft 365 应用程序启动器上选择应用程序时，此选项只是设置将发送到的位置。

> [!IMPORTANT] 
> 在某些情况下，"**单一登录**" 选项将不会出现在 "**企业应用**程序" 中的应用程序的导航中。 
>
> 如果使用**应用注册**注册了应用程序，则默认情况下，单一登录功能设置为使用 OIDC OAuth。 在这种情况下，"**企业应用程序**" 下的导航中将不会显示 "**单一登录**" 选项。 使用**应用注册**添加自定义应用时，将在清单文件中配置选项。 若要了解有关清单文件的详细信息，请参阅[Azure Active Directory 应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)。 若要了解有关 SSO 标准的详细信息，请参阅[使用 Microsoft 标识平台进行身份验证和授权](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)。 
>
> 当应用程序托管在另一个租户中，或者如果你的帐户不具有所需的权限（全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者）时，导航中将缺少**单一登录**。 权限还可能会导致出现这样的情况：你可以打开**单一登录**但无法保存。 若要详细了解 Azure AD 管理角色，请参阅（ https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 。

### <a name="configure-link"></a>配置链接

若要设置应用的链接，请在 "**单一登录**" 页上选择 "**链接**"。 然后输入链接，并选择 "**保存**"。 需要有关在何处可以找到这些选项的提醒？ 查看[快速入门系列](view-applications-portal.md)。
 
配置应用后，将用户和组分配给它。 分配用户时，可以控制应用程序在 "[我的应用](https://myapplications.microsoft.com/)" 或 "Microsoft 365" 应用程序启动器上出现的时间。

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置用户帐户自动预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
