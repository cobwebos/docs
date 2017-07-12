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
ms.date: 09/22/2016
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c4920107b5d05716021bcef80d3c52c25f6eade
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016


---
<a id="managing-owners-for-a-group" class="xliff"></a>

# 管理组的所有者
当资源所有者将资源访问权限分配给一个 Azure AD 组，该组所有者即可管理组的成员身份。 实际上，资源所有者是将其资源的用户访问权限委派给了组的所有者。

<a id="assigning-group-ownership" class="xliff"></a>

## 分配组所有权
**将所有者添加到组**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开你的组织的目录。
2. 选择“组”选项卡，然后打开要将所有者添加到的组。
3. 选择“添加所有者”。
4. 在“添加所有者”页上，选择要添加为该组所有者的用户，并确保该用户名已添加到“选定”窗格中。

**从组中删除所有者**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开你的组织的目录。
2. 选择“组”选项卡，然后打开要从中删除所有者的组。
3. 选择“所有者”选项卡。
4. 选择要从该组中删除的所有者，然后选择“删除”。

<a id="additional-information" class="xliff"></a>

## 其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)


