---
title: 设置文本消息作为验证方法-Azure AD
description: 如何设置安全信息（预览版）页，以使用短信作为验证方法来验证您的身份。
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
ms.openlocfilehash: 32ec35094740f05a2c2a6f17ab5dafa29d30a0f1
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628804"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>设置文本消息作为验证方法

可遵循以下步骤添加双重验证和密码重置方法。 在首次设置此项后，可以返回到“安全信息”页以添加、更新或删除安全信息****。

如果在登录工作或学校帐户后系统提示立即进行此项设置，请参阅[根据登录页提示设置安全信息](security-info-setup-signin.md)一文中的详细步骤。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>如果看不到电话选项，你的组织可能不允许你使用此选项进行验证。 在这种情况下，需要选择另一种方法或与组织的技术支持部门联系以获得更多帮助。

## <a name="set-up-text-messages-from-the-security-info-page"></a>从“安全信息”页设置文本消息

根据组织的设置，可使用短信作为安全信息方法之一。 短信选项是电话选项的一部分，因此，设置该选项的方式与设置电话号码的方式相同，但不同的是选择使用短信而不是让 Microsoft 呼叫你。

>[!Note]
>如果想要接听电话而不是接收短信，请按照[设置安全信息以使用电话](security-info-setup-phone-number.md)一文中的步骤进行操作。

### <a name="to-set-up-text-messages"></a>若要设置短信

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”****************。

    ![“安全信息”页，其中突出显示了“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页上，从下拉列表中选择“电话”，然后选择“添加”************。

    ![添加方法框，选中电话](media/security-info/securityinfo-myprofile-addphonetext.png)

4. 在“电话”页上，键入移动设备的电话号码，选择“以短信形式向我发送验证码”，然后选择“下一步”************。

    ![添加电话号码并选择短信](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. 输入以短信形式发送到移动设备的验证码，然后选择“下一步”****。

    ![添加电话号码并选择短信](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    此时，页面更改为显示成功。

    ![成功通知，连接电话号码，接收短信的选项，以及你的帐户](media/security-info/securityinfo-myprofile-phonetext-success.png)

    安全信息已更新，你可以在使用双重验证或密码重置时使用短信验证身份。 如果要将短信作为默认方法，请参阅本文的[更改默认的安全信息方法](#change-your-default-security-info-method)部分。

## <a name="delete-text-messaging-from-your-security-info-methods"></a>从安全信息方法中删除短信

如果不想再将短信用作安全信息方法，可从“安全信息”页将其删除****。

>[!Important]
>如果错误地删除了短信，则无法将其撤消。 你必须按照本文[设置文本信息](#set-up-text-messages-from-the-security-info-page)部分中的步骤再次添加该方法。

### <a name="to-delete-text-messaging"></a>删除短信

1. 在“安全信息”页上，选择“电话”选项旁边的“删除”链接************。

    ![从安全信息中删除电话和短信方法的链接](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 从确认框中选择“是”，以删除**电话**号码。**** 删除电话号码后，该帐户将从你的安全信息中删除，并从“安全信息”页消失****。 如果“电话”是默认方法，则默认方法将更改为另一种可用方法****。

## <a name="change-your-default-security-info-method"></a>更改默认的安全信息方法

如果要在使用双因素验证或密码重置请求登录到工作或学校帐户时使用文本消息作为默认方法，则可以从 "**安全信息**" 页设置。

### <a name="to-change-your-default-security-info-method"></a>更改默认安全信息方法

1. 在“安全信息”页上，选择“默认登录方法”信息旁边的“更改”链接************。

    ![更改默认登录方法的链接](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 从可用方法的下拉列表中选择“电话 - 短信 (_your_phone_number_)”，然后选择“确认”**********。

    ![选择默认登录的方法](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    用于登录的默认方法更改为“电话 - 短信 (_your_phone_number _)”******。

## <a name="additional-security-info-methods"></a>其他安全信息方法

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **验证器应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **安全密钥。** 注册与 Microsoft 兼容的安全密钥，并将其与用于双重验证或密码重置的 PIN 一起使用。 有关如何使用安全密钥验证身份的分步说明，请参阅[设置安全信息以使用安全密钥](security-info-setup-security-key.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
