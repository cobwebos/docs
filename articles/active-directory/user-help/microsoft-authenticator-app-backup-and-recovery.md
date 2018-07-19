---
title: 使用 Microsoft Authenticator 应用进行备份和恢复 - Azure AD | Microsoft Docs
description: 了解如何使用 Microsoft Authenticator 应用备份和恢复帐户凭据。
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: a9c950ecafd2eb5f3aed1bee3707f57be6ec3b62
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058549"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 应用备份和恢复帐户凭据

**适用于：**

- iOS 设备

Microsoft Authenticator 应用可将帐户凭据和相关的应用设置（例如帐户顺序）备份到云中。 备份后，还可以使用该应用在新设备上恢复信息，从而避免潜在的锁定或重新创建帐户。

>[!IMPORTANT]
> 对于每个备份存储位置，都需要一个个人 Microsoft 帐户和一个 iCloud 帐户。 但在该存储位置中，可以备份多个帐户。 例如，你可以拥有一个个人帐户、一个学校帐户和一个第三方帐户（例如 Facebook、Google 等）。<br><br>我们只会存储你的个人和第三方帐户凭据，包括用于证明你身份的用户名和帐户验证码。 我们不会存储与你的帐户相关的其他任何信息，包括电子邮件或文件。 以外，我们不会以任何方式或者与其他任何产品或服务关联或共享你的帐户。 最后，IT 管理员不会获得有关上述任何帐户的任何信息。

## <a name="back-up-your-account-credentials"></a>备份帐户凭据
在备份凭据之前，必须具有：

- 一个充当恢复帐户的个人 [Microsoft 帐户](https://account.microsoft.com/account)。

- 一个用作实际存储位置的 [iCloud 帐户](https://www.icloud.com/)。 

要求同时登录到这两个帐户可以增强备份信息的安全性。

**启用云备份**
-   在 iOS 设备上，依次选择“设置”、“备份”，然后启用“自动备份”。

    帐户凭据将备份到 iCloud 帐户。

    ![iOS 设置屏幕，其中显示了自动备份设置的位置](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>在新设备上恢复帐户凭据
可以使用备份信息时设置的同一个 Microsoft 恢复帐户，从 iCloud 帐户恢复帐户凭据。

**恢复信息**
1.  在 iOS 设备上打开 Microsoft Authenticator 应用，并选择屏幕底部的“开始恢复”。

    ![Microsoft Authenticator 应用，其中显示了“开始恢复”的位置](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  使用备份期间所用的同一个个人 Microsoft 帐户登录到恢复帐户。

    帐户凭据将恢复到新设备。

完成恢复后，你可能会注意到，旧手机和新手机上的 Microsoft Authenticator 应用中的个人 Microsoft 帐户验证码不相同。 验证码之所以不相同，是因为每台设备都有自身唯一的凭据，而在使用相关的手机登录时，这两个凭据都是有效的。

## <a name="recover-additional-accounts-requiring-more-verification"></a>恢复需要更多验证的其他帐户
如果通过个人、工作或学校帐户使用推送通知，屏幕上会显示一条警报，指出在恢复信息之前，必须先提供附加的验证。 由于推送通知要求使用绑定到特定设备的凭据，而且永远不会通过网络发送，因此，在设备上创建凭据之前，必须先证明自己的身份。

对于个人 Microsoft 帐户，可以通过输入密码以及备用电子邮件或电话号码来证明身份。 对于工作或学校帐户，必须扫描帐户提供商提供的 QR 码。

**为个人帐户提供附加验证**
1.  在 Microsoft Authenticator 应用的“帐户”屏幕中，选择想要恢复的帐户旁边的下拉箭头。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  选择“登录以恢复”，键入密码，然后确认用作附加验证方式的电子邮件地址或电话号码。

    ![Microsoft Authenticator 应用，允许输入登录信息](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**为工作或学校帐户提供附加验证**
1.  在 Microsoft Authenticator 应用的“帐户”屏幕中，选择想要恢复的帐户旁边的下拉箭头。

    ![Microsoft Authenticator 应用，其中显示了可用帐户及其关联的下拉箭头](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  选择“扫描 QR 码以恢复”，然后扫描管理员提供的 QR 码。

    ![Microsoft Authenticator 应用，允许扫描 QR 码](./media/microsoft-authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >有关如何获取 QR 代码的详细信息，请参阅 [Microsoft Authenticator 应用入门](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)一文。

## <a name="troubleshooting-backup-and-recovery-problems"></a>排查备份和恢复问题
有几个可能的原因会导致无法备份：

-   **更改操作系统。** 备份存储在手机操作系统提供的云存储选项中，这意味着，如果在 Android 与 iOS 之间切换，则无法备份。 在这种情况下，必须在应用中手动重新创建帐户。

-   **网络或密码问题。** 确保连接到网络，并使用在最后一台 iPhone 上所用的 AppleID 登录到 iCloud 帐户。

-   **意外删除。** 你可能在上一台设备中删除了备份帐户，或者在管理云存储帐户时删除了备份帐户。 在这种情况下，必须在应用中手动重新创建帐户。

-   **现有的 Microsoft Authenticator 帐户。** 如果已在 Microsoft Authenticator 应用中设置了帐户，该应用将无法恢复备份的帐户。 阻止恢复有助于确保帐户详细信息不会被过期的信息覆盖。 在这种情况下，必须先从 Authenticator 应用中设置的现有帐户删除现有的帐户信息，然后才能恢复备份。

## <a name="next-steps"></a>后续步骤
将帐户凭据备份并恢复到新设备后，可以继续使用 Microsoft Authenticator 应用来验证自己的身份。

## <a name="related-topics"></a>相关主题
- [Microsoft Authenticator 应用入门](microsoft-authenticator-app-how-to.md)  

- [使用手机登录](microsoft-authenticator-app-phone-signin-faq.md)

- [Microsoft Authenticator 应用常见问题解答](microsoft-authenticator-app-faq.md)

- [多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/)
