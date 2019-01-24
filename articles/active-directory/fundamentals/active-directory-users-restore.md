---
title: 还原或永久删除最近删除的用户 - Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory 查看可还原的用户、还原已删除的用户或永久删除用户。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 995a6a9da637c23663c51fe2f4b175741f5849b6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450313"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>使用 Azure Active Directory 还原或删除最近删除的用户
删除用户后，帐户将保持挂起状态 30 天。 在此 30 天期间，可以还原用户帐户及其所有属性。 30 天期限过后，将自动且永久删除用户。

可在 Azure 门户中使用 Azure Active Directory (Azure AD) 查看可还原的用户、还原已删除的用户或永久删除用户。

>[!Important]
>你本人或 Microsoft 客户支持人员均无法还原已永久删除的用户。

## <a name="required-permissions"></a>所需的权限
必须具有以下角色之一才能还原和永久删除用户。

- 公司管理员

- 合作伙伴一线支持人员

- 合作伙伴二线支持人员

- 用户帐户管理员

## <a name="view-your-restorable-users"></a>查看可还原的用户
可以查看 30 天以内删除的所有用户。 这些用户可以还原。

### <a name="to-view-your-restorable-users"></a>查看可还原的用户
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 依次选择“Azure Active Directory”、“用户”、“已删除的用户”。

    查看可还原的用户列表。

    ![“用户 - 已删除的用户”页，显示仍可还原的用户](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>还原最近删除的用户
当用户帐户处于挂起状态时，所有相关目录信息都会保留。 还原用户时，此目录信息也将还原。

### <a name="to-restore-a-user"></a>还原用户
1. 在“用户 - 已删除的用户”页上，搜索并选择一个可用用户。 例如，Mary Parker。

2. 选择“还原用户”。

    ![“用户 - 已删除的用户”页，其中突出显示“还原用户”选项](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>永久删除用户
可从目录中永久删除用户，无需等待 30 天后自动删除。 你本人、其他管理员和 Microsoft 客户支持人员不能还原已永久删除的用户。

>[!Note]
>如果错误地永久删除了用户，必须创建新用户并手动输入以前的所有信息。 有关创建新用户的详细信息，请参阅[添加或删除用户](add-users-azure-active-directory.md)。

### <a name="to-permanently-delete-a-user"></a>永久删除用户

1. 在“用户 - 已删除的用户”页上，搜索并选择一个可用用户。 例如，Rae Huff。

2. 选择“永久删除”。

    ![“用户 - 已删除的用户”页，其中突出显示“还原用户”选项](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>后续步骤
还原或删除用户后，可以执行以下基本过程：

- [添加或删除用户](add-users-azure-active-directory.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [添加另一个目录中的来宾用户](../b2b/what-is-b2b.md) 

有关其他可用的用户管理任务的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。
