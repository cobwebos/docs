---
title: 向 Microsoft Authenticator 应用添加个人 Microsoft 帐户 Azure AD
description: 将个人 Microsoft 帐户（如 Outlook.com 或 Xbox LIVE）添加到 Microsoft Authenticator 应用，以便在使用双重验证时验证你的身份。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: cef4f83881aed67c46477110de530bbf191ee39f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984725"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>将个人 Microsoft 帐户添加到 Microsoft Authenticator 应用

将个人 Microsoft 帐户（例如用于 Outlook.com 和 Xbox LIVE 帐户）添加到 Microsoft Authenticator 应用，用于标准双因素验证过程和无密码手机登录。

- **标准双因素验证方法。** 在要登录到的设备中键入用户名和密码，然后选择 Microsoft Authenticator 应用是否发送通知，或者是否希望从 Microsoft Authenticator 应用的 "**帐户**" 屏幕中复制关联的验证代码。

- **无密码登录方法。** 在使用个人 Microsoft 帐户登录的设备中键入用户名，然后使用您的移动设备验证您使用的是指纹、脸或 PIN。 对于这种方法，无需输入密码。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

若要添加 Microsoft 个人帐户，可以先启用双因素验证，再将帐户添加到应用。 您无需启用双重验证即可只对您的帐户使用无密码手机登录，但我们强烈建议您启用双重验证以实现额外的帐户安全性。

## <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在计算机上，转到[安全基础](https://account.microsoft.com/security)页，并使用 Microsoft 个人帐户登录。 例如，alain@outlook.com 。

2. 在“安全基础”**** 页底部，选择“更多安全选项”**** 链接。

    ![突出显示“更多安全选项”链接的“安全基础”页](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 转到“双因素验证”**** 部分，并选择“启用”**** 此功能。 如果不想再对个人帐户使用此功能，也可以在这里禁用它。

## <a name="add-your-microsoft-account-to-the-app"></a>将 Microsoft 帐户添加到应用

1. 在移动设备上，打开 Microsoft Authenticator 应用。

1. 在 Android 上，从右上角的 "**自定义" 和 "控件**" 图标选择 "**添加帐户**"。

    ![Android 帐户选择页](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    在 iOS 中，选择右上角的加号图标。

    ![iOS 版本的帐户选择体验](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. 在“添加帐户”**** 页上，选择“个人帐户”****。

1. 选择 " **Microsoft 登录**" 以添加帐户。 可以使用 QR 码（如果可用），但始终可以使用用户名和密码登录来添加帐户。

    ![选择 Microsoft 帐户或扫描 QR 码（如果可用）](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 使用相应的电子邮件地址（例如alain@outlook.com）登录到你的个人帐户，然后选择 "**下一步**"。

    >[!Note]
    >如果你没有个人 Microsoft 帐户，[则可以创建一个](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)。

1. 输入密码，然后选择 "**登录**"。 此时，个人帐户被添加到 Microsoft Authenticator 应用。

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 如果在获取个人 Microsoft 帐户的验证代码时遇到问题，请参阅[Microsoft 帐户安全信息 & 验证](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)代码一文中的 "**验证代码问题疑难解答**" 一节。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
