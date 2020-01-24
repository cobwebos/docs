---
title: 更改双因素验证方法和设置-Azure AD
description: 了解如何在 "其他安全性验证" 页中更改工作或学校帐户的安全验证方法和设置。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: lizross
ms.openlocfilehash: e48834800f1fe3d34c13a87cfb3d8ef00c9e6bff
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705269"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>更改双因素验证方法和设置

设置工作或学校帐户的安全验证方法后，可以更新任何相关的详细信息，包括：

- 选择默认的安全验证方法。

- 添加或更新您的安全验证方法详细信息，如您的电话号码。

- 设置新的验证器应用或从验证器应用删除设备。

## <a name="using-the-additional-security-verification-page"></a>使用 "其他安全性验证" 页

如果你的组织提供了有关如何启用和管理你的双因素验证的特定步骤，你应遵循这些说明。 否则，你可以从 "[其他安全验证](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)" 页访问你的安全验证方法设置。

>[!Note]
>如果你在屏幕上看到的内容与本文中所述的内容不匹配，则表示你的管理员已启用安全信息（预览版）体验或你的组织有其自己的自定义门户。 有关安全信息体验的详细信息，请参阅[安全信息（预览版）概述](user-help-security-info-overview.md)。 若要详细了解组织的自定义门户，必须与支持人员联系。

### <a name="to-get-to-the-additional-security-verification-page"></a>转到 "其他安全验证" 页

- 请参阅 "[其他安全性验证](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)" 页。

    !["其他安全验证" 页，其中包含可用的安全验证方法详细信息](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

    如果单击该链接对你无效，还可以按照以下步骤访问“其他安全性验证”页：

    1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

    2. 在右上方选择帐户名称，并选择“配置文件”。

    3. 选择“其他安全性验证”。  

        !["我的应用" 链接到 "其他安全验证" 页](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>有关使用 "**其他安全验证**" 页的 "**应用密码**" 部分的信息，请参阅[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)。 应用密码应仅用于尚不支持双重验证的应用。

## <a name="change-your-default-security-verification-method"></a>更改默认的安全验证方法

使用你的用户名和密码登录到你的工作或学校帐户后，你将自动看到你选择的安全验证方法。 根据组织的需求，这可能是通过验证器应用、短信或电话呼叫发出的通知或验证码。

如果你决定要更改所使用的默认安全验证方法，则可以从此处执行此操作。

### <a name="to-change-your-default-security-verification-method"></a>更改默认的安全验证方法

1. 从 "**其他安全性验证**" 页中，从 "**你喜欢的选项**" 下拉列表中选择要使用的方法。 你将看到所有选项，但你将只能选择可供你的组织使用的选项。

    - **通过应用通知我。** 将通过验证器应用收到等待验证提示的通知。

    - **拨打我的身份验证电话。** 你将在移动设备上收到电话呼叫，要求你验证你的信息。

    - **我的身份验证电话的文本代码。** 你将在移动设备上获取验证码作为文本消息的一部分。 对于工作或学校帐户，必须在验证提示中输入此代码。

    - **拨打我的办公电话。** 你将在你的办公电话上收到电话呼叫，要求你验证你的信息。

    - **使用应用中的验证码。** 你将使用验证器应用获取验证码，你将在你的工作或学校帐户中键入提示。

2. 选择“保存”。

## <a name="add-or-change-your-phone-number"></a>添加或更改你的电话号码

你可以从 "**其他安全验证**" 页中添加新的电话号码或更新现有号码。

>[!Important]
>我们强烈建议你添加辅助电话号码，以帮助防止在你的主要电话丢失或被盗的情况下锁定帐户，或者如果你收到新手机，不再具有原始的主要电话号码。

### <a name="to-change-your-phone-numbers"></a>更改电话号码

1. 在 "**其他安全性验证**" 页的 "**你希望如何响应？** " 部分中，更新**身份验证电话**（主要移动设备）和**办公电话**的电话号码信息。

2. 选中 "**备用身份验证电话**" 选项旁边的框，然后键入一个辅助电话号码，如果无法访问主要设备，则可以在其中接收短信或电话呼叫。

3. 选择“保存”。

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>将新帐户添加到 Microsoft 验证器应用

可以在适用于[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)或[iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator 应用上设置工作或学校帐户。

如果你以前在 Microsoft Authenticator 的应用中设置了工作或学校帐户，则不需要再次执行此操作。

1. 在 "**其他安全性验证**" 页的 "**你希望如何响应？** " 部分中，选择 "**设置验证器应用程序**" 按钮。

    ![在 Microsoft Authenticator 应用程序中设置工作或学校帐户](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

2. 按照屏幕上的说明操作，包括使用移动设备扫描 QR 码，然后选择 "**下一步**"。

    系统会要求你通过 Microsoft Authenticator 应用批准通知，以验证你的信息。

3. 选择“保存”。

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>从 Microsoft Authenticator 应用删除你的帐户或设备

你可以从 "Microsoft Authenticator" 应用删除你的帐户，并可以从你的工作或学校帐户中删除你的设备。 通常，你会删除你的设备以从你的帐户中永久删除丢失、被盗或旧的设备，并删除你的帐户以尝试解决某些连接问题或解决帐户更改问题，例如新用户名。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>从工作或学校帐户中删除设备

1. 在 "**其他安全性验证**" 页的 "**你希望如何响应？** " 部分中，选择 "**设置验证器应用程序**" 按钮。

2. 选择“保存”。

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>从 Microsoft Authenticator 应用中删除帐户

- 在 Microsoft Authenticator 应用中，选择要删除的设备旁边的 "**删除**" 按钮。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>在受信任的设备上打开双重验证提示

根据你的组织设置，你可能会看到一个复选框，指出你在浏览器上执行双重验证时**不会再询问 X 天**。 如果你已选中此框以停止双重验证提示，然后你丢失了设备，否则你的设备可能会受到损害，你应重新打开双重验证提示，以帮助保护你的帐户。 遗憾的是，你无法为单个设备打开提示。 你必须同时为所有设备打开提示。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>为设备重新启用双因素验证提示

- 在 "**其他安全性验证**" 页上，选择 **"在以前信任的设备上还原多重身份验证"** 。

    下一次登录任何设备时，系统将提示你执行双重验证。

## <a name="next-steps"></a>后续步骤

添加或更新你的双因素验证设置后，你可以管理你的应用密码、登录或获取一些与常见的双因素验证相关问题的帮助。

- 为不支持双重验证的任何应用[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)。

- [使用双重验证登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)
