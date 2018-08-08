---
title: 设置安全信息以使用电子邮件 - Azure Active Directory | Microsoft Docs
description: 设置安全信息，以使用工作或学校电子邮件地址验证身份。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 34696802735fd2a145cd6383dcdc7e4a4b6368e2
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391403"
---
# <a name="set-up-security-info-to-use-email-preview"></a>设置安全信息以使用电子邮件（预览）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

设置安全信息需要登录到工作或学校帐户，然后完成注册过程。 如果从未设置过安全信息，则需要立即进行此操作。

## <a name="set-up-email"></a>设置电子邮件

根据组织的设置，系统可能会在你登录时提示你将电子邮件地址添加到安全信息中。 否则，要开始在安全信息中设置电子邮件，请按[管理安全信息](security-info-manage-settings.md)中的步骤操作。

>[!Note]
>建议使用不需要网络密码即可访问的电子邮件帐户。<br>如果看不到电子邮件选项，则可能是因为组织禁止使用电子邮件进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-use-your-email-address"></a>使用电子邮件地址

1. 选择“电子邮件”选项，然后在框中键入电子邮件地址。 此电子邮件地址不能是工作或学校电子邮件。

     ![带电子邮件输入框的安全信息页](media/security-info/security-info-keep-secure-setup-email.png)

2. 替组织检查来自 Microsoft 的电子邮件，将包含框，然后选择“完成”的验证码键入“验证电子邮件”。

     ![带电子邮件验证码输入框的安全信息页](media/security-info/security-info-verify-email.png)

    >[!Note]
    >如果代表组织的你没看到来自 Microsoft 的电子邮件，请确保正确键入了电子邮件地址，然后检查垃圾邮件文件夹。

3. 在“保护帐户安全”页上，选择“完成”。

    已更新安全信息，以便在使用双重验证时通过电子邮件地址验证身份。

## <a name="additional-security-info-options"></a>其他安全信息选项

根据要执行的操作，可选择组织如何联系你来验证你的身份。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。
    
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果需要更新安全信息，请按照[管理安全信息](security-info-manage-settings.md)一文中的说明进行操作。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。