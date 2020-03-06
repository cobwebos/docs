---
title: Microsoft Authenticator 应用备份和恢复帐户-Azure AD
description: 了解如何使用 Microsoft Authenticator 应用备份和恢复已备份的帐户凭据。
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78297950"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 应用备份和恢复帐户凭据

**适用于：**

- iOS 设备，运行版本5.7.0 和更高版本

- Android 设备，运行版本6.6.0 和更高版本

Microsoft Authenticator 应用可将帐户凭据和相关的应用设置（例如帐户顺序）备份到云中。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

每个备份存储位置都要求有一个个人 Microsoft 帐户，而 iOS 还要求你具有 iCloud 帐户。 可以在该位置中存储多个帐户。 例如，你可以有个人帐户、工作或学校帐户，以及适用于 Facebook、Google 等的个人、非 Microsoft 帐户。

> [!IMPORTANT]
> 仅存储你的个人和第三方帐户凭据，其中包括你的用户名和验证身份所需的帐户验证代码。 我们不会存储与帐户相关的任何其他信息，包括电子邮件或文件。 我们也不会以任何方式关联或共享你的帐户，也不会与任何其他产品或服务共享。 最后，IT 管理员不会获得任何这些帐户的任何信息。

## <a name="back-up-your-account-credentials"></a>备份帐户凭据

在备份凭据之前，必须具备：

- 一个充当恢复帐户的个人 [Microsoft 帐户](https://account.microsoft.com/account)。

- **仅适用于 iOS，** 你必须拥有一个用于实际存储位置的[iCloud 帐户](https://www.icloud.com/)。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>为 iOS 设备启用云备份

- 在 iOS 设备上，依次选择“设置”、“备份”，然后启用“iCloud 备份”。

    帐户凭据将备份到 iCloud 帐户。

    ![iOS 设置屏幕，其中显示了 iCloud 备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>为 Android 设备启用云备份

- 在 Android 设备上，依次选择 "**设置**"、"**备份**" 和 "**云备份**"。

    你的帐户凭据将备份到你的云帐户。

    !["Android 设置" 屏幕，显示备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在新设备上恢复帐户凭据

你可以从你的云帐户恢复你的帐户凭据，但必须首先确保你要恢复的帐户不存在于 Microsoft Authenticator 的应用程序中。 例如，如果要恢复个人 Microsoft 帐户，则必须确保未在验证器应用中设置个人 Microsoft 帐户。 这项检查非常重要，所以我们可以确定不会错误地覆盖或擦除现有帐户。

### <a name="to-recover-your-information"></a>恢复信息

1. 在移动设备上，打开 Microsoft Authenticator 应用，并选择屏幕底部的 "**开始恢复**"。

    ![Microsoft Authenticator 应用，其中显示了“开始恢复”的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用备份期间所用的同一个人 Microsoft 帐户登录到恢复帐户。

    帐户凭据将恢复到新设备。

完成恢复后，你可能会注意到，旧手机和新手机上的 Microsoft Authenticator 应用中的个人 Microsoft 帐户验证码不相同。 验证码之所以不相同，是因为每台设备都有自身唯一的凭据，而在使用相关的手机登录时，这两个凭据都是有效的。

## <a name="recover-additional-accounts-requiring-more-verification"></a>恢复需要更多验证的其他帐户

如果你在个人帐户或工作或学校帐户中使用推送通知，你将看到一个屏幕警报，指出你必须先提供其他验证才能恢复你的信息。 由于推送通知需要使用与特定设备关联的凭据，且从未通过网络发送，因此必须在设备上创建凭据之前验证身份。

对于个人 Microsoft 帐户，可以通过输入密码以及备用电子邮件或电话号码来证明身份。 对于工作或学校帐户，必须扫描帐户提供商提供的 QR 码。

### <a name="to-provide-additional-verification-for-personal-accounts"></a>为个人帐户提供附加验证

1. 在 Microsoft Authenticator 应用的 "**帐户**" 屏幕中，选择要恢复的帐户。 在 Android 设备上，选择要恢复的帐户旁边的箭头。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    在 iOS 设备上，点击要恢复的帐户以打开帐户的全屏视图。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. 登录以恢复帐户。 在 Android 设备上，选择 "**登录以恢复**"。

    ![Microsoft Authenticator 应用在 Android 上输入登录信息](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    在 iOS 设备上，点击要恢复的帐户磁贴，然后点击 "登录并恢复" 选项。 然后键入你的密码，并确认你的电子邮件地址或电话号码作为附加的 unt。

    ![Microsoft Authenticator 应用在 iOS 上输入登录信息](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>为工作或学校帐户提供附加验证

1. 登录以恢复帐户。 在 Android 设备上，选择 "**登录以恢复**"。

    ![Microsoft Authenticator 应用在 Android 上恢复工作或学校帐户](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    在 iOS 设备上，点击要恢复的帐户以打开帐户的全屏视图。

    ![Microsoft Authenticator 应用恢复 iOS 上的工作或学校帐户](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. 可以使用 QR 代码恢复帐户。 选择“扫描 QR 码以恢复”，然后扫描 QR 码。

    在 Android 上：

    ![在 Android 上 Microsoft Authenticator 应用程序，允许扫描 QR 码](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    在 iOS 上：

    ![在 iOS 上 Microsoft Authenticator 应用，使你能够扫描 QR 码](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >有关 QR 码和如何获取 QR 码的详细信息，请参阅[Microsoft Authenticator 应用入门](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)或[设置安全信息以使用验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)，具体取决于管理员是否已启用安全信息。
    >
    >如果这是你首次设置 Microsoft Authenticator 应用，则可能会收到一个提示，询问你是允许该应用访问你的相机 (iOS)，还是允许该应用拍摄照片和录制视频 (Android)。 你必须选择“允许”，以便验证器应用可以访问你的相机，在下一步中拍摄 QR 码的照片。 如果你不允许使用相机，仍然可以设置验证器应用，但需要手动添加代码信息。 有关如何手动添加代码的信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>备份和恢复问题疑难解答

备份可能不可用的原因有几个

- **更改操作系统**：你的备份存储在适用于 IOS 的 iCloud 和适用于 Android 的 Microsoft 云存储提供程序中。 这意味着，如果你在 Android 和 iOS 设备之间切换，你的备份将不可用。 如果你进行切换，则必须在 Microsoft Authenticator 应用中手动重新创建帐户。

- **网络问题**：如果遇到与网络相关的问题，请确保已连接到网络并正确登录到帐户。

- **帐户问题**：如果遇到与帐户相关的问题，请确保已正确登录到帐户。 对于 iOS，这意味着必须使用与 iPhone 相同的 AppleID 帐户登录 iCloud。

- **意外删除**：可能已从以前的设备或在管理云存储帐户时删除了备份帐户。 在这种情况下，必须在应用中手动重新创建帐户。

- **现有 Microsoft Authenticator 帐户**：如果已在 Microsoft Authenticator 应用程序中设置帐户，则该应用程序将无法恢复你的已备份帐户。 阻止恢复有助于确保帐户详细信息不会被过期的信息覆盖。 在这种情况下，必须先从 Authenticator 应用中设置的现有帐户删除现有的帐户信息，然后才能恢复备份。

- **备份已过期**：如果你的备份信息已过期，则系统可能会要求你再次登录到 Microsoft 恢复帐户来刷新信息。 恢复帐户是最初用于存储备份的个人 Microsoft 帐户。 如果需要登录，你会在菜单或操作栏上看到一个红点，或者你将看到一个惊叹号，提示你登录完成从备份还原。 选择该图标后，系统将提示你再次登录以更新你的信息。

## <a name="next-steps"></a>后续步骤

将帐户凭据备份并恢复到新设备后，可以继续使用 Microsoft Authenticator 应用来验证自己的身份。 有关详细信息，请参阅[使用 Microsoft Authenticator 应用登录到你的帐户](user-help-sign-in.md)。

## <a name="related-articles"></a>相关文章

- [什么是 Microsoft Authenticator 应用？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)

- [多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/)
