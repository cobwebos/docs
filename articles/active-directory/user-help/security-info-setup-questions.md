---
title: 设置安全信息（预览版）以使用安全性问题 - Azure Active Directory | Microsoft Docs
description: 如何设置安全信息，以使用预定义的安全性问题验证身份。
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
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60474556"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>设置安全信息（预览版）以使用安全性问题
可以按照以下步骤添加密码重置方法。 在首次设置此项后，可以返回到“安全信息”页以添加、更新或删除安全信息。

设置密码重置方法后，还必须使用[验证器应用](security-info-setup-auth-app.md)、[短信](security-info-setup-text-msg.md)或[电话](security-info-setup-phone-number.md)设置双重验证方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>在“安全信息”页上设置安全性问题
根据组织的设置，你可以选择并回答几个安全性问题作为某个安全信息方法。 管理员设置你需要选择和回答的安全性问题的数量。

如果使用安全提问，我们建议结合另一种方法使用这些提问。 因为某些人可能知道问题的答案，因此这安全性问题可能比其他方法更不安全。

> [!Note]
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。
> 
> 如果看不到安全性问题选项，可能是因为组织不允许你使用安全性问题进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。
> 
> 管理员帐户不能使用安全问题，如密码重置方法。 如果您将看不到这些选项的管理员级帐户登录。

### <a name="to-set-up-your-security-questions"></a>设置安全性问题

1. 登录工作或学校帐户，然后转到 https://myprofile.microsoft.com/ 页面。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”。

    ![“安全信息”页面，突出显示“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页上，从下拉列表中选择“安全性问题”，然后选择“添加”。

    ![使用所选的安全性问题添加方法框](media/security-info/securityinfo-myprofile-addquestions.png)

4. 在“安全性问题”页上，选择并回答安全性问题，然后选择“保存”。

    ![添加电话号码并选择电话](media/security-info/securityinfo-myprofile-securityquestions.png)

    已更新安全信息，以便在使用密码重置时通过安全性问题验证身份。

## <a name="delete-security-questions-from-your-security-info-methods"></a>在安全信息方法中删除安全性问题
如果不想再将安全性问题用作安全信息方法，则可以从“安全信息”页将其删除。

>[!Important]
>如果错误地删除了你的安全性问题，则无法将其撤消。 必须按照本文[设置安全性问题](#set-up-your-security-questions-from-the-security-info-page)部分中的步骤再次添加该方法。

### <a name="to-delete-your-security-questions"></a>删除安全性问题

1. 在“安全信息”页上，选择“安全性问题”选项旁的“删除”链接。

    ![从安全信息中删除电话方法的链接](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. 从确认框中选择“是”，以删除“安全性问题”。 删除安全性问题后，该方法将从你的安全信息中删除，并从“安全信息”页面消失。

## <a name="additional-security-info-methods"></a>其他安全信息方法
基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。
   
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
