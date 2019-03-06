---
title: 根据登录提示设置安全信息（预览）- Azure Active Directory | Microsoft Docs
description: 如何根据组织登录页中的提示，为工作或学校帐户设置安全信息。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3da54c4f7fae5a613d88f169d58a083f9273c0d3
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587882"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>根据登录页提示设置安全信息（预览）
如果在你登录到工作或学校帐户后，紧接着出现设置安全信息的提示，你可以遵循以下步骤进行设置。

仅当尚未设置组织所要求的安全信息时，才会显示此提示。 如果你以前设置了安全信息，但想要进行更改，可以遵循各种基于方法的操作指南文章中的步骤。 有关详细信息，请参阅[添加或更新安全信息概述](security-info-add-update-methods-overview.md)。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>登录到工作或学校帐户
登录到工作或学校帐户后，会看到一条提示，要求你提供更多信息，以便能够访问自己的帐户。

![要求提供更多信息的提示](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>使用向导设置安全信息
遵循以下步骤，根据提示为工作或学校帐户设置安全信息。

>[!Important]
>这只是设置过程的一个示例。 根据组织的要求，管理员可能设置了不同的验证方法，在设置安全信息的过程中，你需要设置这些方法。 本示例要求设置两种方法：使用 Microsoft Authenticator 应用和手机号码拨打验证电话或发送短信。

1. 根据提示选择“下一步”后，会出现“保护帐户向导”，其中显示了管理员和组织要求设置的第一种方法。 在本示例中，第一种方法是 Microsoft Authenticator 应用。

    >[!Note]
    >若要使用除 Microsoft Authenticator 应用以外的验证器应用，请选择“我想要使用其他验证器应用”链接。
    
    >如果你的组织允许选择除验证器应用以外的其他方法，则可以选择“我想要设置其他方法”链接。

    ![“保护帐户向导”，其中显示了验证器应用下载页](media/security-info/securityinfo-prompt-get-auth-app.png)

2. 选择“立即下载”以在移动设备上下载并安装 Microsoft Authenticator 应用，然后选择“下一步”。 有关如何下载和安装该应用的详细信息，请参阅[下载并安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)。

    ![“保护帐户向导”，其中显示了验证器的“设置帐户”页](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. 在移动设备上安装 Microsoft Authenticator 应用时，请将“设置帐户”页保持打开。

4. 打开 Microsoft Authenticator 应用，选择允许通知（如果出现相关提示），通过右上角的“自定义和控制”图标中选择“添加帐户”，然后选择“工作或学校帐户”。

5. 返回到计算机上的“设置帐户”页，然后选择“下一步”。

    此时会出现“扫描 QR 码”页。

    ![使用 Authenticator 应用扫描 QR 码](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. 使用在步骤 5 中创建工作或学校帐户后显示在移动设备上的 Microsoft Authenticator 应用 QR 码读取器扫描所提供的代码。

    Authenticator 应用应该会成功添加你的工作或学校帐户，而无需提供其他任何信息。 但是，如果 QR 码读取器无法读取该代码，则你可以选择“无法扫描 QR 码链接”，并将代码和 URL 手动输入到 Microsoft Authenticator 应用中。 有关手动添加代码的详细信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

7. 在计算机上的“扫描 QR 码”页上选择“下一步”。

    随即有一条通知发送到移动设备上的 Microsoft Authenticator 应用，以测试你的帐户。

    ![使用 Authenticator 应用测试帐户](media/security-info/securityinfo-prompt-test-app.png)

8. 在 Microsoft Authenticator 应用中确认通知，然后选择“下一步”。

    ![成功通知，指出正在连接应用和帐户](media/security-info/securityinfo-prompt-auth-app-success.png).

    在使用双重验证或密码重置时，安全信息默认会更新为使用 Microsoft Authenticator 应用来验证你的身份。

9. 在“手机”设置页上，选择是要接收短信还是电话呼叫，然后选择“下一步”。 本示例使用短信，因此，必须使用可接收短信的设备的电话号码。

    ![开始设置电话号码以接收短信](media/security-info/securityinfo-prompt-text-msg.png)

    随即会将一条短信发送到该电话号码。 如果想要接收电话呼叫，设置过程是相同的。 但是，你会收到包含操作说明的电话呼叫，而不是短信。

10. 输入发送到移动设备的短信中提供的代码，然后选择“下一步”。

    ![使用短信测试帐户](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. 查看成功通知，然后选择“完成”。

    ![成功通知](media/security-info/securityinfo-prompt-call-answered-success.png)

    在使用双重验证或密码重置时，安全信息会更新为使用短信作为备用方法来验证你的身份。

12. 查看“成功”页，以确认成功为安全信息设置了 Microsoft Authenticator 应用和手机（短信或电话呼叫）验证方法，然后选择“完成”。

    ![向导中的“成功完成”页](media/security-info/securityinfo-prompt-setup-success.png)

## <a name="next-steps"></a>后续步骤

- 若要更改、删除或更新默认的安全信息方法，请参阅：

    - [为验证器应用设置安全信息](security-info-setup-auth-app.md)。

    - [为短信设置安全信息](security-info-setup-text-msg.md)。

    - [设置安全信息以使用电话呼叫](security-info-setup-phone-number.md)。

    - [设置安全信息以使用电子邮件](security-info-setup-email.md)。

    - [设置安全信息以使用预定义的安全性问题](security-info-setup-questions.md)。

- 有关如何使用指定的方法进行登录的信息，请参阅[如何登录](user-help-sign-in.md)。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。