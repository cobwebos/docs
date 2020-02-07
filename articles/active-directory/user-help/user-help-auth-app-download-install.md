---
title: 下载并安装 Microsoft Authenticator 应用-Azure AD
description: 下载并安装 Microsoft Authenticator 应用，以便在使用双重验证时验证您的身份。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9d8ea02c91dc6aec41a1b548eff148e85111fc32
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063894"
---
# <a name="download-and-install-the-microsoft-authenticator-app"></a>下载并安装 Microsoft Authenticator 应用

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)中查找有关如何设置和管理 Azure Active Directory (Azure AD) 环境的详细信息。

Microsoft Authenticator 应用可帮助你在使用双重验证的情况下登录到帐户。 双重验证可帮助你更安全地访问帐户，尤其是在查看敏感信息时。 由于密码可能会遗忘、失窃或者泄露，双重验证作为一种附加的安全保障步骤，可帮助保护帐户的安全，因为其他人更难破解它。

可通过多种方式使用 Microsoft Authenticator 应用，包括：

- **双重验证。** 标准验证方法，其中一个因素是你的密码。 使用你的用户名和密码登录后，可以批准通知或输入提供的验证码。

- **手机登录。** 双重验证的一个版本，允许使用用户名和移动设备并结合指纹、人脸或 PIN 来进行登录，而无需输入密码。

- **代码生成。** 作为支持验证器应用的任何其他帐户的代码生成器。

> [!Important]
> Microsoft Authenticator 应用可与使用双因素验证并支持基于时间的一次性密码 (TOTP) 标准的任何帐户配合使用。
>
> 组织可能要求你使用验证器应用来登录和访问组织数据和文档。 尽管用户名可能会在应用中显示，但在完成注册过程前，该帐户实际上并未设置为用作验证方法。 有关详细信息，请参阅[添加工作或学校帐户](user-help-auth-app-add-work-school-account.md)。
> 
> [!NOTE]
> 如果在登录帐户时遇到问题，请参阅[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429)以获取帮助。  详细了解如何解决在尝试登录 Microsoft 帐户时收到“[该 Microsoft 帐户不存在](https://support.microsoft.com/help/13811)”消息的问题。

## <a name="download-and-install-the-app"></a>下载并安装应用

根据你的操作系统安装 Microsoft Authenticator 应用的最新版本：

- **Google Android。** 在 Android 设备上，转到 Google Play 来[下载并安装 Microsoft Authenticator 应用](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator)。

- **Apple iOS。** 在 Apple iOS 设备上，转到 App Store 来[下载并安装 Microsoft Authenticator 应用](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

>[!Important]
>如果你当前不在移动设备上，则仍可通过向自己发送[Microsoft Authenticator 页面](https://www.microsoft.com/en-us/account/authenticator)中的下载链接来获取 Microsoft Authenticator 应用。

## <a name="next-steps"></a>后续步骤

下载并安装该应用后，必须添加各种帐户。 有关详细信息，请参阅：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **安全密钥。** 注册与 Microsoft 兼容的安全密钥，并将其与用于双重验证或密码重置的 PIN 一起使用。 有关如何使用安全密钥验证身份的分步说明，请参阅[设置安全信息以使用安全密钥](security-info-setup-security-key.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。
