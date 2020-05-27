---
title: 什么是 Microsoft Authenticator 应用？ - Azure Active Directory | Microsoft Docs
description: 了解 Microsoft Authenticator 应用，包括其概念、工作原理，以及本部分包括的信息。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 60c2b3ed1f367dc5c501a51ed52440f7d9d3ed6b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741184"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>什么是 Microsoft Authenticator 应用？

Microsoft Authenticator 应用可帮助你在使用双重验证的情况下登录到帐户。 双重验证可帮助你更安全地访问帐户，尤其是在查看敏感信息时。 由于密码可能会遗忘、失窃或者泄露，双重验证作为一种附加的安全保障步骤，可帮助保护帐户的安全，因为其他人更难破解它。

可通过多种方式使用 Microsoft Authenticator 应用，包括：

- 使用用户名和密码登录后，响应身份验证提示。

- 使用用户名、验证器应用和移动设备并结合指纹、人脸或 PIN 来进行登录，而无需输入密码。

- 作为支持验证器应用的任何其他帐户的代码生成器。

> [!Important]
> Microsoft Authenticator 应用可与使用双因素验证并支持基于时间的一次性密码 (TOTP) 标准的任何帐户配合使用。
>
>本文适用于尝试下载并使用 Microsoft Authenticator 应用作为安全验证方法的用户。 如果你是管理员，正在查找有关如何使用 Authenticator 应用为你的员工和其他用户启用无密码登录的信息，请参阅[使用 Microsoft Authenticator 应用启用无密码登录(预览版)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone)。

## <a name="terminology"></a>术语

| 术语|说明|
| ----|-----------|
| 双重验证 | 要求专门使用两个验证信息片段（例如密码和 PIN）的验证过程。 Microsoft Authenticator 应用支持标准双重验证和无密码登录。 |
| 多重身份验证 (MFA) | 所有双重验证都属于多重身份验证，要求根据组织的需求至少使用两个验证信息片段。  |
| Microsoft 帐户（也称 MSA） | 创建自己的个人帐户，用于访问面向使用者的 Microsoft 产品和云服务，例如 Outlook、OneDrive、Xbox LIVE 或 Office 365。 Microsoft 帐户在由 Microsoft 运行的 Microsoft 使用者标识帐户系统中创建和存储。 |
| 工作或学校帐户 | 组织为你创建工作或学校帐户（例如 alain@contoso.com），使你能够访问内部的和可能受限的资源，例如 Microsoft Azure、Windows Intune 和 Office 365。 |
| 验证码 | 在每个添加的帐户下显示在验证器应用中的六位代码。 验证码每 30 秒更改一次，以防有人多次使用一个代码。 这也称为一次性密码 (OTP)。 |

## <a name="how-two-factor-verification-works-with-the-app"></a>如何在应用中使用双重验证

Microsoft Authenticator 应用通过两种方式执行双重验证：

- **通知。** 在要登录到的设备中，键入工作或学校帐户或 Microsoft 个人帐户的用户名和密码，然后，Microsoft Authenticator 应用会发送一条通知，要求你**批准登录**。 如果你认可这次登录，请选择“批准”。  否则请选择“拒绝”  。 如果选择“拒绝”，则还可以将请求标记为欺诈。 

- **验证码。** 在要登录到的设备中，键入工作或学校帐户或 Microsoft 个人帐户的用户名和密码，然后复制 Microsoft Authenticator 应用的“帐户”屏幕中显示的关联验证码。  验证码也称为一次性密码 (OTP) 身份验证。

- **无密码登录。** 在要登录到的设备中，键入工作或学校帐户或 Microsoft 个人帐户的用户名，然后在移动设备上使用指纹、人脸或 PIN 验证自己的身份。 对于这种方法，无需输入密码。

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>是否使用设备的生物识别功能

如果使用 PIN 完成身份验证过程，可将 Microsoft Authenticator 应用设置为改用设备的指纹或人脸识别（生物识别）功能。 可以在首次使用 Authenticator 应用验证帐户时完成此设置：选择使用设备生物识别功能而不是 PIN 作为身份验证方法的选项即可。

## <a name="who-decides-if-you-use-this-feature"></a>谁决定是否要使用此功能？

根据具体的帐户类型，你的组织可能决定必须使用双重验证，有时你也可以自行决定使用此功能。

- **工作或学校帐户。** 如果你使用工作或学校帐户（例如 alain@contoso.com），则由你的组织决定是否必须使用双重验证，以及具体的验证方法是什么。 有关将工作或学校帐户添加到 Microsoft Authenticator 应用的详细信息，请参阅[添加工作或学校帐户](user-help-auth-app-add-work-school-account.md)。

- **Microsoft 个人帐户。** 可以选择为 Microsoft 个人帐户（例如 alain@outlook.com）设置双重验证。 有关添加个人 Microsoft 帐户的详细信息，请参阅[添加个人帐户](user-help-auth-app-add-personal-ms-account.md)。

- **非 Microsoft 帐户。** 可以选择为非 Microsoft 帐户（例如 alain@gmail.com）设置双重验证。 非 Microsoft 帐户可能不使用“两重验证”这一术语，但你应该可以在“安全性”或“登录”设置中找到类似功能。   Microsoft Authenticator 应用适用于支持 TOTP 标准的任何帐户。 有关添加非 Microsoft 帐户的详细信息，请参阅[添加非 Microsoft 帐户](user-help-auth-app-add-non-ms-account.md)。

## <a name="in-this-section"></a>在本节中

| 项目 | 说明 |
| ------ | ------------ |
| [下载并安装应用](user-help-auth-app-download-install.md) | 介绍了从何处以及如何为运行 Android 和 iOS 的设备获取并安装 Microsoft Authenticator 应用。 |
| [添加工作或学校帐户](user-help-auth-app-add-work-school-account.md) | 介绍如何将各种工作或学校帐户和个人帐户添加到 Microsoft Authenticator 应用。 |
| [添加个人帐户](user-help-auth-app-add-personal-ms-account.md) | 介绍如何将 Microsoft 个人帐户添加到 Microsoft Authenticator 应用。 |
| [添加非 Microsoft 帐户](user-help-auth-app-add-non-ms-account.md) | 介绍如何将非 Microsoft 帐户添加到 Microsoft Authenticator 应用。 |
| [手动添加帐户](user-help-auth-app-add-account-manual.md) | 介绍在无法扫描所提供的 QR 码的情况下，如何手动将帐户添加到 Microsoft Authenticator 应用。 |
| [使用应用登录](user-help-auth-app-sign-in.md) | 介绍如何使用 Microsoft Authenticator 应用登录到各种帐户。|
| [备份和恢复帐户凭据](user-help-auth-app-backup-recovery.md) | 提供有关如何使用 Microsoft Authenticator 应用备份和恢复帐户凭据的信息。 |
| [Microsoft Authenticator 应用常见问题解答](user-help-auth-app-faq.md) | 提供有关应用的常见问题的解答。 |
