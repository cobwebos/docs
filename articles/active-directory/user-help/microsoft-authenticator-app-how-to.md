---
title: Microsoft Authenticator 应用入门 - Azure Active Directory | Microsoft Docs
description: 了解如何升级到最新版本的 Microsoft Authenticator。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: 8afd743f6010822709bf3d49f7a3bbcd51b19edf
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346481"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft 验证器应用入门

Microsoft Authenticator 应用可以帮助阻止未经授权的人员访问帐户；此外，它还能为工作或学校帐户（例如 alain@contoso.com）或个人 Microsoft 帐户（例如 alain@outlook.com）提供附加的安全层，以此阻止欺诈性的交易。 可以将它用作第二种验证方法，或者在使用手机登录时，用它来取代密码。 有关如何设置和使用无密码手机登录的详细信息，请参阅[使用手机（而不是密码）登录](microsoft-authenticator-app-phone-signin-faq.md)。

将该应用用于双重验证时，它采用以下两种工作方式之一：

- **通知。** 该应用会向设备发送一条通知。 请确保通知正确，然后选择“验证”。 如果无法识别该通知，请选择“拒绝”。

- **验证码。** 键入用户名和密码后，可以打开应用，并将“帐户”屏幕上提供的验证码复制到登录屏幕。 验证码充当第二种形式的身份验证。

## <a name="opt-in-for-two-step-verification"></a>针对双重验证选择加入

组织决定是否对工作或学校帐户使用双重验证。 管理员会告知需要设置和使用哪些验证方法。 有关详细信息，请参阅 [Azure 多重身份验证对我而言有什么用途](multi-factor-authentication-end-user.md)。

对于个人 Microsoft 帐户，可以自行设置双重验证。 有关更多详细信息和说明，请参阅[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。

还可以对非 Microsoft 帐户使用 Microsoft Authenticator 应用。 这些帐户可能不是将此功能称为双重验证，但你应该能在安全设置或登录设置中找到此功能。 有关如何设置这些非 Microsoft 帐户的详细信息，请参阅 [Microsoft 客户支持视频](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX)。

## <a name="install-the-app"></a>安装应用

Microsoft Authenticator 应用适用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)。 为了获得最佳体验，应确保应用能够根据请求收到通知。 

## <a name="add-accounts-to-the-app"></a>将帐户添加到应用

可将工作或学校帐户或者个人帐户添加到 Microsoft Authenticator 应用。 

### <a name="add-a-personal-microsoft-account"></a>添加个人 Microsoft 帐户

对于个人 Microsoft 帐户（用于登录到 Outlook.com、Xbox、Skype 等的帐户），只需在 Microsoft Authenticator 应用中登录到帐户。

### <a name="add-a-work-or-school-account"></a>添加工作或学校帐户

1. 如果可能，请在另一台电脑或设备上转到[其他安全性验证](http://aka.ms/mfasetup)屏幕。 有关如何转到此屏幕的信息，请参阅[更改安全设置](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)，或者与管理员联系。

    >[!Note]
    >如果管理员已启用安全信息预览版体验，则你可以遵照[设置安全信息以使用 Authenticator 应用](security-info-setup-auth-app.md)部分中的说明。

2. 选中“Authenticator 应用”旁边的复选框，然后选择“配置”。

    ![安全验证设置屏幕上的“配置”按钮](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    此时会出现“配置移动应用”屏幕，其中显示了要在 Authenticator 应用中扫描的 QR 码。

    ![提供 QR 码的屏幕](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. 打开 Microsoft Authenticator 应用。 在“帐户”屏幕上，依次选择“添加帐户”、“工作或学校帐户”。

4. 使用设备的相机扫描 QR 码，然后选择“完成”关闭 QR 码屏幕。

    >[!Note]
    >如果相机不能正常工作，可以[手动输入 QR 码和 URL](#add-an-account-to-the-app-manually)。

    应用的“帐户”屏幕将显示你的帐户名，以及六位数的验证码。 为了提高安全性，验证码每隔 30 秒更改一次，以防止使用同一代码两次。  

    ![帐户屏幕](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>手动将帐户添加到应用

1. 转到“其他安全性验证”屏幕。 有关如何访问此屏幕的信息，请参阅[更改安全设置](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)。

2. 选中“Authenticator 应用”旁边的复选框，然后选择“配置”。

    此时会显示“配置移动应用”屏幕。

3. 复制“配置移动应用”屏幕中的代码和 URL 信息，以便可以手动将其键入到 QR 扫描程序中。

4. 打开 Microsoft Authenticator 应用。 在“帐户”屏幕上，依次选择“添加帐户”、“工作或学校帐户”。

5. 在 QR 扫描程序屏幕中，选择“手动输入代码”。

    ![用于扫描 QR 码的屏幕](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. 将包含 QR 码的屏幕中的代码和 URL 键入到“添加帐户”屏幕，然后选择“完成”。

    ![用于输入代码和 URL 的屏幕](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    应用的“帐户”屏幕将显示你的帐户名，以及六位数的验证码。 为了提高安全性，验证码每隔 30 秒更改一次，以防止使用同一代码两次。

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>使用设备的指纹或面部识别功能

组织可能要求使用 PIN 来完成身份验证。 可将 Microsoft Authenticator 应用设置为使用设备的指纹或面部识别功能，而不是使用 PIN。 可以在首次使用 Authenticator 应用验证帐户时完成此设置：选择使用设备生物识别功能而不是 PIN 作为身份验证方法的选项即可。

## <a name="use-the-app-when-you-sign-in"></a>在登录时使用应用

将帐户添加到应用后，可以使用该应用登录到帐户。

如果选择在应用中使用验证码，则会在“帐户”页上看到验证码。 这些代码每隔 30 秒更改一次，以便在需要验证码时始终有新的验证码可用。 但是，在登录且系统提示输入验证码之前，不需要对它们执行任何操作。

## <a name="next-steps"></a>后续步骤

- 如果对该应用存在其他一般性的问题，请参阅 [Microsoft Authenticator 常见问题解答](microsoft-authenticator-app-faq.md)

- 如果在手机登录的用法方面有任何疑问，请参阅[使用手机（而不是密码）登录](microsoft-authenticator-app-phone-signin-faq.md)

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如需有关安全信息的详细信息，请参阅[管理安全信息](security-info-manage-settings.md)
