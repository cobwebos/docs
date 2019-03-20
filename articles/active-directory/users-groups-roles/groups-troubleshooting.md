---
title: 解决组动态成员身份相关问题 - Azure Active Directory | Microsoft Docs
description: 有关 Azure AD 中组的动态成员身份的疑难解答提示。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0594d99874ea9bb83673013a9a03272edcd8ce0b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897667"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>排查和解决组问题

## <a name="troubleshooting-group-creation-issues"></a>组创建问题疑难解答
**我已禁用 Azure 门户中创建的安全组，但仍可以通过 Powershell 创建组****用户可以在 Azure 门户中创建安全组**设置在 Azure 门户的控件中是否非管理员用户可以在访问面板或 Azure 门户中创建安全组。 它不会控制通过 Powershell 创建安全组。

若要禁用组创建有关在 Powershell 中的非管理员用户：
1. 验证是否允许非管理员用户创建组：
   
   ```
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. 如果它返回 `UsersPermissionToCreateGroupsEnabled : True`，则非管理员用户可以创建组。 若要禁用此功能，请执行以下操作：
  
   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**我收到允许错误，尝试在 Powershell 中创建动态组时最大组**<br/>
如果你收到一条消息中，该值指示 Powershell_最大允许组计数达到动态组策略_，这意味着已在租户中的动态组达到最大限制。 每个租户的动态组的最大数量为 5,000。

若要创建任何新的动态组，将首先需要删除某些现有的动态组。 没有方法来提高限制。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>组的动态成员身份疑难解答

**我在组上配置了一个规则，但该组中的成员身份未更新**<br/>
1. 验证的用户或设备属性在规则中的值。 请确保有用户满足该规则。 对于设备，检查设备属性，以确保任何已同步的属性包含预期值。<br/>
2. 检查成员身份处理状态来确认它已完成。 你可以检查[成员身份处理状态](groups-create-rule.md#check-processing-status-for-a-rule)和的上次更新日期上**概述**组页。

如果一切看上去正常，请为要填充的组预留一些时间。 根据租户的大小，首次填充或者在更改规则后，最长可能需要 24 小时才能在组中完成填充。

**我配置了一条规则，但现在却删除了该规则的现有成员**<br/>这是预期的行为。 在启用或更改某个规则时，会删除组中的现有成员。 评估规则后返回的用户将作为成员添加到组中。

**我在添加或更改规则后未立即看到成员身份变化，这是为什么？**<br/>专用成员身份评估定期在异步后台进程中执行。 该过程要花费多长时间取决于目录中的用户数，以及应用规则后创建的组的大小。 通常，用户数较少的目录在几分钟内就能看到组成员身份变化。 而具有大量用户的目录可能需要 30 分钟或更长时间才能填充信息。

**如何强制现在处理的组？**<br/>
目前，没有办法自动触发要按需处理的组。 但是，可以手动触发重新处理更新成员身份规则以在末尾添加空格。  

**遇到了规则处理错误**<br/>下表列出了常见动态成员资格规则错误以及更正方法。

| 规则分析器错误 | 错误用法 | 更正的用法 |
| --- | --- | --- |
| 错误：不支持的属性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>请确保该属性在[支持的属性列表](groups-dynamic-membership.md#supported-properties)中。 |
| 错误：不支持对属性使用运算符。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>属性类型不支持所使用的运算符（在此示例中，-contains 不能用于布尔类型）。 请对该属性类型使用正确的运算符。 |
| 错误：查询编译错误。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1.缺少运算符。 使用 -and 或 -or 这两个联接谓词<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.与 -match 一起使用的正则表达式出错<br>(user.userPrincipalName -match ".*@domain.ext")<br>或：(user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
