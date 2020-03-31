---
title: 使用 Microsoft 身份验证器应用备份和恢复帐户 - Azure AD
description: 了解如何使用 Microsoft 身份验证器应用备份和恢复备份的帐户凭据。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297950"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>使用 Microsoft 身份验证器应用备份和恢复帐户凭据

**适用于：**

- iOS 设备，运行版本 5.7.0 及更高版本

- Android 设备，运行版本 6.6.0 及更高版本

Microsoft Authenticator 应用可将帐户凭据和相关的应用设置（例如帐户顺序）备份到云中。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

每个备份存储位置要求您拥有一个个人 Microsoft 帐户，而 iOS 也要求您拥有 iCloud 帐户。 您可以在该单个位置存储多个帐户。 例如，您可以拥有个人帐户、工作或学校帐户，以及个人非 Microsoft 帐户，如 Facebook、Google 等。

> [!IMPORTANT]
> 仅存储您的个人和第三方帐户凭据，其中包括您的用户名和证明您的身份所需的帐户验证码。 我们不会存储与您的帐户关联的任何其他信息，包括电子邮件或文件。 我们也不会以任何方式与任何其他产品或服务关联或共享您的帐户。 最后，您的 IT 管理员不会获取有关任何这些帐户的任何信息。

## <a name="back-up-your-account-credentials"></a>备份帐户凭据

在备份凭据之前，您必须具有：

- 一个充当恢复帐户的个人 [Microsoft 帐户](https://account.microsoft.com/account)。

- **仅对于 iOS，** 您必须拥有实际存储位置的[iCloud 帐户](https://www.icloud.com/)。

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>打开 iOS 设备的云备份

- 在 iOS 设备上，依次选择“设置”、“备份”，然后启用“iCloud 备份”。************

    帐户凭据将备份到 iCloud 帐户。

    ![iOS 设置屏幕，其中显示了 iCloud 备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>打开 Android 设备的云备份

- 在 Android 设备上，选择 **"设置"，** 选择 **"备份**"，然后打开**云备份**。

    您的帐户凭据将备份到您的云帐户。

    ![Android 设置屏幕，显示备份设置的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在新设备上恢复帐户凭据

可以从云帐户中恢复帐户凭据，但首先必须确保正在恢复的帐户不存在在 Microsoft 身份验证器应用中。 例如，如果要恢复个人 Microsoft 帐户，必须确保在身份验证器应用中没有已设置的个人 Microsoft 帐户。 此检查很重要，因此我们可以确保我们不会错误地覆盖或擦除现有帐户。

### <a name="to-recover-your-information"></a>恢复信息

1. 在移动设备上，打开 Microsoft 身份验证器应用，然后从屏幕底部选择 **"开始恢复**"。

    ![Microsoft Authenticator 应用，其中显示了“开始恢复”的位置](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. 使用备份期间所用的同一个人 Microsoft 帐户登录到恢复帐户。

    帐户凭据将恢复到新设备。

完成恢复后，你可能会注意到，旧手机和新手机上的 Microsoft Authenticator 应用中的个人 Microsoft 帐户验证码不相同。 验证码之所以不相同，是因为每台设备都有自身唯一的凭据，而在使用相关的手机登录时，这两个凭据都是有效的。

## <a name="recover-additional-accounts-requiring-more-verification"></a>恢复需要更多验证的其他帐户

如果您将推送通知用于个人或工作或学校帐户，您将获得屏幕警报，指出必须先提供其他验证，然后才能恢复您的信息。 由于推送通知需要使用绑定到特定设备且从未通过网络发送的凭据，因此必须在设备上创建凭据之前证明您的身份。

对于个人 Microsoft 帐户，可以通过输入密码以及备用电子邮件或电话号码来证明身份。 对于工作或学校帐户，必须扫描帐户提供商提供的 QR 码。

### <a name="to-provide-additional-verification-for-personal-accounts"></a>为个人帐户提供附加验证

1. 在 Microsoft 身份验证器应用的 **"帐户"** 屏幕中，选择要恢复的帐户。 在 Android 设备上，选择要恢复的帐户旁边的箭头。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    在 iOS 设备上，点击要恢复的帐户以打开帐户的全屏视图。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. 登录以恢复帐户。 在 Android 设备上，选择 **"登录以恢复**"。

    ![微软身份验证器应用程序，以在 Android 上输入您的登录信息](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    在 iOS 设备上，点按要恢复的帐户磁贴，然后点击登录和恢复的选项。 然后键入您的密码，然后确认您的电子邮件地址或电话号码作为其他验证。

    ![Microsoft 身份验证器应用，用于在 iOS 上输入您的登录信息](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>为工作或学校帐户提供附加验证

1. 登录以恢复帐户。 在 Android 设备上，选择 **"登录以恢复**"。

    ![微软身份验证器应用程序恢复在 Android 上的工作或学校帐户](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    在 iOS 设备上，点击要恢复的帐户以打开帐户的全屏视图。

    ![微软身份验证器应用在 iOS 上恢复工作或学校帐户](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. 您可以使用 QR 码恢复帐户。 选择“扫描 QR 码以恢复”，然后扫描 QR 码。****

    在 Android 上：

    ![微软身份验证器应用程序在Android上，允许您扫描你的QR码](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    在 iOS 上：

    ![iOS 上的 Microsoft 身份验证器应用，允许您扫描 QR 码](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >有关 QR 码以及如何获取 QR 代码的详细信息，请参阅[从 Microsoft 身份验证器应用开始](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)，或根据管理员是否打开安全信息设置[安全信息以使用身份验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)。
    >
    >如果这是你首次设置 Microsoft Authenticator 应用，则可能会收到一个提示，询问你是允许该应用访问你的相机 (iOS)，还是允许该应用拍摄照片和录制视频 (Android)。 你必须选择“允许”****，以便验证器应用可以访问你的相机，在下一步中拍摄 QR 码的照片。 如果你不允许使用相机，仍然可以设置验证器应用，但需要手动添加代码信息。 有关如何手动添加代码的信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

## <a name="troubleshoot-backup-and-recovery-problems"></a>排除备份和恢复问题

备份可能不可用的原因有几个

- **更改操作系统**：您的备份存储在 iCloud 中，用于 iOS 和 Microsoft 的 Android 云存储提供商中。 这意味着，如果您在 Android 和 iOS 设备之间切换，您的备份将不可用。 如果进行切换，则必须在 Microsoft 身份验证器应用中手动重新创建帐户。

- **网络问题**：如果您遇到与网络相关的问题，请确保您已连接到网络并正确登录到您的帐户。

- **帐户问题**：如果您遇到与帐户相关的问题，请确保您已正确登录到您的帐户。 对于 iOS，这意味着您必须使用与 iPhone 相同的 AppleID 帐户登录 iCloud。

- **意外删除**：您可能会从以前的设备中删除备份帐户，或者在管理云存储帐户时删除备份帐户。 在这种情况下，必须在应用中手动重新创建帐户。

- **现有的 Microsoft 身份验证器帐户**：如果您已在 Microsoft 身份验证器应用中设置了帐户，则该应用将无法恢复备份的帐户。 阻止恢复有助于确保帐户详细信息不会被过期的信息覆盖。 在这种情况下，必须先从 Authenticator 应用中设置的现有帐户删除现有的帐户信息，然后才能恢复备份。

- **备份已过期**：如果您的备份信息过期，系统可能会要求您通过再次登录到 Microsoft 恢复帐户来刷新信息。 您的恢复帐户是您最初用于存储备份的个人 Microsoft 帐户。 如果需要登录，您将在菜单或操作栏上看到一个红点，或者您将看到一个感叹号，提示您登录以完成从备份恢复。 选择该图标后，系统将提示您再次登录以更新您的信息。

## <a name="next-steps"></a>后续步骤

将帐户凭据备份并恢复到新设备后，可以继续使用 Microsoft Authenticator 应用来验证自己的身份。 有关详细信息，请参阅使用[Microsoft 身份验证器应用登录您的帐户](user-help-sign-in.md)。

## <a name="related-articles"></a>相关文章

- [什么是 Microsoft Authenticator 应用？](user-help-auth-app-overview.md)

- [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md)

- [多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/)
