---
title: 向 Microsoft Authenticator 应用添加个人 Microsoft 帐户 Azure AD
description: 将个人 Microsoft 帐户（如 Outlook.com 或 Xbox LIVE）添加到 Microsoft Authenticator 应用，以便在使用双重验证时验证你的身份。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.openlocfilehash: 8945f7a49f4c04b3265cb79c88c9acb287c50d10
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704742"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>将个人 Microsoft 帐户添加到 Microsoft Authenticator 应用

将 Microsoft 个人帐户（如 Outlook.com 和 Xbox LIVE 帐户）添加到 Microsoft Authenticator 应用，以实现标准双因素验证流程和无密码电话登录方法。

- **标准双因素验证方法。** 在要登录的设备中，键入用户名和密码，再选择是 Microsoft Authenticator 应用发送通知，还是从 Microsoft Authenticator 应用的“帐户”屏幕复制关联的验证码。

- **无密码登录方法。** 在要登录的设备中，键入 Microsoft 个人帐户的用户名，再在移动设备上使用指纹、人脸或 PIN 验证自己的身份。 对于这种方法，无需输入密码。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

## <a name="add-your-personal-microsoft-account"></a>添加 Microsoft 个人帐户

若要添加 Microsoft 个人帐户，可以先启用双因素验证，再将帐户添加到应用。

>[!Note]
>如果计划只对 Microsoft 个人帐户使用无密码电话登录，无需启用双因素验证。 不过，为了提高帐户安全，建议启用双因素验证。

### <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在计算机上，转到[安全基础](https://account.microsoft.com/security)页，并使用 Microsoft 个人帐户登录。 例如，alain@outlook.com 。

2. 在“安全基础”页底部，选择“更多安全选项”链接。

    ![突出显示“更多安全选项”链接的“安全基础”页](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 转到“双因素验证”部分，并选择“启用”此功能。 如果不想再对个人帐户使用此功能，也可以在这里禁用它。

### <a name="add-your-microsoft-account-to-the-app"></a>将 Microsoft 帐户添加到应用

1. 在移动设备上，打开 Microsoft Authenticator 应用。

2. 选择右上角“自定义和控制”图标中的“添加帐户”。

    ![突出显示“自定义和控制”图标的“帐户”页](./media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

3. 在“添加帐户”页上，选择“个人帐户”。

4. 使用相应的电子邮件地址（如 alain@outlook.com）登录个人帐户，再选择“下一步”。

    >[!Note]
    >如果没有 Microsoft 个人帐户，可以在此处创建一个。

5. 输入密码，再选择“登录”。

    此时，个人帐户被添加到 Microsoft Authenticator 应用。

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 如果在获取个人 Microsoft 帐户的验证代码时遇到问题，请参阅[Microsoft 帐户安全信息 & 验证](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)代码一文中的 "**验证代码问题疑难解答**" 一节。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
