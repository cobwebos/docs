---
title: 在 Azure AD 权限管理中更改访问包的资源角色-Azure Active Directory
description: 了解如何在 Azure Active Directory 权限管理中更改现有访问包的资源角色。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261887"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中更改访问包的资源角色

作为访问包管理器，你可以随时更改访问包中的资源，而无需担心设置用户对新资源的访问权限，或者从以前的资源中删除其访问权限。 本文介绍如何更改现有访问包的资源角色。

此视频概述了如何更改访问包。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>检查资源的目录

如果需要将资源添加到访问包，应检查目录中是否提供了所需的资源。 如果你是访问包管理器，则不能将资源添加到目录，即使你拥有这些资源也是如此。 您只能使用目录中提供的资源。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开目录。

1. 在左侧菜单中，单击 "**资源**" 以查看此目录中的资源列表。

    ![目录中的资源列表](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 如果你是访问包管理器并且需要将资源添加到目录，则可以要求目录所有者添加这些资源。

## <a name="add-resource-roles"></a>添加资源角色

资源角色是与资源关联的权限的集合。 向用户提供资源以供用户请求的方式是将资源角色添加到访问包。 您可以为组、团队、应用程序和 SharePoint 站点添加资源角色。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在左侧菜单中，单击 "**资源角色**"。

1. 单击 "**添加资源角色**" 以打开 "将资源角色添加到 access 包" 页面。

    ![访问包-添加资源角色](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 根据你是要添加组、团队、应用程序还是 SharePoint 站点，请执行下列资源角色部分之一中的步骤。

## <a name="add-a-group-or-team-resource-role"></a>添加组或团队资源角色

当分配了访问包时，可以让权利管理自动将用户添加到 Microsoft 团队中的组或团队。 

- 如果组或团队是访问包的一部分，并且将用户分配到该访问包，则会将该用户添加到该组或团队（如果尚未存在）。
- 当用户的访问包分配过期时，它们将从组或团队中删除，除非它们当前已分配给另一个访问包，其中包含相同的组或团队。

你可以选择任何[Azure AD 安全组或 Office 365 组](../fundamentals/active-directory-groups-create-azure-portal.md)。 管理员可以将任何组添加到目录;如果目录所有者是组的所有者，则可以将任何组添加到目录中。 选择组时，请牢记以下 Azure AD 约束：

- 当用户（包括来宾）作为成员添加到组或团队时，他们可以查看该组或团队的所有其他成员。
- Azure AD 无法使用 Azure AD Connect 更改从 Windows Server Active Directory 同步的组的成员身份，或者无法更改在 Exchange Online 中创建为通讯组的组的成员身份。  
- 动态组的成员身份无法通过添加或删除成员进行更新，因此动态组成员身份不适用于授权管理。

有关详细信息，请参阅[比较组](https://docs.microsoft.com/office365/admin/create-groups/compare-groups)和[Office 365 组和 Microsoft 团队](https://docs.microsoft.com/microsoftteams/office-365-groups)。

1. 在 "**将资源角色添加到访问包**" 页面上，单击 "**组和团队**" 以打开 "选择组" 窗格。

1. 选择要包括在访问包中的组和团队。

    ![访问包-添加资源角色-选择组](./media/entitlement-management-access-package-resources/group-select.png)

1. 单击“选择”。

    选择组或团队后，"**子类型**" 列将列出以下子类型之一：

    |  |  |
    | --- | --- |
    | 安全性 | 用于授予对资源的访问权限。 |
    | 分发 | 用于向一组人员发送通知。 |
    | O365 | 不是团队启用的 Office 365 组。 用于在公司内部和外部用户之间的协作。 |
    | 团队 | 启用了团队的 Office 365 组。 用于在公司内部和外部用户之间的协作。 |

1. 在 "**角色**" 列表中，选择 "**所有者**" 或 "**成员**"。

    通常选择成员角色。 如果选择 "所有者" 角色，则允许用户添加或删除其他成员或所有者。

    ![访问包-添加组或团队的资源角色](./media/entitlement-management-access-package-resources/group-role.png)

1. 单击“添加”。

    添加了对访问包的现有分配的任何用户将自动成为该组或团队的成员。

## <a name="add-an-application-resource-role"></a>添加应用程序资源角色

可以 Azure AD 让用户在分配访问包时，自动为用户分配对 Azure AD 企业应用程序的访问权限，包括 SaaS 应用程序和联合到 Azure AD 的组织的应用程序。 对于通过联合单一登录与 Azure AD 相集成的应用程序，Azure AD 将为分配给该应用程序的用户颁发联合身份验证令牌。

应用程序可以有多个角色。 将应用程序添加到访问包时，如果该应用程序有多个角色，则需要为这些用户指定适当的角色。 如果要开发应用程序，可以在[如何：配置在用于企业应用程序的 SAML 令牌中颁发的角色声明](../develop/active-directory-enterprise-app-role-management.md)中了解有关如何将这些角色添加到应用程序的详细信息。

一旦应用程序角色是访问包的一部分：

- 向用户分配该访问包时，会将该用户添加到该应用程序角色（如果尚未存在）。
- 当用户的访问包分配过期时，将从应用程序中删除其访问权限，除非他们分配到另一个包含该应用程序角色的访问包。

选择应用程序时，需要注意以下事项：

- 应用程序还可以将组分配给其角色。  你可以选择在访问包中添加组来代替应用程序角色，但是，应用程序在 "我的访问门户" 中作为访问包的一部分对用户不可见。

1. 在 "**将资源角色添加到 access 包**" 页面上，单击 "**应用程序**" 以打开 "选择应用程序" 窗格。

1. 选择要包括在访问包中的应用程序。

    ![访问包-添加资源角色-选择应用程序](./media/entitlement-management-access-package-resources/application-select.png)

1. 单击“选择”。

1. 在 "**角色**" 列表中，选择应用程序角色。

    ![访问包-添加应用程序的资源角色](./media/entitlement-management-access-package-resources/application-role.png)

1. 单击“添加”。

    如果添加了对访问包的现有分配，则这些用户将自动获得对此应用程序的访问权限。

## <a name="add-a-sharepoint-site-resource-role"></a>添加 SharePoint 站点资源角色

当向用户分配访问包时，Azure AD 可以自动为用户分配对 SharePoint Online 网站或 SharePoint Online 网站集的访问权限。

1. 在 "**将资源角色添加到 access 包**" 页面上，单击 " **sharepoint 站点**" 以打开 "选择 sharepoint Online 网站" 窗格。

1. 选择要包含在访问包中的 SharePoint Online 站点。

    ![访问包-添加资源角色-选择 SharePoint Online 网站](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. 单击“选择”。

1. 在 "**角色**" 列表中，选择 "SharePoint Online 站点" 角色。

    ![访问包-添加 SharePoint Online 网站的资源角色](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. 单击“添加”。

    添加具有对访问包的现有分配的任何用户时，将自动获得对此 SharePoint Online 站点的访问权限。

## <a name="remove-resource-roles"></a>删除资源角色

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**访问包**"，然后打开访问包。

1. 在左侧菜单中，单击 "**资源角色**"。

1. 在资源角色列表中，找到要删除的资源角色。

1. 单击省略号（ **...** ），然后单击 "**删除资源角色**"。

    删除此资源角色后，对该访问包具有现有分配权限的任何用户都将自动撤消其访问权限。

## <a name="when-changes-are-applied"></a>应用更改时

在 "授权管理" 中，Azure AD 将每天多次处理访问包中的分配和资源的批量更改。 因此，如果您执行分配或更改访问包的资源角色，则可能需要长达24小时才能在 Azure AD 中进行该更改，以及将这些更改传播到其他 Microsoft Online Services 或连接的 SaaS 应用程序所需的时间量。些. 如果你所做的更改只影响几个对象，则更改可能只需要几分钟的时间才能应用到 Azure AD 中，之后其他 Azure AD 组件将检测到该更改并更新 SaaS 应用程序。 如果更改会影响数千个对象，则所做的更改将需要更长的时间。 例如，如果你有一个具有2个应用程序的访问包和100个用户分配，并且你决定将 SharePoint 站点角色添加到访问包，则可能会有延迟，直到所有用户都是该 SharePoint 站点角色的一部分。 可以通过 Azure AD 审核日志、Azure AD 预配日志和 SharePoint 站点审核日志来监视进度。

删除团队成员时，也会将其从 Office 365 组中删除。 从团队的聊天功能中删除可能会延迟。 有关详细信息，请参阅[组成员身份](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 创建基本组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
- [如何：为企业应用程序配置 SAML 令牌中颁发的角色声明](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online 简介](/sharepoint/introduction)