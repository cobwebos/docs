---
title: 设置安全信息以使用短信消息 - Azure Active Directory | Microsoft Docs
description: 设置安全信息以使用短信 (SMS) 消息验证身份。
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
ms.openlocfilehash: f20bcb4245a2870d8eaa442c6f747ee533b362db
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347630"
---
# <a name="set-up-security-info-to-use-text-messaging-preview"></a>设置安全信息以使用短信消息（预览版）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

设置安全信息需要登录到工作或学校帐户，然后完成注册过程。 如果从未设置过安全信息，将提示你立即设置。

## <a name="set-up-text-messaging"></a>设置短信

根据组织的设置，可能会在登录时提示你将短信消息添加到安全信息中。 否则，若要开始在安全信息中设置短信消息，请执行[管理安全信息](security-info-manage-settings.md)中的步骤。

短信选项是电话选项的一部分，因此，设置该选项的方式与设置电话号码的方式相同，但不同的是选择使用短信而不是让 Microsoft 呼叫你。 如果看不到电话选项，则可能是因为组织不允许你使用电话号码进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-use-a-text-message"></a>使用短信

1. 选择“电话”选项。

    将显示“设置电话”向导。

    ![设置国家或地区代码及电话号码](media/security-info/security-info-keep-secure-setup-text.png)

2. 从下拉框中选取“国家或地区”，将电话号码（包括区域代码，如果适用）键入到“电话号码”框中，选择“以短信形式向我发送代码”选项，然后选择“下一步”。

    你将收到一条包含需要输入到验证页的代码的短信。

    ![要输入短信代码的验证页](media/security-info/security-info-keep-secure-verify-text-msg.png)

    在使用双重验证或自助密码重置时，安全信息会更新，以便发送用于进行身份验证的短信。

    >[!Note]
    >如果想要接听电话而不是接收短信，请按照[设置安全信息以使用电话](security-info-setup-phone-number.md)一文中的步骤进行操作。

## <a name="additional-security-info-options"></a>其他安全信息选项

根据要执行的操作，可选择组织如何联系你来验证你的身份。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。
   
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

## <a name="next-steps"></a>后续步骤

- 如果需要更新安全信息，请按照[管理安全信息](security-info-manage-settings.md)一文中的说明进行操作。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。