---
title: 将 Microsoft 个人帐户添加到 Microsoft Authenticator 应用 - Azure AD
description: 使用双重验证时，将 Microsoft 个人帐户（如 Outlook.com 或 Xbox LIVE 帐户）添加到 Microsoft Authenticator 应用，以验证你的身份。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83741629"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>将 Microsoft 个人帐户添加到 Microsoft Authenticator 应用

将 Microsoft 个人帐户（如 Outlook.com 和 Xbox LIVE 帐户）添加到 Microsoft Authenticator 应用，以实现标准双重验证流程和无密码电话登录。

- **标准双因素验证方法。** 在要登录的设备中，键入用户名和密码，然后选择是 Microsoft Authenticator 应用发送通知，还是从 Microsoft Authenticator 应用的“帐户”屏幕复制关联的验证码。

- **无密码登录方法。** 在要登录的设备中，键入用户名以使用 Microsoft 个人帐户，然后在移动设备上使用指纹、人脸或 PIN 验证自己的身份。 对于这种方法，无需输入密码。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

若要添加 Microsoft 个人帐户，可以先启用双因素验证，再将帐户添加到应用。 如果仅对帐户使用无密码手机登录，则无需启用双重验证，但我们强烈建议你启用双重验证来提高帐户安全性。

## <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在计算机上，转到[安全基础](https://account.microsoft.com/security)页，并使用 Microsoft 个人帐户登录。 例如，alain@outlook.com 。

2. 在“安全基础”页底部，选择“更多安全选项”链接。

    ![突出显示“更多安全选项”链接的“安全基础”页](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. 转到“双因素验证”部分，并选择“启用”此功能。 如果不想再对个人帐户使用此功能，也可以在这里禁用它。

## <a name="add-your-microsoft-account-to-the-app"></a>将 Microsoft 帐户添加到应用

1. 在移动设备上，打开 Microsoft Authenticator 应用。

1. 在 Android 上，选择右上角“自定义和控制”图标中的“添加帐户”。

    ![Android 帐户选择页面](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    在 iOS 上，选择右上角的加号图标。

    ![iOS 版本的账号选择体验](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. 在“添加帐户”页上，选择“个人帐户”。

1. 选择“使用 Microsoft 登录”以添加帐户。 如果有 QR 码，可以使用 QR 码；但无论何时，你都可以通过使用用户名和密码进行登录来添加帐户。

    ![选择 Microsoft 帐户或扫描 QR 码（如果有）](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. 使用相应的电子邮件地址（如 alain@outlook.com）登录个人帐户，然后选择“下一步”。

    >[!Note]
    >如果没有 Microsoft 个人帐户，[可以创建一个](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index)。

1. 输入密码，然后选择“登录”。 此时，个人帐户被添加到 Microsoft Authenticator 应用。

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 如果在获取 Microsoft 个人帐户的验证码时遇到问题，请参阅 [Microsoft 帐户安全信息和验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的“排查验证码问题”部分。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
