---
title: "Microsoft Authenticator 应用常见问题解答"
description: "提供与 Microsoft Authenticator 应用和 Azure Multi-Factor Authentication 相关的常见问题与解答列表。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: dbe949dac15b45ba038ed675873f3da1fb1ff86a


---
# <a name="microsoft-authenticator-application-faq"></a>Microsoft Authenticator 应用程序常见问题解答
Microsoft Authenticator 应用替代了 Azure Authenticator 应用，建议使用 Azure Multi-Factor Authentication 时使用该应用。 此应用可用于 Windows Phone、Android 和 iOS。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>Azure Authenticator、多重身份验证和 Microsoft 帐户应用怎么了？
Microsoft Authenticator 应用替代了这些应用。 Azure Authenticator 升级到 Microsoft Authenticator。 如果使用的是多重身份验证或 Microsoft 帐户应用，现在需要安装 Microsoft Authenticator 并重新添加帐户。 请务必在将帐户添加到新应用后，再删除旧帐户。

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>我已使用 Microsoft Authenticator 应用程序生成验证代码。 如何切换到一键式推送通知？
通过推送通知批准登录仅适用于 Microsoft 帐户，并不适用于 Google 或 Facebook 等第三方帐户。 但对于工作或学校 Microsoft 帐户，组织可以选择禁用此选项。

如果你的 Microsoft 帐户使用的是个人帐户，并且想要切换到推送通知，则需要再次添加帐户。 向你的帐户重新注册该设备，并设置推送通知。  

如果你对工作或学校帐户使用 Microsoft Authenticator，则你的组织决定是否允许一键式通知。

### <a name="when-will-i-be-able-to-use-one-click-push-notifications-on-iphone-or-ipad"></a>我什么时候能在 iPhone 或 iPad 上使用一键式推送通知？
此功能到 8 月底之前都处于测试阶段，之后将会广泛可用于 Microsoft 帐户。 如果要加入 Beta 计划，请将电子邮件发送到 msauthenticator@microsoft.com.，邮件需包含姓名和 Apple ID。  

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>一键式推送通知是否适用于非 Microsoft 帐户？
不适用，推送通知只适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果您的工作单位或学校使用的是 Azure AD 帐户，则可能会禁用此功能。  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>我从备份还原设备后，帐户代码丢失或无效。 发生了什么情况？
出于安全考虑，我们不会从应用备份还原帐户。 如果从备份还原 iOS 应用，帐户仍然显示，但无法接收登录验证或生成安全代码。 还原应用后，请删除帐户，然后重新添加。

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>我将使用新设备。 如何从旧设备删除 Microsoft Authenticator 应用并将其迁移到新设备？
将 Microsoft Authenticator 应用添加到新设备不会自动将其从任何其他设备删除。 若要管理为帐户配置的设备，请访问用于管理双重验证的同一网站，然后选择删除旧应用。

对于个人 Microsoft 帐户，此网站是[帐户安全](https://account.microsoft.com/security)页。 对于工作或学校 Microsoft 帐户，此网站可能是 [MyApps](https://myapps.microsoft.com) 或组织已设置的自定义门户。

### <a name="how-do-i-remove-an-account-from-the-app"></a>如何从应用中删除帐户？
* iOS：从主屏幕中，向左滑动帐户磁贴。 选择“删除”。
* Windows Phone：在主屏幕中，依次选择菜单按钮、“**编辑帐户**”。 点击帐户名称旁边的 **X**。
* Android：在主屏幕中，依次选择菜单按钮、“**编辑帐户**”。 点击帐户名称旁边的 **X**。

如果你具有向组织注册的 Android 设备，可能需要完成额外的步骤来删除你的帐户。 在这些设备上，Microsoft Authenticator 应用自动注册为设备管理员。 如果你要完全卸载该应用，首先需要在应用设置中取消注册它。

### <a name="why-does-the-app-request-so-many-permissions"></a>应用为什么请求这么多的权限？
下面是我们请求的权限完整列表及它们在应用中的用法：

* **照相机**：当你添加工作、学校或非 Microsoft 帐户时，我们使用照相机来扫描 QR 代码。
* **联系人和电话**：当你使用个人 Microsoft 帐户登录时，我们会尝试通过查找你在手机中使用的现有帐户简化该过程。
* **短信**：当你首次使用个人 Microsoft 帐户登录时，我们必须确保你的电话号码与我们记录的相符。 我们将短信发送到你下载应用的手机。 短信包含 6-8 位验证码。 我们在短信中为你找到此代码，而不是要求你查找并输入。
* **在其他应用上绘制**：当收到让你验证自己身份的通知时，我们会将该通知显示在可能正运行的任何其他应用上方。
* **从 Internet 接收数据**：此权限是发送通知所必需的。
* **防止手机休眠**：如果你向组织注册设备，他们可以更改你手机上的这项策略。
* **控制振动**：你可以选择在收到验证身份的通知时是否希望手机振动。
* **使用指纹硬件**：每当你验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许你使用指纹而不是输入 PIN。
* **查看网络连接**：添加 Microsoft 帐户时，应用需要网络/Internet 连接。
* **读取存储内容**：你通过应用设置报告技术问题时才使用此权限。 收集存储中的某些信息来诊断问题。
* **完全网络访问**：此权限是发送通知以验证身份必需的。
* **启动时运行**：如果你重新打开手机，此权限可确保你继续接收通知以验证你的身份。

## <a name="contact-us"></a>联系我们
如果此处未解答你的问题，请在页面底部留言。 或者[联系支持人员](https://support.microsoft.com/contactus)，我们将尽快答复。

如果联系支持人员，请尽可能多包含以下信息：

* **用户 ID** – 尝试登录时使用的电子邮件地址。
* **错误的一般描述** – 实际看到的错误消息。  如果没有任何错误消息，请详细描述你所发现的意外行为。
* **页面** – 看到错误时所在的页面（包括 URL）。
* **错误代码** - 收到的特定错误代码。
* **会话 ID** - 收到的特定会话 ID。
* **相关性 ID** – 用户看到错误时所生成的相关性 ID 代码。
* **时间戳** – 看到错误时的精确日期和时间（包括时区）。

其中许多信息都位于登录页面。 如果未及时验证登录，请选择“查看详细信息”。

![登录错误详细信息](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

包含这些信息有助于我们尽快解决问题。

## <a name="related-topics"></a>相关主题
* [Azure 多重身份验证常见问题](multi-factor-authentication-faq.md)  
* 关于 Microsoft 帐户的[双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* [使用双重验证时遇到问题？](multi-factor-authentication-end-user-troubleshoot.md)




<!--HONumber=Nov16_HO3-->


