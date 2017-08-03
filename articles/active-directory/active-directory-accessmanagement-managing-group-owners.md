---
title: "使用组进行访问管理的后续步骤 | Microsoft 文档"
description: "有关如何管理安全组，以及如何使用这些组来管理对资源的访问的高级操作指南。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 82fbeb379e90add09f7c569111053f6e9b1bc9c5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="managing-owners-for-a-group"></a>管理组的所有者
当资源所有者将资源访问权限分配给一个 Azure AD 组，该组所有者即可管理组的成员身份。 实际上，资源所有者是将其资源的用户访问权限委派给了组的所有者。

> [!IMPORTANT]
> Microsoft 建议使用 Azure 门户而不是本文中引用的 Azure 经典门户通过 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。 

## <a name="assigning-group-ownership"></a>分配组所有权
**将所有者添加到组**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开组织的目录。
2. 选择“组”选项卡，打开要将所有者添加到的组。
3. 选择“添加所有者”。
4. 在“添加所有者”页上，选择要添加为该组所有者的用户，并确保该用户名已添加到“选定”窗格中。

**从组中删除所有者**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开组织的目录。
2. 选择“组”选项卡，并打开要从中删除所有者的组。
3. 选择“所有者”选项卡。
4. 选择要从该组中删除的所有者，并选择“删除”。

## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

