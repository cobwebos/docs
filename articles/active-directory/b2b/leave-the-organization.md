---
title: 将组织保留为来宾用户 - Azure 活动目录
description: 演示 Azure AD B2B 来宾用户如何使用访问面板离开组织。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272499"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以来宾用户身份离开组织

如果 Azure Active Directory (Azure AD) B2B 来宾用户不再需要使用来自某个组织的应用或保持任何关联，则可随时决定离开该组织。 用户可自行离开组织，无需联系管理员。

> [!NOTE]
> 如果来宾用户帐户在家庭租户或资源租户中被禁用，则来宾用户无法离开组织。 如果禁用其帐户，来宾用户将需要联系租户管理员，租户管理员可以删除来宾帐户或启用来宾帐户，以便用户可以离开组织。

## <a name="leave-an-organization"></a>离开组织

要离开组织，请按照以下步骤操作。

1. 通过执行以下步骤之一，转到访问面板配置文件页面：
   
   - 在[Azure 门户](https://portal.azure.com)中，单击右上角的名称，然后选择 **"查看帐户**"。
   - 打开[访问面板](https://myapps.microsoft.com)，单击右上角的名称，在 **"组织"** 旁边，选择设置图标（齿轮）。
 
   ![显示访问面板中用户设置的屏幕截图](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 如果您尚未登录到要离开的组织，请在 **"组织"** 下单击 **"登录"，将组织链接保留**在组织名称旁边。 登录后，在 **"组织"** 的右上角和旁边再次单击您的姓名，选择设置图标（齿轮）。

3. 在“组织”下，找到要离开的组织，然后选择“离开组织”********。

   ![显示用户界面中“离开组织”选项的屏幕截图](media/leave-the-organization/LeaveOrg.png)

4. 系统要求确认时，请选择“离开”****。 

## <a name="account-removal"></a>删除帐户

当用户离开组织时，用户帐户会在目录中被“软删除”。 默认情况下，用户对象移动到 Azure AD 中的 **"已删除用户**区域"，但在 30 天内不会永久删除。 如果用户在 30 天内请求恢复帐户，管理员可借助此软删除功能恢复用户帐户（包括群组和权限）。

如果需要，租户管理员可以在 30 天期间内的任何时间永久删除帐户。 为此，请按以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”****。
2. 在“管理”下，选择“用户”********。
3. 选择“删除的用户”****。
4. 选中删除的用户旁边的复选框，然后选择“永久删除”****。

如果永久删除用户，此操作不可撤销。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD B2B 的概述，请参阅[什么是 Azure AD B2B 协作？](what-is-b2b.md)



