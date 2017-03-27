---
title: "排查动态组成员身份问题 | Microsoft 文档"
description: "有关 Azure AD 中组的动态成员身份的疑难解答提示。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 89bb04b6-a379-49c2-8465-fe386641816a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: 27f8d329c0dddd21cca7e3631594ab326f3610b2


---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>组的动态成员身份疑难解答
**我在组上配置了一个规则，但该组中的成员身份未更新**<br/>在“配置”选项卡中，检查“启用委派组管理”设置是否设为“是”。 仅当你已使用分配有 Azure Active Directory Premium 许可证的用户登录时，才能看到此设置。 检查规则中的用户属性值：是否有用户满足该规则？

**我配置了一条规则，但现在却删除了该规则的现有成员**<br/>这是预期的行为。 在启用或更改某个规则时，会删除组中的现有成员。 评估规则后返回的用户将作为成员添加到组中。     

**我在添加或更改规则后未立即看到成员身份变化，这是为什么？**<br/>专用成员身份评估定期在异步后台进程中执行。 该过程要花费多长时间取决于目录中的用户数，以及应用规则后创建的组的大小。 通常，用户数较少的目录在几分钟内就能看到组成员身份变化。 而具有大量用户的目录可能需要 30 分钟或更长时间才能填充信息。

### <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO2-->


