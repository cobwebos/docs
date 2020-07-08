---
title: 管理 Azure 多重身份验证的用户设置-Azure Active Directory
description: 了解如何为 Azure 多重身份验证配置 Azure Active Directory 用户设置
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
ms.openlocfilehash: 295738ee5943a6cf54bc7e1e3ce4bba621dbe29f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84658683"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>管理 Azure 多重身份验证的用户设置

为了帮助管理 Azure 多重身份验证的用户，你可以要求用户重置其密码，为 MFA 重新注册，或撤消现有的 MFA 会话。 对于已定义应用密码的用户，你还可以选择删除这些密码，从而导致在这些应用程序中出现旧身份验证失败。 如果需要向用户提供帮助或想要重置其安全状态，则可能需要执行这些操作。

## <a name="manage-user-authentication-options"></a>管理用户身份验证选项

如果分配了*身份验证管理员*角色，则可以要求用户重置其密码，重新注册 MFA，或从其用户对象撤消现有的 mfa 会话。 若要管理用户设置，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择 " **Azure Active Directory**  >  **用户**" "  >  **所有用户**"。
1. 选择要在其上执行操作的用户，然后选择“身份验证方法”。**** 在窗口顶部，为用户选择以下选项之一：
   - **重置密码**会重置用户的密码，并分配在下次登录时必须更改的临时密码。
   - **要求重新注册 MFA**使其进行，以便在下次用户登录时，他们将被请求建立新的 MFA 身份验证方法。
   
      > [!NOTE]
      > 如果管理员需要重新注册 MFA，则不会删除用户的当前注册身份验证方法。 用户为 MFA 重新注册后，我们建议他们查看其安全信息并删除任何以前注册的不再可用的身份验证方法。
   
   - **REVOKE Mfa 会话**会清除用户记住的 mfa 会话，并要求他们在下次设备上策略要求时执行 mfa。

   ![从 Azure 门户管理身份验证方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>删除用户现有的应用密码

如果需要，你可以删除用户创建的所有应用密码。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。 需要*全局管理员*权限才能执行此操作。

若要删除用户的应用密码，请完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，选择 " **Azure Active Directory**  >  **用户**" "  >  **所有用户**"。
1. 选择“多重身份验证”。 可能需要向右滚动才能看到此菜单选项。 单击以下示例屏幕截图可查看完整的 Azure 门户窗口和菜单位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "从 Azure AD 的“用户”窗口中选择“多重身份验证”")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 选中要管理的用户或用户旁的框。 右侧会显示快速步骤选项列表。
1. 选择 "**管理用户设置**"，然后选中 "**删除选定用户生成的所有现有应用密码**" 复选框，如以下示例中所示： ![ 删除所有现有的应用密码](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 选择 "**保存**"，然后单击 "**关闭**"。

## <a name="next-steps"></a>后续步骤

本文介绍如何配置单个用户设置。 若要配置 Azure 多重身份验证服务设置，请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)

如果用户需要帮助，请参阅[Azure 多重身份验证的用户指南](../user-help/multi-factor-authentication-end-user.md)。
