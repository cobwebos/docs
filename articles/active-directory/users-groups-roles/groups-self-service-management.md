---
title: 设置自助服务组管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中创建和管理安全组或 Office 365 组，并可以请求安全组或 Office 365 组成员身份
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541deb5cf44ad5440e31641b673ed5da5b5d2b26
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768544"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>在 Azure 活动目录中设置自助服务组管理 

您可以允许用户在 Azure 活动目录 （Azure AD） 中创建和管理自己的安全组或 Office 365 组。 组的所有者可以批准或拒绝成员资格请求，并可以委派对组成员身份的控制。 自助服务组管理功能不适用于启用邮件的安全组或通讯组列表。

## <a name="self-service-group-membership-defaults"></a>自助服务组成员身份默认值

在 Azure 门户或使用 Azure AD PowerShell 中创建安全组时，只有该组的所有者可以更新成员资格。 ["Access"面板](https://account.activedirectory.windowsazure.com/r#/joinGroups)中的自助服务创建的安全组和所有 Office 365 组可供所有用户加入，无论是所有者批准还是自动批准。 在"访问"面板中，您可以在创建组时更改成员资格选项。

在 | 安全组默认行为 | Office 365 组默认行为
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 只有所有者可以添加成员<br>可见但无法加入访问面板 | 开放以加入所有用户
[Azure 门户](https://portal.azure.com) | 只有所有者可以添加成员<br>可见但无法加入访问面板<br>在组创建时不会自动分配所有者 | 开放以加入所有用户
[访问面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 开放以加入所有用户<br>创建组时可以更改成员资格选项 | 开放以加入所有用户<br>创建组时可以更改成员资格选项

## <a name="self-service-group-management-scenarios"></a>自助服务组管理方案

* **委派的组管理**例如，管理公司正在使用的 SaaS 应用程序的访问权限的管理员。 由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。 管理员将应用程序的访问权限分配给新组，并将已访问该应用程序的所有人添加到组中。 然后，业务所有者可以添加更多用户，而这些用户会自动预配到该应用程序中。 业务所有者无需等待管理员管理用户的访问权限。 如果管理员向其他业务组中的经理授予相同的权限，则该人员还可以管理其自己的组成员的访问。 企业所有者和经理都不能查看或管理彼此的组成员身份。 该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。
* **自助服务组管理**此方案的一个示例是两个用户，他们都有独立设置的 SharePoint Online 网站。 他们希望让彼此的团队访问他们的网站。 要实现此目的，他们可以在 Azure AD 中创建一个组，各自在 SharePoint Online 中选择该组并为该组提供对其站点的访问权限。 当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。 后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。 SaaS 应用程序的管理员可以将此应用程序的访问权限添加到 SharePoint Online 站点。 从那以后，他批准的任何请求都将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## <a name="make-a-group-available-for-user-self-service"></a>使组可用于用户自助服务

1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择 **"组**"，然后选择 **"常规**设置"。
1. 集**所有者可以在"访问面板"中管理组成员身份请求**。 **Yes**
1. 将**访问面板中的组限制为****"否**"
1. 如果设置 **"用户可以在 Azure 门户中创建安全组"，** 或者**用户可以在 Azure 门户中创建 Office 365 组**，

    - **是**：允许 Azure AD 组织中的所有用户创建新的安全组并将成员添加到这些组。 这些新组也会显示在其他所有用户的“访问面板”中。 如果组上的策略设置允许，则其他用户可以创建加入这些组的请求
    - **否**：用户无法创建组，也不能更改其所有者的现有组。 不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。

您还可以使用**可以将成员分配为 Azure 门户中的组所有者的所有者，** 也可以将**成员指定为 Azure 门户中的组所有者的所有者**，以便对用户的自助服务组管理实现更精细的访问控制。

当用户可以创建组时，允许组织中的所有用户创建新组，然后作为默认所有者，可以将成员添加到这些组。 不能指定可以创建自己的组的个人。 只能指定个人，使另一个组成员成为组所有者。

> [!NOTE]
> 用户需要 Azure 活动目录高级版 （P1 或 P2） 许可证才能请求加入安全组或 Office 365 组，并且所有者需要批准或拒绝成员资格请求。 如果没有 Azure 活动目录高级许可证，用户仍然可以在"访问面板"中管理其组，但不能创建在访问面板中需要所有者批准的组，并且无法请求加入组。 

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](groups-settings-cmdlets.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
