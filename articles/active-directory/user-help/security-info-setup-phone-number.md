---
title: 设置安全信息以使用电话呼叫 - Azure Active Directory | Microsoft Docs
description: 设置安全信息，以使用移动设备或工作电话号码验证身份。
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
ms.openlocfilehash: 701a47a3907c77dbf5d51692e5d1ddc0c49ab985
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390096"
---
# <a name="set-up-security-info-to-use-phone-calls-preview"></a>设置安全信息以使用电话呼叫（预览）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

设置安全信息需要登录到工作或学校帐户，然后完成注册过程。 如果从未设置过安全信息，则需要立即进行此操作。

## <a name="set-up-phone-calls"></a>设置电话呼叫

根据组织的设置，可能会在登录时提示你将电话号码添加到安全信息中。 否则，若要开始在安全信息中设置电话呼叫，请执行[管理安全信息](security-info-manage-settings.md)中的步骤。

>[!Note]
>安全信息不支持使用电话分机。 即使添加正确的“+1 4255551234X12345”格式，在拨出电话前也会删除分机。<br>如果看不到电话选项，则可能是因为组织不允许你使用电话呼叫进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-use-your-phone-number"></a>使用你的电话号码

1. 选择“电话”选项。

    将显示“设置电话”向导。

    ![设置国家或地区代码及电话号码](media/security-info/security-info-keep-secure-setup-phone.png)

2. 从下拉框中选取“国家/地区或区域”，将电话号码（包括区域代码，如果适用）键入到“电话号码”框中，选择“呼叫我”选项，然后选择“下一步”。

    你将接到电话呼叫，以确保键入正确的电话号码。 此时，系统将提示你按井号 (#) 键，确认并完成设置。

    ![验证手机屏幕，屏幕上显示已成功接听呼叫](media/security-info/security-info-keep-secure-verify-phone-call.png)

    在使用双重验证或自助密码重置时，安全信息会更新，以便使用电话号码验证身份。

    >[!Note]
    >如果想要接收短信而不是通过移动设备接听电话，请按照[设置安全信息，以使用短信 (SMS)](security-info-setup-text-msg.md) 一文中的步骤进行操作。

## <a name="additional-security-info-options"></a>其他安全信息选项

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息，以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。
    
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果需要更新安全信息，请按照[管理安全信息](security-info-manage-settings.md)一文中的说明进行操作。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。