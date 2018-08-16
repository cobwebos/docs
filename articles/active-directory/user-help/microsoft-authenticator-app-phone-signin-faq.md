---
title: Microsoft Authenticator 手机登录 - Azure Active Directory | Microsoft Docs
description: 提供有关如何在不键入密码的情况下，使用手机登录到工作和学校帐户以及个人 Microsoft 帐户的详细信息与分步说明。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: a6df90a1ae1dddbf7f76a7ef6c7e13d9e645dd9c
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528419"
---
# <a name="sign-in-with-your-phone-not-your-password"></a>使用手机（而不是密码）登录

在你输入密码后，Microsoft Authenticator 应用不仅可以通过执行双重验证来帮助保护帐户的安全，而且现在它还能完全取代 Microsoft 帐户密码。

此功能可在 iOS 设备和运行 Android 6.0 或更高版本的 Android 设备上使用。

## <a name="how-it-works"></a>工作原理

在登录 Microsoft 帐户时，许多人会将 Microsoft Authenticator 应用用于双重验证。 键入密码，并转到该应用以批准通知或获取验证码。 使用手机登录时，会跳过密码，并在手机上完成所有身份验证。 由于手机登录是一种双重验证，因此仍需提供你知道和拥有的事物来验证自己的身份。 手机是你拥有的事物，手机的 PIN 或生物识别密钥则是你知道的事物。

## <a name="turn-on-phone-sign-in-for-your-work-or-school-account"></a>为工作或学校帐户启用手机登录

如果组织使用 Azure Active Directory 进行标识管理和身份验证，在 Microsoft Authenticator 应用中启用手机登录后，便可以使用此方法。 

### <a name="to-turn-on-phone-sign-in-for-your-account"></a>为帐户启用手机登录

- **如果手机上出现了此图标 ![手机登录图标](./media/microsoft-authenticator-app-phone-signin-faq/icon.png)。** 这表示已设置手机登录。 系统可能要求你升级帐户以添加推送通知，以便在从应用外部发送身份验证请求时，可以收到通知。

- **如果已使用 Microsoft Authenticator 应用进行双重验证。** 请转到应用的“帐户”屏幕，选择工作或学校帐户旁边的下拉箭头，然后选择“启用手机登录”。

- **如果为个人帐户使用了 Microsoft Authenticator 应用，但没有为工作或学校帐户使用该应用。** 请遵照组织的双重验证说明，然后启用手机登录。 有关详细信息，请参阅 [Microsoft Authenticator 应用入门](microsoft-authenticator-app-how-to.md)

### <a name="to-use-phone-sign-in-to-sign-in-to-your-work-or-school-account"></a>使用手机登录方法登录到工作或学校帐户

1.  像平时一样登录到工作或学校帐户。

    键入用户名后，会看到一个页面，其中包含一个 2 位数的数字，并要求通过 Microsoft Authenticator 应用批准登录。 如果不想要使用此登录方法，可以选择“改用密码”，并使用密码登录。

    ![登录页](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in.png)
 
2. Microsoft 身份验证应用程序中会显示一条通知，要求你**批准登录**。 如果你认可这次登录，请选择“批准”。 否则请选择“拒绝”。

3. 点击“批准登录”屏幕上显示的同一数字。 使用手机的 PIN 或生物识别密钥完成身份验证。
 
    ![“批准登录”页](./media/microsoft-authenticator-app-phone-signin-faq/microsoft-auth-app-sign-in-numbers.png)

## <a name="turn-on-phone-sign-in-for-your-personal-account"></a>为个人帐户启用手机登录

可为个人 Microsoft 帐户（例如，用来登录 Outlook.com、Xbox、或 Skype 的帐户）启用手机登录。

### <a name="to-turn-on-phone-sign-in-for-your-personal-account"></a>为个人帐户启用手机登录

- **如果未使用 Microsoft Authenticator 应用。** 下载并安装 Microsoft Authenticator 应用，然后从 Microsoft Authenticator 应用的“添加帐户”屏幕登录到帐户，以添加个人 Microsoft 帐户。 有关详细信息，请参阅 [Microsoft Authenticator 应用入门](microsoft-authenticator-app-how-to.md)。
新帐户会自动启用，因此你可以立即开始使用手机登录。

- **如果已使用 Microsoft Authenticator 应用进行双重验证。** 请转到应用的“帐户”屏幕，选择工作或学校帐户旁边的下拉箭头，然后选择“启用手机登录”。

>[!NOTE]
>为帮助保护帐户，该应用要求在设备上设置 PIN 或生物识别锁。 如果将手机保持解锁状态，该应用会要求在启用手机登录之前设置安全锁。

### <a name="to-use-phone-sign-in-to-sign-in-to-your-personal-account"></a>使用手机登录方法登录到个人帐户

1.  转到个人 Microsoft 帐户登录页，然后选择“改用 Microsoft Authenticator 应用”链接，而不要键入密码。

    Microsoft 向手机发送通知。 

2.  批准通知。

## <a name="phone-sign-in-faq"></a>手机登录常见问题解答

|**问题**|**解决方案**|
|--------------|-------------|
|**为何使用手机登录比键入密码更安全？**|当今，大多数人在登录网站或应用时，使用用户名和密码。 遗憾的是，密码可能会丢失、被盗或被黑客猜出。<br><br>安装 Microsoft Authenticator 应用后，该应用会在手机上创建一个密钥，用于解锁受手机 PIN 或生物识别锁保护的帐户。 登录时，可以使用此密钥来证明自己的身份。<br><br>**重要说明**<br>你的数据仅用于在本地保护你的密钥。 这些数据永远不会发送到或者存储在云中。|
|**这是否会取代双重验证？是否应将其关闭？**| 我们正努力扩展手机登录的适用范围，但目前在 Microsoft 生态系统中仍有不支持它的情景。 在这些情景中，仍需使用双重验证进行安全登录。 因此，不应关闭帐户的双重验证。|
|**如果为帐户保持启用双重验证，是否必须批准两个通知？**| 不是。 使用手机登录到 Microsoft 帐户也算作双重验证，因此不需要再次批准。|
|**如果手机遗失或未随身携带，如何访问我的帐户？**| 始终可以在登录页中选择“改用密码”链接，重新改用密码登录。 但是，如果使用双重验证，则仍需要使用第二种方法来验证身份。<br><br>**重要说明**<br>我们建议设置多种与帐户关联的最新验证方法。<br><br>可以通过[安全设置](https://account.live.com/proofs/manage)页管理个人帐户的验证方法。 对于工作或学校帐户，如果管理员已启用安全信息，则你可以转到组织的[其他安全验证](https://aka.ms/MFASetup)页或“保护帐户安全”页。 有关安全信息的详细信息，请参阅[管理安全信息](security-info-manage-settings.md)。<br><br>如果无法管理验证方法，则必须与管理员联系。|
|**如何停用此功能，并重新改用密码登录？**|对于个人帐户，请在登录期间选择“改用密码”链接。 系统会记住你最近的选择，并在下次登录时默认提供此选项。 如果想要改用手机登录，请在登录期间选择“改用应用”链接。<br><br>对于工作或学校帐户，必须删除帐户：打开 Microsoft Authenticator 应用，选择“编辑帐户”菜单，然后删除帐户。|
|**为何不能使用多个工作或学校帐户进行手机登录？**| 一部手机只能注册到单个工作或学校帐户。 如果想要为不同的工作或学校帐户启用手机登录，必须先通过“设置”页取消注册旧帐户。|
|**是否可以使用手机登录到电脑？**| 我们建议使用 Windows 10 上的 Windows Hello 登录到电脑。 Windows Hello 允许使用人脸、指纹或 PIN 登录。|
|**是否可以在 Windows Phone 上使用手机登录？**| 不是。 Windows Phone 上的 Microsoft Authenticator 应用不支持此功能。|

## <a name="next-steps"></a>后续步骤

-   如果对该应用存在其他一般性的问题，请参阅 [Microsoft Authenticator 常见问题解答](microsoft-authenticator-app-faq.md)

-   如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

-   如需有关安全信息的详细信息，请参阅[管理安全信息](security-info-manage-settings.md)
