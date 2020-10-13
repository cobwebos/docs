---
title: 使用 Microsoft Authenticator 应用备份和恢复帐户 - Azure AD
description: 了解如何使用 Microsoft Authenticator 应用备份和恢复已备份的帐户凭据。
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530895"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 应用备份和恢复帐户凭据

**适用于：**

- 运行版本 5.7.0 和更高版本的 iOS 设备

- 运行版本 6.6.0 和更高版本的 Android 设备

Microsoft Authenticator 应用可将帐户凭据和相关的应用设置（例如帐户顺序）备份到云中。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

对于每个备份存储位置，你都需要具有一个个人 Microsoft 帐户，而对于 iOS，你还需要具有一个 iCloud 帐户。 可在单个位置中存储多个帐户。 例如，你可以拥有一个个人帐户、一个工作或学校帐户帐户和一个个人的非 Microsoft 帐户（例如 Facebook、Google 等）。

> [!IMPORTANT]
> 我们只会存储你的个人和第三方帐户凭据，包括用于证明你身份的用户名和帐户验证码。 我们不会存储与你的帐户相关的其他任何信息，包括电子邮件或文件。 此外，我们不会以任何方式或者与其他任何产品或服务关联或共享你的帐户。 最后，IT 管理员不会获得有关上述任何帐户的任何信息。

## <a name="back-up-your-account-credentials"></a>备份帐户凭据

在备份凭据之前，必须具有：

- 一个充当恢复帐户的个人 [Microsoft 帐户](https://account.microsoft.com/account)。

- 一个用作实际存储位置的 [iCloud 帐户](https://www.icloud.com/)（仅限 iOS）。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>为 iOS 设备启用云备份

- 在 iOS 设备上，依次选择“设置”、“备份”，然后启用“iCloud 备份”。  

    帐户凭据将备份到 iCloud 帐户。

    ![iOS 设置屏幕，其中显示了 iCloud 备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>为 Android 设备启用云备份

- 在 Android 设备上，依次选择“设置”、“备份”，然后启用“云备份”  。

    帐户凭据将备份到云帐户。

    ![Android 设置屏幕，其中显示了备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在新设备上恢复帐户凭据

可以从云帐户恢复帐户凭据，但必须首先确保要恢复的帐户不存在于 Microsoft Authenticator 应用中。 例如，如果要恢复个人 Microsoft 帐户，必须确保 Authenticator 应用中尚未设置个人 Microsoft 帐户。 这项检查非常重要，以便我们可以确定不会错误地覆盖或擦除现有帐户。

### <a name="to-recover-your-information"></a>恢复信息

1. 在移动设备上打开 Microsoft Authenticator 应用，并选择屏幕底部的“开始恢复”。

    ![Microsoft Authenticator 应用，其中显示了“开始恢复”的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用备份期间所用的同一个人 Microsoft 帐户登录到恢复帐户。

    帐户凭据将恢复到新设备。

完成恢复后，你可能会注意到，旧手机和新手机上的 Microsoft Authenticator 应用中的个人 Microsoft 帐户验证码不相同。 验证码之所以不相同，是因为每台设备都有自身唯一的凭据，而在使用相关的手机登录时，这两个凭据都是有效的。

## <a name="recover-accounts-requiring-more-verification"></a>恢复需要更多验证的帐户

如果通过个人、工作或学校帐户使用推送通知，屏幕上会显示一条警报，指出在恢复信息之前，必须先提供附加验证。 由于推送通知要求使用绑定到特定设备的凭据，而且永远不会通过网络发送，因此，在设备上创建凭据之前，必须先证明自己的身份。

对于个人 Microsoft 帐户，可以通过输入密码以及备用电子邮件或电话号码来证明身份。 对于工作或学校帐户，必须扫描帐户提供商提供的 QR 码。

### <a name="to-provide-more-verification-for-personal-accounts"></a>为个人帐户提供更多验证

1. 在 Microsoft Authenticator 应用程序的 " **帐户** " 屏幕上，点击要恢复的帐户以打开该帐户的全屏视图。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="显示具有可用帐户磁贴的 Microsoft Authenticator 应用程序的屏幕截图。" border="true":::

1. 点击要恢复的帐户的磁贴，然后点击 "登录" 选项以恢复。 输入密码，然后确认你的电子邮件地址或电话号码作为附加验证。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="显示具有可用帐户磁贴的 Microsoft Authenticator 应用程序的屏幕截图。" border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>为工作或学校帐户提供更多验证

1. 在 Microsoft Authenticator 应用程序的 " **帐户** " 屏幕上，点击要恢复的帐户以打开该帐户的全屏视图。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="显示具有可用帐户磁贴的 Microsoft Authenticator 应用程序的屏幕截图。" border="true":::

1. 在全屏视图中，点击选项以扫描 QR 代码以完全恢复。

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="显示具有可用帐户磁贴的 Microsoft Authenticator 应用程序的屏幕截图。" border="true":::

>[!NOTE]
>有关 QR 码以及如何获取 QR 码的详细信息，请参阅 [Microsoft Authenticator 应用入门](./user-help-auth-app-download-install.md)或[设置安全信息以使用 Authenticator 应用](./security-info-setup-auth-app.md)，具体取决于管理员是否已启用安全信息。
>
>如果这是你首次设置 Microsoft Authenticator 应用，则可能会收到一个提示，询问你是允许该应用访问你的相机 (iOS)，还是允许该应用拍摄照片和录制视频 (Android)。 你必须选择“允许”，以便验证器应用可以访问你的相机，在下一步中拍摄 QR 码的照片。 如果你不允许使用相机，仍然可以设置验证器应用，但需要手动添加代码信息。 有关如何手动添加代码的信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>对备份和恢复问题进行故障排除

有几个可能的原因会导致无法备份

- **更改操作系统**：备份存储在 iCloud 中（适用于 iOS 设备）和 Microsoft 的云存储提供商处（适用于 Android 设备）。 这意味着，如果你在 Android 和 iOS 设备之间切换，则备份将不可用。 如果进行切换，则必须在 Microsoft Authenticator 应用中手动重新创建帐户。

- **网络问题**：如果遇到与网络相关的问题，请确保已连接到网络并正确登录到帐户。

- **帐户问题**：如果遇到与帐户相关的问题，请确保正确登录到帐户。 对于 iOS，这意味着必须使用与 iPhone 相同的 AppleID 帐户登录 iCloud。

- **意外删除**：你可能在上一台设备中删除了备份帐户，或者在管理云存储帐户时删除了备份帐户。 在这种情况下，必须在应用中手动重新创建帐户。

- **现有的 Microsoft Authenticator 帐户**：如果已在 Microsoft Authenticator 应用中设置了帐户，该应用将无法恢复备份的帐户。 阻止恢复有助于确保帐户详细信息不会被过期的信息覆盖。 在这种情况下，必须先从 Authenticator 应用中设置的现有帐户删除现有的帐户信息，然后才能恢复备份。

- **备份已过期**：如果备份信息已过期，系统可能会要求你再次登录 Microsoft 恢复帐户以刷新信息。 恢复帐户是最初用于存储备份的个人 Microsoft 帐户。 如果需要登录，你会在菜单或操作栏上看到一个红点，或者你将看到一个惊叹号图标，提示你登录以完成从备份还原。 选择相应的图标后，系统将提示你再次登录以更新你的信息。

## <a name="next-steps"></a>后续步骤

将帐户凭据备份并恢复到新设备后，可以继续使用 Microsoft Authenticator 应用来验证自己的身份。 有关详细信息，请参阅[使用 Microsoft Authenticator 应用登录到帐户](user-help-sign-in.md)。

## <a name="related-articles"></a>相关文章

- [什么是 Microsoft Authenticator 应用？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)

- [多重身份验证](/azure/multi-factor-authentication/)