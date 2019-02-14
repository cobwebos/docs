---
title: 重置用户的密码 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 重置用户密码的说明。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce76fa7fecb8a82f76d40827bfa868061a0ed584
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204893"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>使用 Azure Active Directory 重置用户的密码
如果忘记密码、设备遭锁定用户无法使用，或用户从未收到密码，管理员可以重置用户的密码。

>[!Note]
>除非 Azure AD 租户是用户的主目录，否则你将无法重置其密码。 这意味着，如果用户使用另一个组织的帐户（Microsoft 帐户或 Google 帐户）登录到组织，那么你将无法重置其密码。<br><br>如果用户具有 Windows Server Active Directory 的权限源，则只有在你打开密码回写时才能重置密码。<br><br>如果用户拥有外部 Azure AD 的权限源，你将无法重置密码。 只有用户或外部 Azure AD 中的管理员才能重置密码。

>[!Note]
>如果你不是管理员，只想获得有关如何重置你自己的工作或学校密码的说明，请参阅[重置工作或学校密码](../user-help/active-directory-passwords-update-your-own-password.md)。

## <a name="to-reset-a-password"></a>重置密码

1. 以全局管理员、用户管理员或密码管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. 选择“Azure Active Directory”，选择“用户”，搜索并选择需要重置的用户，然后选择“重置密码”。

    将显示“Alain Charon - 配置文件”页面，其中包含“重置密码”选项。

    ![用户的配置文件页面，其中突出显示“重置密码”选项](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. 在“重置密码”页面中，选择“重置密码”。

    临时密码是自动为用户生成的。

4. 复制密码并将其提供给用户。 在下次登录过程中，用户需要更改密码。

    >[!Note]
    >临时密码永不过期。 用户下次登录时，无论自生成临时密码以来已经过了多长时间，密码仍然有效。

## <a name="next-steps"></a>后续步骤
重置用户密码后，可以执行以下基本过程：

- [添加或删除用户](add-users-azure-active-directory.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

也可以执行更复杂的用户方案，例如分配委托、使用策略以及共享用户帐户。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。
