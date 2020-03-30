---
title: 为 Azure 门户的用户设置目录级别的非活动超时值 | Microsoft Docs
description: 管理员可以在会话注销之前强制执行最大空闲时间。非活动超时策略在目录级别设置。
services: azure-portal
keywords: 设置, 超时
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096606"
---
# <a name="set-directory-level-inactivity-timeout"></a>设置目录级别的非活动超时

如果用户忘记了保护工作站，非活动超时设置有助于防范资源受到未经授权的访问。 当用户处于空闲状态一段时间时，其 Azure 门户会话将自动注销。[全局管理员角色的](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)管理员可以在会话注销之前强制执行最大空闲时间。不活动超时设置适用于目录级别。 有关目录的详细信息，请参阅 [Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

## <a name="configure-the-inactive-timeout-setting"></a>配置非活动超时设置

如果您是全局管理员，并且希望为 Azure 门户的所有用户强制实施空闲超时设置，请按照以下步骤操作：

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 在全局页头中选择“设置”。****
3. 选择链接文本“配置目录级别超时”****。

    ![显示门户设置的屏幕截图，其中突出显示了链接文本](./media/admin-timeout/settings.png)

4. 此时会打开一个新页面。 在“配置目录级非活动超时”**** 页上，选择“为 Azure 门户启用目录级别的空闲超时”**** 以启用设置。
5. 接下来，输入**小时数**和**分钟数**，以便设置在会话自动注销之前用户可处于空闲状态的最长时间。
6. 选择“应用”。

    ![屏幕截图，显示用于设置目录级别非活动超时的页面](./media/admin-timeout/configure.png)

若要确认是否正确设置了非活动超时策略，请从全局页眉中选择“通知”。**** 验证是否列出了成功通知。

  ![屏幕截图，显示用于目录级别非活动超时的成功通知消息](./media/admin-timeout/confirmation.png)

设置对新会话生效。 它不会立即应用于已登录的任何用户。

> [!NOTE]
> 如果全局管理员配置了目录级超时设置，用户可以覆盖策略并设置自己的非活动注销持续时间。 但是，用户必须选择的时间间隔小于全局管理员在目录级别设置的时间间隔。
>

## <a name="next-steps"></a>后续步骤

* [设置 Azure 门户首选项](set-preferences.md)
* [导出或删除用户设置](azure-portal-export-delete-settings.md)
* [启用高对比度或更改主题](azure-portal-change-theme-high-contrast.md)
