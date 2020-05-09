---
title: 将电子邮件地址设置为验证方法-Azure AD
description: 如何设置安全信息（预览版）页，以使用电子邮件地址作为验证方法来验证身份。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 4c3dd3e7e0bc899b87a16f66dc4d049a2876dbb4
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628361"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>将电子邮件地址设置为验证方法

可以按照以下步骤添加密码重置方法。 在首次设置此项后，可以返回到“安全信息”页以添加、更新或删除安全信息****。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>如果看不到电子邮件选项，则可能是你的组织不允许你将此选项用于密码重置方法。 在这种情况下，需要选择另一种方法或与组织的技术支持部门联系以获得更多帮助。

## <a name="security-vs-password-reset-verification"></a>安全与密码重置验证

安全信息方法既用于双因素安全验证，又用于密码重置。 但是，并非所有方法都可以用于这二者。

| 方法 | 用于 |
| ------ | -------- |
| Authenticator 应用 | 双重验证和密码重置身份验证。 |
| 短信 | 双重验证和密码重置身份验证。 |
| 电话呼叫 | 双重验证和密码重置身份验证。 |
| 安全密钥 | 双重验证和密码重置身份验证。 |
| 电子邮件帐户 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |
| 安全提问 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |

## <a name="set-up-your-email-address-from-the-security-info-page"></a>在安全信息页面设置电子邮件地址

根据组织的设置，可以将电子邮件地址用作安全信息方法之一。

>[!Note]
>建议使用不需要网络密码即可访问的电子邮件地址。 如果看不到电子邮件选项，则可能是因为组织禁止使用电子邮件进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-set-up-your-email-address"></a>设置电子邮件地址

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”****************。

    ![“安全信息”页，其中突出显示了“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页上，从下拉列表中选择“电子邮件”，然后选择“添加”************。

    ![“添加方法”框，已选中“电子邮件”](media/security-info/securityinfo-myprofile-addemail.png)

4. 在“电子邮件”页，键入电子邮件地址（例如 alain@gmail.com）并选择“下一步”********。

    ![添加电话号码并选择电话呼叫](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >此电子邮件地址不能是工作或学校电子邮件。

5. 输入发送到指定电子邮件地址的代码，然后选择“下一步”****。

    ![添加电话号码并选择短信](media/security-info/securityinfo-myprofile-emailcode.png)

    已更新安全信息，以便在使用密码重置时通过电子邮件地址验证身份。

## <a name="delete-your-email-address-from-your-security-info-methods"></a>从安全信息方法中删除电子邮件地址

如果不想再将电子邮件地址用作安全信息方法，可以从“安全信息”页将其删除****。

>[!Important]
>如果错误地删除了电子邮件地址，则无法撤消该操作。 必须按照本文[设置电子邮件地址](#set-up-your-email-address-from-the-security-info-page)部分中的步骤再次添加该方法。

### <a name="to-delete-your-email-address"></a>删除电子邮件地址

1. 在“安全信息”页上，选择“电子邮件”选项旁边的“删除”链接************。

    ![从安全信息中删除电话方法的链接](media/security-info/securityinfo-myprofile-emaildelete.png)

2. 从确认框中选择“是”，以删除“电子邮件”帐户********。 删除电子邮件帐户后，该帐户将从安全信息中删除，并从“安全信息”页消失****。

## <a name="additional-security-info-methods"></a>其他安全信息方法

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **验证器应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **安全密钥。** 注册与 Microsoft 兼容的安全密钥，并将其与用于双重验证或密码重置的 PIN 一起使用。 有关如何使用安全密钥验证身份的分步说明，请参阅[设置安全信息以使用安全密钥](security-info-setup-security-key.md)。

- **安全问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
