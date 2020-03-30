---
title: 修复动态组成员身份的问题 - Azure AD |微软文档
description: Azure 活动目录中动态组成员身份的故障排除提示
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026548"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>排除和解决组问题

## <a name="troubleshooting-group-creation-issues"></a>解决组创建问题

**我禁用了 Azure 门户中的安全组创建，但仍可以通过 Powershell 创建组****用户可以在 Azure 门户中的 Azure 门户设置中创建安全组**，以控制非管理员用户是否可以在 Access 面板或 Azure 门户中创建安全组。 它不通过 Powershell 控制安全组创建。

要禁用 Powershell 中非管理员用户的组创建：
1. 验证是否允许非管理员用户创建组：
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. 如果它返回 `UsersPermissionToCreateGroupsEnabled : True`，则非管理员用户可以创建组。 若要禁用此功能，请执行以下操作：
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**尝试在 Powershell 中创建动态组时，我收到允许的最大组错误**<br/>
如果在 Powershell 中收到一条消息，指示_动态组策略已达到最大允许的组计数_，这意味着已达到租户中动态组的最大限制。 每个租户的动态组的最大数量为 5，000。

要创建任何新的动态组，首先需要删除某些现有动态组。 没有办法增加限制。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>组的动态成员身份疑难解答

**我在组上配置了一个规则，但该组中的成员身份未更新**<br/>
1. 验证规则中用户或设备属性的值。 确保有用户满足规则。 对于设备，请检查设备属性以确保任何同步的属性包含预期值。<br/>
2. 检查成员资格处理状态以确认是否已完成。 您可以在"**概述"** 页上检查[成员资格处理状态](groups-create-rule.md#check-processing-status-for-a-rule)和上次更新日期。

如果一切看上去正常，请为要填充的组预留一些时间。 根据租户的大小，首次填充或者在更改规则后，最长可能需要 24 小时才能在组中完成填充。

**我配置了一条规则，但现在却删除了该规则的现有成员**<br/>这是预期的行为。 在启用或更改某个规则时，会删除组中的现有成员。 评估规则后返回的用户将作为成员添加到组中。

**我在添加或更改规则后未立即看到成员身份变化，这是为什么？**<br/>专用成员身份评估定期在异步后台进程中执行。 该过程要花费多长时间取决于目录中的用户数，以及应用规则后创建的组的大小。 通常，用户数较少的目录在几分钟内就能看到组成员身份变化。 而具有大量用户的目录可能需要 30 分钟或更长时间才能填充信息。

**如何强制立即处理组？**<br/>
目前，无法自动触发要按需处理的组。 但是，您可以通过更新成员资格规则来在末尾添加空格来手动触发后处理。  

**遇到了规则处理错误**<br/>下表列出了常见动态成员资格规则错误以及更正方法。

| 规则分析器错误 | 错误用法 | 更正的用法 |
| --- | --- | --- |
| 错误: 不支持的属性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>请确保该属性在[支持的属性列表](groups-dynamic-membership.md#supported-properties)中。 |
| 错误: 不支持对属性使用运算符。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>属性类型不支持所使用的运算符（在此示例中，-contains 不能用于布尔类型）。 请对该属性类型使用正确的运算符。 |
| 错误: 查询编译错误。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. 缺少操作员。 使用 -and 或 -or 这两个联接谓词<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. 正则表达式中的错误与 -匹配一起使用<br>(user.userPrincipalName -match ".*@domain.ext")<br>或：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [什么是 Azure 活动目录？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure 活动目录集成](../hybrid/whatis-hybrid-identity.md)