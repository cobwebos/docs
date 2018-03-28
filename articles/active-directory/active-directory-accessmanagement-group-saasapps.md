---
title: 使用组来管理对 SaaS 应用程序的访问 | Microsoft 文档
description: 如何在 Azure Active Directory Premium 或 Basic 中使用组来分配与 Azure Active Directory 集成的 SaaS 应用程序的访问权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 4aee5b28fd42be98fac8f9c7d61538319e3efd9a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>使用组来管理对 SaaS 应用程序的访问
通过 Azure AD Premium 或 Azure AD Basic 许可证使用 Azure Active Directory (Azure AD) 时，可以使用组来分配与 Azure AD 集成的 SaaS 应用程序的访问权限。 例如，如果要为营销部分配使用五个不同 SaaS 应用程序所需的访问权限，则可以创建一个包含销售部用户的组，然后将该组分配给营销部所需的这五个 SaaS 应用程序。 这样就可以在一个位置管理营销部的成员身份，从而节省了时间。 之后，当将用户添加为营销组的成员时，这些用户将分配到应用程序；当将用户从营销组中删除时，也会从应用程序中删除为其分配的权限。 此功能适用于可从 Azure AD 应用程序库中添加的数百个应用程序。

> [!IMPORTANT]
> 只有在开始试用 Azure AD Premium 或购买 Azure AD Premium/Azure AD Basic 许可证后，才能使用此功能。 基于组的分配仅适用于安全组。 目前应用程序的基于组的分配不支持嵌套的组成员身份。

**授予用户或组对 SaaS 应用程序的访问权限的具体步骤**

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)内，选择“企业应用程序”。
2. 选择从应用程序库添加的应用程序，即可打开它。
3. 依次选择“用户和组”和“添加用户”。
4. 在“添加分配”上，选择“用户和组”，打开“用户和组”选择列表。
6. 选择任意多个组或用户，再单击或点击“选择”，将它们添加到“添加分配”列表。 还可以在此阶段向用户分配角色。
7. 选择“分配”，将用户或组分配到选定的企业应用程序。

### <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
