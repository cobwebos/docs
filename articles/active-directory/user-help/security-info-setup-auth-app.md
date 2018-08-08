---
title: 设置安全信息以使用验证器应用 - Azure Active Directory | Microsoft Docs
description: 设置安全信息以使用 Microsoft Authenticator 应用验证身份。
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
ms.openlocfilehash: bd62ff03c3158743337ce958eb1bda7c7bd2a7f6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347628"
---
# <a name="set-up-security-info-to-use-an-authenticator-app-preview"></a>设置安全信息以使用验证器应用（预览版）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

设置安全信息需要登录到工作或学校帐户，然后完成注册过程。 如果从未设置过安全信息，将提示你立即设置。

## <a name="set-up-the-microsoft-authenticator-app"></a>设置 Microsoft Authenticator 应用

根据组织的设置，可能会在登录时提示你设置 Microsoft Authenticator 应用。 否则，若要开始在安全信息中设置 Microsoft Authenticator 应用，请执行[管理安全信息](security-info-manage-settings.md)中的步骤。

若要下载 Microsoft Authenticator 应用并了解其详细信息，请参阅 [Microsoft Authenticator 应用入门](microsoft-authenticator-app-how-to.md)。

>[!Note]
>如果不想使用 Microsoft Authenticator 应用，可在设置过程中选择其他应用。 本文使用 Microsoft Authenticator 应用。 如果看不到验证器应用选项，则可能是因为组织不允许你使用验证器应用进行验证。 如果是这样，则将需要选择另一种方法，或与管理员联系以获取进一步帮助。

### <a name="to-use-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 应用

1. 选择“验证器应用”选项。

    将显示“获取应用”向导。

    ![“获取应用”向导，初始屏幕](media/security-info/security-info-auth-app-wizard.png)

    如果不想使用 Microsoft Authenticator 应用，可从“获取应用”屏幕单击“我想要使用其他验证器应用”链接。

2. 安装 Microsoft Authenticator 应用后，选择“下一步”。

    如果出现提示，允许通知，添加一个新帐户，并选择“工作或学校帐户”。

3. 选择“**下一步**”。

    将出现“扫描 QR 码”屏幕。

    ![使用 Authenticator 应用扫描 QR 码](media/security-info/security-info-scan-qr.png)

4. 打开 Microsoft Authenticator 应用，从右上方的“自定义和控制”图标中选择“添加帐户”，然后选择“工作或学校帐户”。 

5. 如果具有 QR 码读取器应用，请扫描所提供的代码。 如果没有代码读取器应用，则可以选择“无法扫描 QR 码链接”，并将代码和 URL 手动输入到 Microsoft Authenticator 应用中。

6. 使用 Microsoft Authenticator 应用批准通知，以激活应用。

    在使用双重验证或自助密码重置时，安全信息会更新为使用 Microsoft Authenticator 应用验证身份。

## <a name="additional-security-info-options"></a>其他安全信息选项

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。
    
    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 如果需要更新安全信息，请按照[管理安全信息](security-info-manage-settings.md)一文中的说明进行操作。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。