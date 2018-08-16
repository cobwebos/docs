---
title: 排查动态组成员身份问题 | Microsoft 文档
description: 有关 Azure AD 中组的动态成员身份的疑难解答提示。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 82a5c57ce874e77a7912945a6fca07acee339197
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444481"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>组的动态成员身份疑难解答

**我在组上配置了一个规则，但该组中的成员身份未更新**<br/>检查规则中的用户属性值：是否有用户满足该规则？ 如果一切看上去正常，请为要填充的组预留一些时间。 根据租户的大小，首次填充或者在更改规则后，最长可能需要 24 小时才能在组中完成填充。

**我配置了一条规则，但现在却删除了该规则的现有成员**<br/>这是预期的行为。 在启用或更改某个规则时，会删除组中的现有成员。 评估规则后返回的用户将作为成员添加到组中。

**我在添加或更改规则后未立即看到成员身份变化，这是为什么？**<br/>专用成员身份评估定期在异步后台进程中执行。 该过程要花费多长时间取决于目录中的用户数，以及应用规则后创建的组的大小。 通常，用户数较少的目录在几分钟内就能看到组成员身份变化。 而具有大量用户的目录可能需要 30 分钟或更长时间才能填充信息。

**遇到了规则处理错误**<br/>下表列出了常见动态成员资格规则错误以及更正方法。

| 规则分析器错误 | 错误用法 | 更正的用法 |
| --- | --- | --- |
| 错误: 不支持的属性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>请确保该属性在[支持的属性列表](groups-dynamic-membership.md#supported-properties)中。 |
| 错误: 不支持对属性使用运算符。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>属性类型不支持所使用的运算符（在此示例中，-contains 不能用于布尔类型）。 请对该属性类型使用正确的运算符。 |
| 错误: 查询编译错误。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1.缺少运算符。 使用 -and 或 -or 这两个联接谓词<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.与 -match 一起使用的正则表达式出错<br>(user.userPrincipalName -match ".*@domain.ext")<br>或：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](../fundamentals/active-directory-manage-groups.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../connect/active-directory-aadconnect.md)
