---
title: 手动将帐户添加到应用 - Azure Active Directory | Microsoft Docs
description: 如何手动将帐户添加到 Microsoft Authenticator 应用以进行双因素验证。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00f8a0e8d655f9412eceb7b0901ced829e490f78
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616091"
---
# <a name="manually-add-an-account-to-the-app"></a>手动将帐户添加到应用

如果摄像头无法捕获 QR 码，可以手动将帐户信息添加到 Microsoft Authenticator 应用中，以进行双重验证。 这适用于工作或学校帐户和非 Microsoft 帐户。

为帐户提供的密码不区分大小写，在将密码添加到 Microsoft Authenticator 应用时，无需添加空格。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

## <a name="add-your-work-or-school-account"></a>添加工作或学校帐户

1. 在计算机上，记下“配置移动应用”页上的“密码”和“URL”信息。 不要关闭此页，这样就能查看密码和 URL 了。

    ![提供 QR 码的屏幕](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. 打开 Microsoft Authenticator 应用，从右上方的“自定义和控制”图标中选择“添加帐户”，然后选择“工作或学校帐户”。

3. 选择“或手动输入密码”。

    ![用于扫描 QR 码的屏幕](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. 输入第 1 步中记下的“密码”和“URL”，再选择“完成”。

    ![用于输入代码和 URL 的屏幕](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    应用的“帐户”屏幕将显示你的帐户名，以及六位数的验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个验证码。

## <a name="add-your-google-account"></a>添加 Google 帐户

1. 在计算机上，选择显示 QR 码的“设置验证器”页中的“无法扫描它”。

    此时，包含密码的“无法扫描条形码”页显示。 不要关闭此页，这样就能查看密码了。

2. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再依次选择“其他帐户(Google、Facebook 等)”和“或手动输入密码”。

3. 输入“帐户名”（例如，“Google”），并键入第 1 步中记下的“密钥”，再选择“完成”。

4. 在计算机的“设置验证器”页上，键入应用中为 Google 帐户提供的六位数验证码，然后选择“验证”。

    应用的“帐户”屏幕将显示你的帐户名，以及六位数的验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个验证码。

    >[!NOTE]
    >若要详细了解双因素验证和 Google 帐户，请参阅[启用双因素验证](https://support.google.com/accounts/answer/185839)和[详细了解双因素验证](https://www.google.com/landing/2step/help.html)。

## <a name="add-your-facebook-account"></a>添加 Facebook 帐户

1. “通过第三方验证器进行设置”页中显示 QR 码，这是要输入应用的密码。 不要关闭此页，这样就能查看密码了。

2. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再依次选择“其他帐户(Google、Facebook 等)”和“或手动输入密码”。

3. 输入“帐户名”（例如，“Facebook”），并键入第 1 步中记下的“密钥”，再选择“完成”。

4. 在计算机的“双因素验证器”页上，键入应用中为 Facebook 帐户提供的六位数验证码，然后选择“验证”。

    应用的“帐户”屏幕将显示你的帐户名，以及六位数的验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个验证码。

    >[!NOTE]
    >若要详细了解双因素验证和 Facebook 帐户，请参阅[什么是双因素身份验证及其工作原理？](https://www.facebook.com/help/148233965247823)。

## <a name="add-your-amazon-account"></a>添加 Amazon 帐户

若要添加 Amazon 帐户，可以启用双因素验证，并将帐户添加到应用。

1. 在计算机上，选择显示 QR 码的“选择如何接收验证码”页中的“无法扫描条形码”。

    此时，包含密码的“无法扫描条形码”消息显示。 不要关闭此页，这样就能查看密码了。

2. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再依次选择“其他帐户(Google、Facebook 等)”和“或手动输入密码”。

3. 输入“帐户名”（例如，“Amazon”），并键入第 1 步中记下的“密钥”，再选择“完成”。

4. 完成注册过程的剩余步骤（包括添加短信等备份验证方法），再选择“发送验证码”。

5. 在计算机的“添加备份验证方法”页上，键入备份验证方法为 Amazon 帐户提供的六位数验证码，然后选择“验证验证码并继续”。

6. 在“即将完成”页上，决定是否让计算机成为受信任的设备，然后选择“知道了。 启用双因素验证”。

    此时，“高级安全设置”页显示，其中包含更新后的双因素验证详细信息。

    >[!NOTE]
    >若要详细了解双因素验证和 Amazon 帐户，请参阅[关于双因素验证](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)和[通过双因素验证进行登录](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)。    

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Microsoft Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 如果在获取个人 Microsoft 帐户的验证代码时遇到问题, 请参阅[Microsoft 帐户安全信息 & 验证](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)代码一文中的 "**验证代码问题疑难解答**" 一节。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
