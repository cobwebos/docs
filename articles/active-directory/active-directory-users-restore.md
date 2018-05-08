---
title: 在 Azure Active Directory 中还原或永久删除最近删除的用户 | Microsoft Docs
description: 如何在 Azure Active Directory 中还原已删除的用户、查看可还原的用户或永久删除用户
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/28/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9e28184000964564bcf170a2c8015f3b4c220209
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>在 Azure Active Directory 中还原已删除的用户

本文包含如何还原或永久删除以前删除的用户的说明。 在 Azure Active Directory (Azure AD) 中删除某个用户时，删除的用户会在从删除日期开始算起的 30 天内保留。 在此期间，可以还原该用户及其属性。 

> [!wARNING]
> 在它被永久删除后，将无法还原用户。


## <a name="how-to-restore-a-recently-deleted-user"></a>如何还原最近删除的用户
如果最近删除了某个用户，将保留该用户所有目录信息。 如果还原该用户，也会还原这些信息。

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)中，选择“用户和组”&gt;“所有用户”。 
2. 在“显示”下，筛选页以显示“最近删除的用户”。 
3. 选择一个或多个最近删除的用户。
4. 选择“还原用户”。

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>如何永久删除最近删除的用户

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)中，选择“用户和组”&gt;“所有用户”。 
2. 在“显示”下，筛选页以显示“最近删除的用户”。 
3. 选择一个或多个最近删除的用户。
4. 选择“永久删除”。

## <a name="required-permissions"></a>所需的权限
以下权限足以还原用户。

角色  | 权限 
--------- | ---------
公司管理员<p>合作伙伴一线支持人员<p>合作伙伴二线支持人员<p>用户帐户管理员 | 可以还原已删除的用户 
公司管理员<p>合作伙伴一线支持人员<p>合作伙伴二线支持人员<p>用户帐户管理员 | 可以永久删除用户

## <a name="next-steps"></a>后续步骤
以下文章提供了有关 Azure Active Directory 用户管理的更多信息。

* [快速入门：在 Azure Active Directory 中添加或删除用户](add-users-azure-active-directory.md)
* [管理用户个人资料](active-directory-users-profile-azure-portal.md)
* [添加另一个目录中的来宾用户](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [在 Azure AD 中为用户分配角色](active-directory-users-assign-role-azure-portal.md)
