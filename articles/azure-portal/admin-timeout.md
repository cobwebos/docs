---
title: 为 Azure 门户的用户设置目录级别的非活动超时值 |Microsoft Docs
description: 管理员可以强制执行会话注销之前的最长空闲时间。在目录级别设置非活动超时策略。
services: azure-portal
keywords: 设置，超时
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 950580048f0496fd8436901938a5b6768c61bab6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132079"
---
# <a name="set-directory-level-inactivity-timeout"></a>设置目录级别的非活动超时

如果用户忘记保护自己的工作站，则 "非活动超时" 设置有助于防止对资源进行未经授权的访问。 用户处于空闲状态一段时间后，其 Azure 门户会话会自动注销。[全局管理员角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)中的管理员可强制执行在注销会话之前的最长空闲时间。非活动超时设置适用于目录级别。 有关目录的详细信息，请参阅[Active Directory 域服务概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

## <a name="configure-the-inactive-timeout-setting"></a>配置非活动超时设置

如果你是全局管理员，并且想要为 Azure 门户的所有用户强制使用空闲超时设置，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从全局页眉中选择 "**设置**"。
3. 选择链接文本 "**配置目录级别超时**"。

    ![显示链接文本突出显示的门户网站设置的屏幕截图](./media/admin-timeout/settings.png)

4. 此时会打开一个新页面。 在 "**配置目录级别非活动超时**" 页上，选择 **"启用 Azure 门户的目录级别空闲超时**" 以打开设置。
5. 接下来，输入用户在其会话自动注销之前可处于空闲状态的最长时间（**小时**和**分钟）** 。
6. 选择“应用”。

    ![显示用于设置目录级别非活动超时的页的屏幕截图](./media/admin-timeout/configure.png)

若要确认已正确设置非活动超时策略，请从全局页眉中选择 "**通知**"。 验证是否列出了成功通知。

  ![显示目录级非活动超时成功通知消息的屏幕截图](./media/admin-timeout/confirmation.png)

此设置对新会话生效。 它不会立即应用于已登录的任何用户。

> [!NOTE]
> 如果管理员配置了目录级别的超时设置，则用户可以替代策略并设置其自己的非活动注销持续时间。 但是，用户必须选择小于在目录级别设置的时间间隔。
>

## <a name="next-steps"></a>后续步骤

* [设置 Azure 门户首选项](set-preferences.md)
* [导出或删除用户设置](azure-portal-export-delete-settings.md)
* [启用高对比度或更改主题](azure-portal-change-theme-high-contrast.md)
