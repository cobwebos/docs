---
title: 在 Azure Active Directory 中设置自助式应用程序访问管理 | Microsoft Docs
description: 在 Azure Active Directory 中创建和管理安全组或 Office 365 组，并可以请求安全组或 Office 365 组成员身份
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: ee116762945f01c5c8031b97e0e09fa6c6ae6d7f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762007"
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>为自助服务组管理设置 Azure Active Directory
用户可以在 Azure Active Directory (Azure AD) 中创建和管理其自己的安全组或 Office 365 组。 用户也可以请求安全组或 Office 365 组成员身份，组所有者可以批准或拒绝成员身份。 可以将组成员身份的日常控制委托给了解该成员身份的业务上下文的人员。 自助组管理功能仅适用于安全组和 Office 365 组，而不适用于已启用邮件的安全组或通讯组列表。

自助服务组管理当前可用于两个基本方案：委派组管理和自助服务组管理。

* **委托组管理** — 以管理对公司所用 SaaS 应用程序的访问权限的管理员为例。 由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。 管理员将该应用程序的访问权限分配给新组，并添加到所有人员已访问该应用程序的组。 然后，业务所有者可以添加更多用户，而这些用户会自动预配到该应用程序中。 业务所有者无需等待管理员管理用户的访问权限。 如果管理员将相同的权限授予不同业务组中的管理员，则该人员也可以管理自己的用户的访问权限。 业务所有者和管理员都无法查看或管理彼此的用户。 该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。
* **自助组管理** — 以下是该方案的一个示例：两个用户都拥有独立设置的 SharePoint Online 站点。 他们都想为对方的团队提供对其站点的访问权限。 要实现此目的，他们可以在 Azure AD 中创建一个组，各自在 SharePoint Online 中选择该组并为该组提供对其站点的访问权限。 当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。 后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。 SaaS 应用程序的管理员可以将此应用程序的访问权限添加到 SharePoint Online 站点。 从那以后，他批准的任何请求都将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## <a name="make-a-group-available-for-user-self-service"></a>使组可用于用户自助服务
1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”，然后选择“组设置”。
3. 将“启用自助服务组管理”设置为“是”。
4. 将“用户可以创建安全组”或“用户可以创建 Office 365 组”设置为“是”。
  * 启用这些设置时，将允许目录中的所有用户创建新的安全组并在这些组中添加成员。 这些新组也会显示在其他所有用户的“访问面板”中。 如果组的策略设置允许，其他用户可以创建加入这些组的请求。 
  * 禁用这些设置时，用户将无法创建组，也无法更改其拥有的现有组。 不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。

还可以使用“可以管理安全组的用户”和“可以管理 Office 365 组的用户”对用户的自助服务组管理功能进行更精细的访问控制。 启用“用户可以创建组”后，将允许租户中的所有用户创建新组并在这些组中添加成员。 通过将其设置为“部分”，可限制为只有一组有限的用户可进行组管理。 将此开关设为“部分”后，必须先将用户添加到 SSGMSecurityGroupsUsers 组，这些用户才能创建新组并在这些组中添加成员。 通过将“可对安全组使用自助服务的用户”和“可管理 Office 365 组的用户”设为“全部”，即可允许租户中的所有用户创建新组。

还可以使用“可以管理安全组的组”或“可以管理 Office 365 组的组”来指定其成员可以使用自助服务的单个组。

## <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问权限](active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
