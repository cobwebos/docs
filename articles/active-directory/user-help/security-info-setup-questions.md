---
title: 设置安全信息以使用安全性问题 - Azure Active Directory | Microsoft Docs
description: 设置安全信息，以使用预定义的安全性问题验证身份。
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
ms.openlocfilehash: f1905e66bbe823f6c7c9c35d6540f3c8ca1d6ccd
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389627"
---
# <a name="set-up-security-info-to-use-pre-defined-security-questions-preview"></a>设置安全信息以使用预定义的安全性问题（预览）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

设置安全信息需要登录到工作或学校帐户，然后完成注册过程。 如果从未设置过安全信息，则需要立即进行此操作。

## <a name="set-up-security-questions"></a>设置安全问题

根据组织的设置，系统可能会在你登录时提示你将安全性问题添加到安全信息中。 否则，要开始在安全信息中设置安全性问题，请按[管理安全信息](security-info-manage-settings.md)中的步骤操作。

如果使用安全提问，我们建议结合另一种方法使用这些提问。 因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。

>[!Note]
>安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。<br>如果看不到安全性问题选项，可能是因为组织不允许你使用安全性问题进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-choose-and-answer-your-security-questions"></a>选择和回答安全性问题

1. 选择“安全性问题”，然后选择要回答的安全性问题。 

    必须选择的安全性问题数目由管理员决定。

    ![在安全信息页上，选择你的安全性问题](media/security-info/security-info-keep-secure-setup-pick-questions.png)

2. 为所选问题提供答案，然后选择“完成”。

## <a name="additional-security-info-options"></a>其他安全信息选项

根据要执行的操作，可选择组织如何联系你来验证你的身份。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息，以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。
   
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果需要更新安全信息，请按照[管理安全信息](security-info-manage-settings.md)一文中的说明进行操作。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。