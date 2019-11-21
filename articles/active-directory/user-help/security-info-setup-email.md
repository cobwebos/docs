---
title: Set up an email address as your authentication method - Azure AD
description: How to set up the Security info page to verify your identity using an email address as your authentication method.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03b53760f27f853f34397ac9047f18a37e1bc845
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231780"
---
# <a name="set-up-an-email-address-as-your-authentication-method-from-the-security-info-preview-page"></a>Set up an email address as your authentication method from the Security info (preview) page

可以按照以下步骤添加密码重置方法。 在首次设置此项后，可以返回到“安全信息”页面以添加、更新或删除安全信息。

设置密码重置方法后，还必须使用[验证器应用](security-info-setup-auth-app.md)、[短信](security-info-setup-text-msg.md)或[电话](security-info-setup-phone-number.md)设置双重验证方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>在安全信息页面设置电子邮件地址

根据组织的设置，可以将电子邮件地址用作安全信息方法之一。

>[!Note]
>建议使用不需要网络密码即可访问的电子邮件地址。 如果看不到电子邮件选项，则可能是因为组织禁止使用电子邮件进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-set-up-your-email-address"></a>设置电子邮件地址

1. 登录工作或学校帐户，然后转到 https://myprofile.microsoft.com/ 页面。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”。

    ![“安全信息”页面，突出显示“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页上，从下拉列表中选择“电子邮件”，然后选择“添加”。

    ![“添加方法”框，已选中“电子邮件”](media/security-info/securityinfo-myprofile-addemail.png)

4. 在“电子邮件”页，键入电子邮件地址（例如 alain@gmail.com）并选择“下一步”。

    ![添加电话号码并选择电话](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >此电子邮件地址不能是工作或学校电子邮件。

5. 输入发送到指定电子邮件地址的代码，然后选择“下一步”。

    ![添加电话号码并选择短信](media/security-info/securityinfo-myprofile-emailcode.png)

    已更新安全信息，以便在使用密码重置时通过电子邮件地址验证身份。

## <a name="delete-your-email-address-from-your-security-info-methods"></a>从安全信息方法中删除电子邮件地址

如果不想再将电子邮件地址用作安全信息方法，可以从“安全信息”页将其删除。

>[!Important]
>如果错误地删除了电子邮件地址，则无法撤消该操作。 必须按照本文[设置电子邮件地址](#set-up-your-email-address-from-the-security-info-page)部分中的步骤再次添加该方法。

### <a name="to-delete-your-email-address"></a>删除电子邮件地址

1. 在“安全信息”页上，选择“电子邮件”选项旁边的“删除”链接。

    ![从安全信息中删除电话方法的链接](media/security-info/securityinfo-myprofile-emaildelete.png)

2. 从确认框中选择“是”，以删除“电子邮件”帐户。 删除电子邮件帐户后，该帐户将从安全信息中删除，并从“安全信息”页消失。

## <a name="additional-security-info-methods"></a>其他安全信息方法

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **Security key.** Register your Microsoft-compatible security key and use it along with a PIN for two-step verification or password reset. For step-by-step instructions about how to verify your identity with a security key, see [Set up security info to use a security key](security-info-setup-security-key.md).

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果你丢失或忘记了密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤来重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
