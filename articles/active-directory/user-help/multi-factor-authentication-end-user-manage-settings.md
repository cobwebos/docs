---
title: 更改双重验证方法和设置 - Azure Active Directory
description: 了解如何在其他安全性验证页中更改工作或学校帐户的安全验证方法和设置。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798374"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>更改双重验证方法和设置

设置工作或学校帐户的安全验证方法后，可以更新任何相关的详细信息，包括：

- 默认安全验证方法

- 安全验证方法详细信息，如电话号码

- 验证器应用设置或从验证器应用删除设备

## <a name="using-the-additional-security-verification-page"></a>使用其他安全性验证页

如果你的组织提供了有关如何启用和管理双重验证的特定步骤，你应该首先遵循这些说明。 否则，你可以在[其他安全性验证](./multi-factor-authentication-end-user-first-time.md)页访问你的安全验证方法设置。

>[!Note]
>如果屏幕上显示的内容与本文所述不符，则意味着管理员已启用“我的个人资料”（预览版）体验或者你的组织提供了你的自定义门户。 如需有关新安全信息体验的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)。 若要详细了解组织的自定义门户，必须与组织的技术支持部门联系。

### <a name="to-get-to-the-additional-security-verification-page"></a>转到“其他安全验证”页

点击此链接转到[其他安全性验证](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)页。

![“其他安全验证”页，其中包含可用的安全验证方法详细信息](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

还可按以下步骤访问“其他安全验证”页：

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上方选择帐户名称，并选择“配置文件”。

1. 选择“其他安全性验证”。  

    ![“我的应用”链接到“其他安全验证”页](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>有关使用“其他安全验证”页的“应用密码”部分的信息 ，请参阅[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)。 应用密码应仅用于不支持双重验证的应用。

## <a name="change-your-default-security-verification-method"></a>更改默认的安全验证方法

使用用户名和密码登录到工作或学校帐户后，你将自动看到所选的安全验证方法。 取决于组织的需求，这可能是通过验证器应用、短信或电话呼叫发出的通知或验证码。

如果你决定要更改所使用的默认安全验证方法，请参考以下步骤。

### <a name="to-change-your-default-security-verification-method"></a>更改默认的安全验证方法

1. 在“其他安全验证”页上，从“你的首选选项”列表中选择要使用的方法。 你将看到所有选项，但只能选择组织允许你使用的选项。

    - 通过应用通知我：你会通过验证器应用收到通知，提示你有一个待解决的验证提示。

    - 呼叫我的身份验证电话：你会通过移动设备收到电话呼叫，要求你验证信息。

    - 向身份验证电话发送验证码：会以短信的形式向你的移动设备发送验证码。 你必须在工作或学校帐户的验证提示中输入此验证码。

    - 拨打我的办公电话：你会通过办公电话收到电话呼叫，要求你验证信息。

    - 使用来自应用的验证码：你将使用验证器应用获取验证码，然后在工作或学校帐户的验证提示中输入它。

2. 选择“保存”。

## <a name="add-or-change-your-phone-number"></a>添加或更改电话号码

你可以在“其他安全验证”页上添加新电话号码或更新现有号码。

>[!Important]
>强烈建议添加备用电话号码，以帮助防止在下列情况下无法登录帐户：主电话丢失或被盗，或者更换新电话后不再使用原来的主电话号码。

### <a name="to-change-your-phone-numbers"></a>更改电话号码

1. 在“其他安全验证”页的“你希望如何响应?”部分 ，更新“身份验证电话”（你的主要移动设备）和“办公电话”的电话号码信息 。

1. 选中 " **备用身份验证电话** " 选项旁边的框，然后键入一个辅助电话号码，如果无法访问主要设备，则可以在该号码接收电话呼叫。

1. 选择“保存”。

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>向 Microsoft 验证器应用添加新帐户

你可以在适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458) 的 Microsoft Authenticator 应用中设置工作或学校帐户。

如果你已经在 Microsoft Authenticator 应用中设置了工作或学校帐户，则无需再次执行此操作。

1. 在“其他安全验证”页的“你希望如何响应?”部分 ，选择“设置 Authenticator 应用”。

    ![在 Microsoft Authenticator 应用中设置工作或学校帐户](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. 按照屏幕上的说明操作，包括使用移动设备扫描 QR 码，然后选择“下一步”。

    你需要通过 Microsoft Authenticator 应用同意通知，以验证你的信息。

1. 选择“保存”。

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>从 Microsoft Authenticator 应用删除帐户或设备

你可以从 Microsoft Authenticator 应用删除帐户，以及从工作或学校帐户删除设备。 通常，删除设备是为了从帐户永久删除丢失、被盗或旧的设备，删除帐户是为了尝试解决某些连接问题或解决帐户更改问题，例如新用户名。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>从工作或学校帐户删除设备

1. 在“其他安全验证”页的“你希望如何响应?”部分，选择“设置 Authenticator 应用”按钮。

1. 选择“保存”。

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>从 Microsoft Authenticator 应用删除帐户

在 Microsoft Authenticator 应用中，选择要删除的设备旁边的“删除”按钮。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>在受信任的设备上打开双重验证提示

在浏览器中执行双重验证时，根据组织设置，可能会有一个复选框显示“X 天不再询问”。 如果已选择此选项以停止双重验证提示，然后丢失设备，或者设备可能收到威胁，则应重新打开双重验证提示，以帮助保护你的帐户。 你必须同时为所有设备启用提示。 不幸的是，你不能只为特定设备启用提示。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>为设备重新启用双重验证提示

在[“其他安全验证”页](#to-get-to-the-additional-security-verification-page)中，选择“在以前受信任的设备上还原多重身份验证”。 下次登录任何设备时，会提示执行双重验证。

## <a name="next-steps"></a>后续步骤

添加或更新双重验证设置后，你可以管理应用密码、登录，或获得一些常见双重验证相关问题的帮助。

- 为不支持双重验证的任何应用[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)。

- [如何使用双重验证登录](multi-factor-authentication-end-user-signin.md)

- [解决双重验证的常见问题](multi-factor-authentication-end-user-troubleshoot.md)