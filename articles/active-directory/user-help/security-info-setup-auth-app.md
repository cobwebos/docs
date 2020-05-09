---
title: 将 Microsoft Authenticator 应用设置为验证方法-Azure AD
description: 如何设置安全信息（预览版）页，以使用 Microsoft Authenticator 应用作为验证方法来验证身份。
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
ms.openlocfilehash: 264b2803774bcec000fc767693cf96d15e684790
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628395"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>将 Microsoft Authenticator 应用设置为验证方法

可遵循以下步骤添加双重验证和密码重置方法。 在首次设置此项后，可以返回到“安全信息”页以添加、更新或删除安全信息****。

如果在登录工作或学校帐户后系统提示立即进行此项设置，请参阅[根据登录页提示设置安全信息](security-info-setup-signin.md)一文中的详细步骤。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> 如果看不到 "验证器应用" 选项，则你的组织可能不允许你使用此选项进行验证。 在这种情况下，需要选择另一种方法或与组织的技术支持部门联系以获得更多帮助。

## <a name="security-vs-password-reset-verification"></a>安全与密码重置验证

安全信息方法既用于双因素安全验证，又用于密码重置。 但是，并非所有方法都可以用于这二者。

| 方法 | 用于 |
| ------ | -------- |
| Authenticator 应用 | 双重验证和密码重置身份验证。 |
| 短信 | 双重验证和密码重置身份验证。 |
| 电话呼叫 | 双重验证和密码重置身份验证。 |
| 安全密钥 | 双重验证和密码重置身份验证。 |
| 电子邮件帐户 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |
| 安全提问 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>在“安全信息”页中设置 Microsoft Authenticator 应用

根据组织的设置，有时可以使用验证器应用作为安全信息方法之一。 不一定要使用 Microsoft Authenticator 应用，可在设置过程中选择其他应用。 但是，本文使用 Microsoft Authenticator 应用。

### <a name="to-set-up-the-microsoft-authenticator-app"></a>设置 Microsoft Authenticator 应用

1. 登录到工作或学校帐户，然后转到 https://myaccount.microsoft.com/ 页。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”****************。

    ![“安全信息”页，其中突出显示了“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页上，从下拉列表中选择“验证器应用”，然后选择“添加”************。

    ![“添加方法”框，其中已选择“验证器应用”](media/security-info/securityinfo-myprofile-addauthapp.png)

4. 在“首先获取应用”页上，选择“立即下载”以在移动设备上下载并安装 Microsoft Authenticator 应用，然后选择“下一步”。************

    有关如何下载和安装该应用的详细信息，请参阅[下载并安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)。

    ![“首先获取应用”页](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > 若要使用除 Microsoft Authenticator 应用以外的验证器应用，请选择“我想要使用其他验证器应用”链接。 
   >
   > 如果你的组织允许选择除验证器应用以外的其他方法，则可以选择“我想要设置其他方法”链接。 

5. 在移动设备上安装 Microsoft Authenticator 应用时，请将“设置帐户”页保持打开。 

    ![“设置验证器应用”页](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. 打开 Microsoft Authenticator 应用，选择允许通知（如果出现相关提示），通过右上角的“自定义和控制”图标中选择“添加帐户”，然后选择“工作或学校帐户”。   

    >[!Note]
    >如果这是你首次设置 Microsoft Authenticator 应用，则可能会收到一个提示，询问你是允许该应用访问你的相机 (iOS)，还是允许该应用拍摄照片和录制视频 (Android)。 你必须选择“允许”  ，以便验证器应用可以访问你的相机，在下一步中拍摄 QR 码的照片。 如果你不允许使用相机，仍然可以设置验证器应用，但需要手动添加代码信息。 有关如何手动添加代码的信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

7. 返回到计算机上的“设置帐户”页，然后选择“下一步”。  

    此时会出现“扫描 QR 码”页。 

    ![使用 Authenticator 应用扫描 QR 码](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. 使用在步骤 6 中创建工作或学校帐户后显示在移动设备上的 Microsoft Authenticator 应用 QR 码读取器扫描所提供的代码。

    Authenticator 应用应该会成功添加你的工作或学校帐户，而无需提供其他任何信息。 但是，如果 QR 码读取器无法读取该代码，则你可以选择“无法扫描 QR 码链接”，并将代码和 URL 手动输入到 Microsoft Authenticator 应用中。**** 有关手动添加代码的详细信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

9. 在计算机上的“扫描 QR 码”页上选择“下一步”。  

    随即有一条通知发送到移动设备上的 Microsoft Authenticator 应用，以测试你的帐户。

    ![使用 Authenticator 应用测试帐户](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. 在 Microsoft Authenticator 应用中确认通知，然后选择“下一步”。 

     ![成功通知，指出正在连接应用和帐户](media/security-info/securityinfo-myprofile-successauthapp.png)

     在使用双重验证或密码重置时，安全信息默认会更新为使用 Microsoft Authenticator 应用来验证你的身份。

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>从安全信息方法中删除验证器应用

如果你不再想要使用验证器应用作为安全信息方法，可以从“安全信息”页中将其删除****。 这适用于所有身份验证器应用，而不仅仅是 Microsoft Authenticator 应用。 删除该应用后，必须在移动设备上转到验证器应用，并删除帐户。

>[!Important]
>如果错误地删除了验证器应用，无法撤消该操作。 必须遵循本文的[设置验证器应用](#set-up-the-microsoft-authenticator-app-from-the-security-info-page)部分中的步骤再次添加验证器应用。

### <a name="to-delete-the-authenticator-app"></a>删除验证器应用

1. 在“安全信息”页上，选择“验证器应用”旁边的“删除”链接********。

    ![“安全信息”中用于删除验证器应用的链接](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. 在确认框中选择“是”，以删除该验证器应用。**** 删除验证器应用后，它将从你的安全信息中删除，并在“安全信息”页中消失****。 如果验证器应用是默认方法，则默认方法将更改为另一种可用方法。

3. 在移动设备上打开验证器应用，选择“编辑帐户”，然后从验证器应用中删除你的工作或学校帐户。****

    你的帐户将完全从用于双重验证和密码重置请求的验证器应用中删除。

## <a name="change-your-default-security-info-method"></a>更改默认的安全信息方法

如果在使用双重验证登录到工作或学校帐户时想要将验证器应用用作默认的方法，或者希望验证器应用成为默认用于密码重置请求的方法，可以从“安全信息”页设置该方法****。

### <a name="to-change-your-default-security-info-method"></a>更改默认安全信息方法

1. 在“安全信息”页上，选择“默认登录方法”信息旁边的“更改”链接************。

    ![更改默认登录方法的链接](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. 从可用方法下拉列表中选择“Microsoft Authenticator - 通知”。**** 如果不使用 Microsoft Authenticator 应用，请选择“验证器应用或硬件令牌”选项。****

    ![选择默认登录的方法](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. 选择“确认”。****

    用于登录的默认方法将更改为 Microsoft Authenticator 应用。

## <a name="additional-security-info-methods"></a>其他安全信息方法

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **安全密钥。** 注册与 Microsoft 兼容的安全密钥，并将其与用于双重验证或密码重置的 PIN 一起使用。 有关如何使用安全密钥验证身份的分步说明，请参阅[设置安全信息以使用安全密钥](security-info-setup-security-key.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

    >[!Note]
    >如果缺少其中某些选项，则很可能是因为组织不允许使用这些方法。 如果是这样，则将需要选择可用的方法，或与管理员联系以获取进一步帮助。

## <a name="next-steps"></a>后续步骤

- 按照使用[双重验证或安全信息的登录](security-info-setup-signin.md)一文中的步骤，使用 Microsoft Authenticator 应用登录。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
