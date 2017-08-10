---
title: "使用组来管理对 SaaS 应用程序的访问 | Microsoft 文档"
description: "如何在 Azure Active Directory Premium 或 Basic 中使用组来分配与 Azure Active Directory 集成的 SaaS 应用程序的访问权限。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro;oldportal
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: d350011ee9fc5ced9ddb16993f68d3c840a645a5
ms.contentlocale: zh-cn
ms.lasthandoff: 08/08/2017

---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>使用组来管理对 SaaS 应用程序的访问
通过 Azure AD Premium 或 Azure AD Basic 许可证使用 Azure Active Directory (Azure AD) 时，可以使用组来分配与 Azure AD 集成的 SaaS 应用程序的访问权限。 例如，如果要为营销部分配使用五个不同 SaaS 应用程序所需的访问权限，则可以创建一个包含销售部用户的组，并将该组分配给营销部所需的这五个 SaaS 应用程序。 这样就可以在一个位置管理营销部的成员身份，从而节省了时间。 之后，当将用户添加为营销组的成员时，这些用户将分配到应用程序；当将用户从营销组中删除时，也会从应用程序中删除为其分配的权限。

> [!IMPORTANT]
> Microsoft 建议使用 Azure 门户而不是本文中引用的 Azure 经典门户通过 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。 

此功能适用于可从 Azure AD 应用程序库中添加的数百个应用程序。

**为组分配对 SaaS 应用程序的访问权限**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，在左侧导航栏上选择“Active Directory”。
2. 选择“目录”选项卡，并打开要在其中为组分配对 Saas 应用程序的访问权限的目录。
3. 选择“应用程序”选项卡。 选择从应用程序库中添加的应用程序，并单击“用户和组”选项卡。
4. 在“用户和组”选项卡的“开头为”字段中，输入要为其分配访问权限的组的名称，并单击右上角的复选标记。 只需键入组名称的开头部分。
5. 选择组，并选择“分配访问权限”按钮。 当看见确认消息时选择“是”。 目前应用程序的基于组的分配不支持嵌套的组成员身份。
6. 还可以查看哪些用户分配到了该应用程序，无论这些用户是直接分配还是按组成员身份分配。 为此，请将“显示”下拉列表从“组”更改为“所有用户”。 此列表显示目录中的用户，以及每个用户是否已分配到该应用程序。 此列表还显示分配的用户是直接分配到该应用程序（分配类型显示为“直接”），还是按组成员身份分配（分配类型显示为“继承”）。

> [!NOTE]
> 仅当启用了 Azure AD Premium 或 Azure AD Basic 后，才会看到“用户和组”选项卡。
>
>

### <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

