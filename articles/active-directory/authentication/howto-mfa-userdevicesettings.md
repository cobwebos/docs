---
title: 管理 Azure 多重身份验证的用户设置 - Azure 活动目录
description: 了解如何为 Azure 多重身份验证配置 Azure 活动目录用户设置
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309761"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>管理 Azure 多重身份验证的用户设置

为了帮助管理 Azure 多重身份验证的用户，可以要求用户重置其密码、重新注册 MFA 或撤消现有的 MFA 会话。 对于已定义应用密码的用户，您还可以选择删除这些密码，从而导致这些应用程序中的旧身份验证失败。 如果您需要向用户提供帮助，或者想要重置其安全状态，则可能需要执行这些操作。

## <a name="manage-user-authentication-options"></a>管理用户身份验证选项

如果您分配了*身份验证管理员*角色，则可以要求用户重置其密码、重新注册 MFA 或从其用户对象撤消现有的 MFA 会话。 要管理用户设置，完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择**Azure 活动目录** > **用户** > **所有用户**。
1. 选择要在其上执行操作的用户，然后选择“身份验证方法”。**** 在窗口顶部，然后为用户选择以下选项之一：
   - **重置密码**将重置用户的密码，并分配必须在下一个登录时更改的临时密码。
   - **要求重新注册 MFA**使用户下次登录时，会要求他们设置新的 MFA 身份验证方法。
   - **撤消 MFA 会话**会清除用户记住的 MFA 会话，并要求他们在下次设备上的策略要求时执行 MFA。

   ![从 Azure 门户管理身份验证方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>删除用户现有的应用密码

如果需要，可以删除用户创建的所有应用密码。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。 执行此操作需要*全局管理员*权限。

要删除用户的应用密码，请完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择**Azure 活动目录** > **用户** > **所有用户**。
1. 选择**多重身份验证**。 您可能需要向右滚动才能看到此菜单选项。 选择下面的示例屏幕截图以查看完整的 Azure 门户窗口和菜单位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "从 Azure AD 中的"用户"窗口选择多重身份验证")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 选中要管理的用户或用户旁的框。 右侧会显示快速步骤选项列表。
1. 选择 **"管理用户设置**"，然后选中 **"删除所选用户生成的所有现有应用密码**"复选框，如以下示例所示：![删除所有现有应用密码](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 选择 **"保存**"，然后**关闭**。

## <a name="next-steps"></a>后续步骤

本文帮助配置了单个用户设置。 要配置 Azure 多重身份验证服务设置，请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)

如果用户需要帮助，请参阅[Azure 多重身份验证的用户指南](../user-help/multi-factor-authentication-end-user.md)。
