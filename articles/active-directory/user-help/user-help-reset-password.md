---
title: 使用安全信息重置密码 - Azure Active Directory | Microsoft Docs
description: 如果忘记密码，如何使用安全信息和双重验证来重置自己的密码。
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
ms.openlocfilehash: a95d5adb4ee0eb8aa71f7759e3f2b5520bda5ff4
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347629"
---
# <a name="reset-your-work-or-school-password"></a>重置工作或学校密码

如果忘记了密码、从来没有从公司支持人员处得到过密码，或者帐户被锁定，则可使用安全信息和移动设备来重置密码。

>[!NOTE]
>如果知道密码而只是想对其进行更改，可以转到本文中的[更改密码](#how-to-change-your-password)步骤。<br><br>
>若要取回个人帐户（例如 Xbox、hotmail.com 或 outlook.com），请尝试[当无法登录到你的 Microsoft 帐户时](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)文章中的建议。

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>如何重置或解锁工作或学校帐户的密码

如果无法访问 Azure Active Directory (Azure AD) 帐户，可能是以下某一原因导致的：

- 密码不起作用需要重置，或

- 知道密码但帐户被锁定，需要解锁帐户。

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>若要重置密码并取回帐户

1. 在“输入密码”屏幕中，选择“忘记密码”。

2. 在“取回帐户”屏幕中，键入工作或学校“用户 ID”（例如电子邮件地址），通过输入在屏幕上看到的字符证明自己不是机器人，然后选择“下一步”。

   ![重新登录帐户屏幕](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >如果管理员尚未为你开启重置自己的密码的功能，你将看到“联系管理员”链接而不是“取回帐户”屏幕。 此链接可让你通过电子邮件或 Web 门户就重置密码与管理员联系。

3. 选择以下某一方法来验证身份和更改密码。 根据管理员对组织的设置，你可能需要再次进行此过程，添加信息以完成第二步验证。

    ![返回你的帐户，验证步骤 #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >根据管理员对组织的设置，其中某些验证选项可能不可用。 必须事先使用以下至少一种方法来设置移动设备，以便进行验证。<br><br>此外，新密码还可能需要满足特定的强度要求。 强密码通常具有 8 到 16 个字符，包括大写和小写字符、至少一个数字和至少一个特殊字符。

- **使用电子邮件地址重置密码。** 向之前在双重验证或安全信息中设置的电子邮件地址发送一封电子邮件。 如果管理员已启用安全信息体验，则可以在[设置安全信息以使用电子邮件（预览版）](security-info-setup-email.md)一文中找到有关设置电子邮件地址的详细信息。 如果尚未使用安全信息，可在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置电子邮件地址的详细信息。 

    1. 选择“向备用电子邮件发送电子邮件”，然后选择“电子邮件”。

    2. 将电子邮件中的验证码键入到框中，然后选择“下一步”。
    
    3. 键入并确认新密码，然后选择“完成”。

- **使用短信重置密码。** 向之前在安全信息中设置的电话号码发送短信。 如果管理员已启用安全信息体验，可以在[设置安全信息以使用短信消息（预览版）](security-info-setup-text-msg.md)一文中找到有关设置短信消息的详细信息。 如果尚未使用安全信息，可以在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置短信消息的详细信息。

    1. 选择“向移动电话发送短信”，键入电话号码，并选择“发送短信”。

    2. 将短信中的验证码键入到框中，然后选择“下一步”。

    3. 键入并确认新密码，然后选择“完成”。

- **使用电话号码重置密码。** 向之前在安全信息中设置的电话号码发送短信。 如果管理员已启用安全信息体验，可以在[设置安全信息以使用电话（预览版）](security-info-setup-phone-number.md)一文中找到有关设置电话号码的详细信息。 如果尚未使用安全信息，可以在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置电话号码的详细信息。

    1. 选择“呼叫移动电话”，键入电话号码，并选择“呼叫”。

    2. 接听电话并按照说明进行身份验证，然后选择“下一步”。

    3. 键入并确认新密码，然后选择“完成”。

- **使用安全性问题重置密码。** 显示在安全信息中设置的安全性问题列表。 如果管理员已启用安全信息体验，可以在[设置安全信息以使用预定义的安全性问题（预览版）](security-info-setup-questions.md)一文中找到有关设置安全性问题的详细信息。 如果尚未使用安全信息，可以在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置安全性问题的详细信息。

    1. 选择“回答安全性问题”，回答问题，并选择“下一步”。

    2. 键入并确认新密码，然后选择“完成”。

- **使用来自验证器应用的通知来重置密码。** 向验证器应用发送审批通知。 如果管理员已启用安全信息体验，可以在[设置安全信息以使用身份验证应用（预览版）](security-info-setup-auth-app.md)一文中找到有关设置验证器应用以发送通知的详细信息。 如果尚未使用安全信息，可以在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置验证器应用以发送通知的详细信息。

    1. 选择“在验证器应用上批准通知”，然后选择“发送通知”。

    2. 从验证器应用批准登录。

    3. 键入并确认新密码，然后选择“完成”。

- **使用来自验证器应用的代码来重置密码。** 接受身份验证应用提供的随机代码。 如果管理员已启用安全信息体验，可以在[设置安全信息以使用身份验证应用（预览版）](security-info-setup-auth-app.md)一文中找到有关设置验证器应用以提供代码的详细信息。 如果尚未使用安全信息，可以在[设置帐户以便进行双重验证](multi-factor-authentication-end-user-first-time.md)一文中找到有关设置验证器应用以提供代码的详细信息。

    1. 选择“输入来自验证器应用的代码”，然后选择“发送通知”。

    2. 打开验证器应用，在框中键入帐户的验证码，然后选择“下一步”。

    3. 键入并确认新密码，然后选择“完成”。

    4. 收到指示密码已重置的消息后，可以使用新密码登录到帐户。
        
    如果仍无法访问帐户，应联系组织管理员，寻求进一步的帮助。

重置密码后，你可能会收到一封来自“Microsoft 代表\<组织>”之类帐户的确认电子邮件。 如果收到类似电子邮件，但最近未重置密码，必须立即联系组织管理员。

## <a name="how-to-change-your-password"></a>如何更改密码

如果只想更改密码，可通过 Office 365 门户、Azure 访问面板或 Windows 10 登录页来执行此操作。

### <a name="to-change-your-password-using-the-office-365-portal"></a>使用 Office 365 门户更改密码

如果通常使用 Office 门户来访问应用，则请使用此方法：

1. 使用现有密码登录到 [Office 365 帐户](https://www.office.com)。

2. 选择右上角的个人资料，然后选择“查看帐户”。

3. 选择“安全和隐私” > “密码”。

4. 键入旧密码，创建并确认新密码，然后选择“提交”。

### <a name="to-change-your-password-from-the-azure-access-panel"></a>从 Azure 访问面板更改密码

如果通常使用 Azure 访问面板 (MyApps) 来访问应用，则请使用此方法：

1. 使用现有密码登录到 [Azure 访问面板](https://myapps.microsoft.com/)。

2. 选择右上角的个人资料，然后选择“个人资料”。

3. 选择“更改密码”。

4. 键入旧密码，创建并确认新密码，然后选择“提交”。

### <a name="to-change-your-password-at-windows-sign-in"></a>在 Windows 登录页更改密码

如果管理员已启用此功能，则可以在 Windows 7、Windows 8、Windows 8.1 或 Windows 10 登录屏幕上看到“重置密码”链接。

1. 选择“重置密码”链接启动密码重置过程，而无需使用常规的基于 Web 的体验。

2. 确认用户 ID，然后选择“下一步”。

3. 选择并确认用于验证的联系方式。 如有必要，选择不同于前一验证选项的第二个验证选项，填写必要的信息。

4. 在“创建新密码”页上，键入并确认新密码，然后选择“下一步”。

    强密码通常具有 8 到 16 个字符，包括大写和小写字符、至少一个数字和至少一个特殊字符。

5. 收到指示密码已重置的消息后，可以选择“完成”。

    如果仍无法访问帐户，应联系组织管理员，寻求进一步的帮助。

## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法

以下是一些常见的错误案例及其解决方法：

|问题|Description|解决方案|
| --- | --- | --- |
|尝试更改我的密码时收到错误。 |你的密码包含单词、短语或模式，这些使你的密码容易被猜出。| 请使用更强的密码重试。|
|在输入我的用户 ID 后，我转到一个页面，该页面指示“请联系你的管理员”。|Microsoft 确定你的用户帐户密码由你的管理员在本地环境中管理。 因此，你无法使用“无法访问帐户”链接重置你的密码。 |请联系管理员获取进一步的帮助。|
|在输入我的用户 ID 后，我收到一个错误，指示“你的帐户未针对密码重置进行启用”。|你的管理员尚未设置你的帐户，因此，你可以重置自己的密码。|管理员尚未通过“无法访问帐户”链接为组织启用密码重置功能，或未授权你使用该功能。<br><br> 若要重置密码，必须选择“联系管理员”链接，向公司管理员发送电子邮件并告知他们你想要重置密码。|
|在输入我的用户 ID 后，我收到一个错误，指示“我们无法验证你的帐户”。|登录过程无法验证帐户信息。|可能看到此消息的原因有两个。<br><br>1.管理员为组织启用了密码重置功能，但你并未注册使用该服务。 若要注册以便进行密码重置，请根据你的验证方法参阅以下某篇文章：[设置安全信息以使用验证器应用（预览版）](security-info-setup-auth-app.md)、[设置安全信息以使用电话（预览版）](security-info-setup-phone-number.md)、[设置安全信息以使用短信消息（预览版）](security-info-setup-text-msg.md)、[设置安全信息以使用电子邮件（预览版）](security-info-setup-email.md)或[设置安全信息以使用安全性问题（预览版）](security-info-setup-questions.md)。<br><br>2.管理员未为组织启用密码重置功能。 在此情况下，必须选择“联系管理员”链接以向管理员发送电子邮件，请求重置密码。|

## <a name="next-steps"></a>后续步骤

- 编辑安全信息，以添加或更改[管理安全信息（预览版）](security-info-manage-settings.md)一文中的验证方法。 

- 在[安全信息（预览版）概述](user-help-security-info-overview.md)一文中了解安全信息。

- 在[双重验证概述](user-help-two-step-verification-overview.md)一文中了解双重验证。 

- 如果丢失或忘记密码，请从[密码重置门户](https://passwordreset.microsoftonline.com/)重置密码

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。