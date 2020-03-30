---
title: 有关 Microsoft 身份验证器应用&答案的问题 - Azure AD
description: 有关 Microsoft 身份验证应用和双重验证的常见问题和答案 （FAQ）。
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 86b2311c31e5f291968773ac2e34ebc4f6a92003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284001"
---
# <a name="frequently-asked-questions-faqs-about-the-microsoft-authenticator-app"></a>有关 Microsoft 身份验证器应用的常见问题 （FAQ）

本文解答了有关 Microsoft Authenticator 应用的常见问题。 如果没有看到所提问题的答案，请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。

Microsoft Authenticator 应用替代了 Azure Authenticator 应用，建议使用 Azure 多重身份验证时使用该应用。 Microsoft Authenticator 应用可用于 [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 和 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

## <a name="frequently-asked-questions"></a>常见问题

| 问题 | 解决方案 |
| -------- | -------- |
| 我可以在 Android 微软身份验证器上截取 OTP 代码的屏幕截图吗？ | 默认情况下，当获取身份验证器的屏幕截图时，将隐藏所有 OTP 代码以更好地保护我们的用户，因此，根据 Microsoft 身份验证器 Android 版本 6.2003.1704，将隐藏所有 OTP 代码。 如果用户希望查看屏幕截图中的 OTP 代码或允许其他应用捕获身份验证器的屏幕，则可以通过在身份验证器应用中启用"屏幕捕获"设置，然后重新启动应用来执行此操作。 |
| 哪些数据是 Authenticator 以我的名义存储的，我如何删除它？ | Microsoft Authenticator 应用收集三种类型的信息：<ul><li>你在添加帐户时提供的帐户信息。 可以通过删除帐户删除此数据。</li><li>诊断日志数据仅保留在应用中，直到您选择 **"发送日志**"**应用的帮助菜单**以向 Microsoft 发送日志。 这些日志文件包含个人数据，如您的电子邮件地址（如），alain@contoso.com服务器或 IP 地址和设备数据（如设备名称和操作系统版本），个人数据仅限于帮助解决应用问题所必需的信息。 任何时候都可以在应用中查看这些日志文件来查看所收集的信息。 如果你发送日志文件，则身份验证应用工程师可以使用它来解决客户报告的问题。</li><li>非个人可识别的使用数据，例如"启动添加帐户流/成功添加帐户"或"已批准的通知"。 此数据是我们的工程决策不可或缺的一部分，并帮助我们确定哪些功能对你很重要，以及什么地方需要以应用更新的形式进行改进。 作为应用用户，您会在第一次启动应用时看到此数据收集的通知，并被告知可以在应用的 **"设置"** 页上将其关闭。 可以在任何时候启用或禁用此设置。</li></ul> |
| 此应用中的代码有哪些用途？ | 打开 Microsoft 身份验证器应用时，您将看到添加的帐户为磁贴。<li>在 iOS 设备上，您的工作或学校帐户以及您的个人 Microsoft 帐户在帐户的全屏视图中将可以看到六位或八位数字（通过点击帐户磁贴访问）。<br><br>![应用中的“帐户”屏幕](./media/user-help-auth-app-faq/auth-app-accounts.png)<li>对于 iOS 设备上的其他帐户和 Android 设备上的所有帐户，您将在应用的"帐户"页中看到六位或八位数字。 你将使用这些代码来验证自己声明的身份。 使用用户名和密码登录后，您将键入与该帐户关联的验证码。 例如，如果您正在登录 Contoso 帐户，并且正在使用 iOS 设备，则点击帐户磁贴，然后使用验证码验证您的身份。 如果您是凯蒂登录到 Outlook 帐户，则遵循相同的步骤。<br><br>![点击应用中的帐户磁贴后](./media/user-help-auth-app-faq/katy-signin.png)<br><br>点击 Contoso 帐户磁贴后，Katy 在全屏视图中看到验证码，然后输入 895823 完成登录。<br><br>![应用程序中的验证码屏幕](./media/user-help-auth-app-faq/verification-code.png) |
| 为何代码旁边的数字不断地倒计数？ | 活动验证码的旁边可能会出现 30 秒倒计时。 此计时器可防止使用同一代码登录两次。 与密码不同，你不需要记住此数字。 此机制确保只有有权访问你手机的人才知道验证码。 |
| 帐户磁贴为何灰显？ | 某些组织要求 Microsoft Authenticator 应用使用单一登录并保护组织资源。 在此情况下，该帐户不会用于双重验证，并且灰显或处于非活动状态。 此类帐户往往称为“代理”帐户。 |
| 什么是设备注册？ | 您的组织可能希望您注册设备，以便他们了解设备是否正在访问安全资源，如文件和应用程序。 它们还可能打开条件访问，以减少意外访问这些资源的风险。 您可以在 **"设置"** 中取消注册设备，但可能会失去对 Outlook 中的电子邮件、OneDrive 中的文件的访问权限，并且您将失去使用电话登录的功能。 |
| 是否需要连接到 Internet 或网络才能获取和使用验证码？ | 无需连接到 Internet 或数据网络即可获取验证码，因此，无需手机服务即可登录。 此外，应用在关闭后会停止运行，因为不会耗尽电池。 |
| 仅当应用处于打开状态时才会收到通知。 如果应用关闭，则无法收到通知。 | 如果接收的是通知而不是警报，则即使打开了铃声，也应该检查应用设置。 请确保在应用中启用声音或振动通知。 如果完全收不到通知，应检查是否存在以下情况：<ul><li>手机是否进入“免打扰”或“静音”模式？ 这些模式可能阻止应用发送通知。</li><li>是否能从其他应用收到通知？ 如果不能，原因可能是手机出现网络连接问题，或者 Android 或 Apple 的通知通道出现问题。 可以尝试通过手机设置解决网络连接问题，但可能需要联系服务提供商来帮助解决 Android 或 Apple 通知通道问题。</li><li>是否应用中的某些帐户可以收到通知，而其他帐户则不能？ 如果是，请从应用中删除有问题的帐户，然后再次添加并允许其发送通知，看看是否能解决问题。</li></ul>如果尝试了所有这些步骤但仍有问题，我们建议发送日志文件做诊断。 打开应用，转到“帮助”并选择“发送日志”。******** 然后，转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)告知遇到的问题，以及目前为止已尝试过的步骤。 |
| 我在应用中使用了验证码，但如何切换到推送通知？ | 可以针对工作或学校帐户（如果管理员已启用）或个人 Microsoft 帐户设置此方法，并通知并不适用于 Google 或 Facebook 等第三方帐户。<br><br>若要将个人帐户切换到通知，必须在帐户中重新注册设备。 转到“添加帐户”****，选择“个人 Microsoft 帐户”****，然后使用你的用户名和密码进行登录。<br><br>组织会决定是否允许工作或学校帐户使用一键式通知，因此，组织可能已禁用此功能。|
|通知是否适用于非 Microsoft 帐户 | 不适用，通知只适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果工作单位或学校使用的是 Azure AD 帐户，则可能会禁用此功能。 |
| 我购买了新的设备，或者从备份还原了我的设备。 如何再次在 Microsoft Authenticator 应用中设置我的帐户？ | 如果您正在运行 iOS 或 Android 设备，并且如果您在旧设备上打开了**云备份**，则可以使用旧备份在新设备上恢复帐户凭据。 有关详细信息，请参阅[使用 Microsoft Authenticator 应用备份和恢复帐户凭据](user-help-auth-app-backup-recovery.md)一文。 |
| 我丢失了设备或者改用了新设备。 如何确保不会继续向旧设备发送通知？ | 将 Microsoft Authenticator 应用添加到新设备不会自动从旧设备上删除该应用。 从旧设备中删除该应用并不足够。 必须从旧设备中删除该应用，同时告知 Microsoft 或组织忘记旧设备，并从帐户中注销该设备。<ul><li>**使用个人 Microsoft 帐户从设备中删除应用。** 转到[帐户安全](https://account.microsoft.com/security) 页面的两步验证区域，然后选择关闭旧设备的验证。</li><li>**使用工作或学校 Microsoft 帐户从设备中删除应用。** 转到[MyApps](https://myapps.microsoft.com/)页面的两步验证区域或组织的自定义门户，然后选择关闭旧设备的验证。</li></ul> |
| 如何从应用中删除帐户？ | <ul><li>**Ios。** 点击要从应用中删除的帐户的帐户磁贴以加载帐户的全屏视图。 点击 **"删除帐户"** 选项从应用中删除帐户。</li><li>**Android。** 在主屏幕中，依次选择菜单按钮、“编辑帐户”。**** 点击帐户名称旁边的 **X**。</li></ul>如果拥有已注册到组织的设备，可能需要完成一个额外步骤才能删除帐户。 在这些设备上，Microsoft Authenticator 应用自动注册为设备管理员。 如果要完全卸载该应用，首先需要在应用设置中取消注册它。 |
| 应用为什么请求这么多的权限？ | 下面是可能需要访问的权限的完整列表，以及应用如何使用这些权限。 您看到的特定权限将取决于您拥有的电话类型。<ul><li>**相机。** 在添加工作、学校或非 Microsoft 帐户时用于扫描 QR 码。</li><li>**联系人和电话。** 该应用程序需要此权限，以便它可以搜索手机上的现有工作或学校 Microsoft 帐户并将其添加到应用，从而有助于确保您的帐户正常工作。</li><li>**短信。** 用于确保在首次使用个人 Microsoft 帐户登录时， 电话号码与记录中的号码匹配。 我们会将包含 6-8 位数验证码的短信发送到下载应用的手机。 我们不会要求在应用中查找并输入此代码，而是在短信中发送此代码。</li><li>**在其他应用上绘制。** 身份验证的通知也会显示在可能正运行的其他任何应用上。</li><li>**从 Internet 接收数据。** 必须使用此权限发送通知。</li><li>**防止手机休眠。** 如果向组织注册设备，组织可以更改手机上的这项策略。</li><li>**控制振动。** 可以选择在收到验证身份的通知时是否希望手机振动。</li><li>**使用指纹硬件。** 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许使用指纹而不是输入 PIN。</li><li> **查看网络连接。** 添加 Microsoft 帐户时，应用需要网络/Internet 连接。</li><li>**读取存储内容。** 仅当通过应用设置报告技术问题时才使用此权限。 收集存储中的某些信息来诊断问题。</li><li>**完全网络访问。** 必须使用此权限发送通知以验证身份。</li><li>**启动时运行。** 如果重启手机，此权限可确保继续接收通知以验证身份。</li></ul> |
| 为何 Microsoft Authenticator 应用允许在不解锁设备的情况下批准请求？ | 同意验证请求时无需解锁设备，因为仅需证明的是你带了你的电话。 双重验证要求提供两项证明：一件你知道的事和一件你拥有的物品。 你只需知道密码。 您拥有的是您的手机（使用 Microsoft 身份验证器应用设置并注册为 MFA 证明。因此，让电话和批准请求符合第二个身份验证因素的条件。 |
| 为什么在 Apple Watch 上打开 Microsoft 身份验证器应用时，我的所有帐户都未显示？ | 在 Apple Watch 伴侣应用中，Microsoft Authenticator 应用仅支持使用 Microsoft 个人帐户或者学校或工作帐户发送推送通知。 对于其他帐户（如 Google 或 Facebook），您必须在手机上打开身份验证器应用才能查看验证码。 |
| 为什么我无法批准或拒绝 Apple Watch 上的通知？ | 首先，请确保您已升级到 Microsoft 身份验证器应用，iPhone 上的版本为 6.0.0 或更高版本。 然后，在 Apple Watch 上打开 Microsoft Authenticator 伴侣应用，并查看其下面带有“设置”按钮的所有帐户。**** 必须完成设置过程才能批准这些帐户的通知。 |
| 我收到苹果手表和我的手机之间的通信错误。 我可以做什么来排除故障？ | 当 Watch 屏幕在完成与手机通信之前进入睡眠状态时会发生此错误。<br><br><b>如果在安装期间出现这种情况：</b><br>再次尝试运行安装程序，在完成之前，请确保 Watch 处于待机状态。 同时打开手机上的应用，并响应任何出现的提示。<br><br>如果您的手机和手表仍然无法通信，您可以尝试以下操作：<ol><li>强制退出 Microsoft Authenticator 手机应用，并在 iPhone 上再次打开它。</li><li>在 Apple Watch 上强制退出伴侣应用。<ol><li> 在 Watch 上打开 Microsoft Authenticator 伴侣应用</li><li>按住边侧按钮，直到“关机”屏幕出现。****</li><li>松开边侧按钮，并按住 Digital Crown 强制退出活动的应用。</li></ol></li><li>关闭手机和 Watch 的蓝牙与 Wi-Fi，然后重新打开。</li><li>重启 iPhone 和 Watch。</li></ol><b>如果尝试批准通知时发生这种情况：</b><br>下次试图批准 Apple Watch 上的通知时，在请求完成并听到表示成功的提示音之前，请保持屏幕处于待机状态。 |
| 为什么适用于 Apple Watch 的 Microsoft 身份验证器配套应用程序无法同步或显示在我的手表上？ | 如果应用未显示在手表上，请尝试以下操作： <ol><li>确保 Watch 运行 watchOS 4.0 或更高版本。</li><li>再次同步 Watch。</li></ol> |
| 我的 Apple Watch 伴侣应用已崩溃。 是否可以向你们发送崩溃日志以便调查？ | 您首先必须确保您已选择与我们共享您的分析。 如果您是 TestFlight 用户，则已注册。 否则，可以转到“设置”>“隐私”>“分析”，并同时选择“共享 iPhone 和 Watch 分析”和“与应用开发人员共享”选项。************<br><br>注册后，可以尝试重现崩溃，因此自动将崩溃日志发送给我们进行调查。 但是，如果您无法重现崩溃，您可以手动复制日志文件并将其发送给我们。<ol><li>在手机上打开 Watch 应用，转到“设置”>“通用”，然后单击“复制 Watch 分析数据”。********</li><li>在“设置”>“隐私”>“分析”>“分析数据”下找到相应的崩溃信息，然后手动复制整个文本。****</li><li>在手机上打开 Microsoft Authenticator 应用，并将复制的文本粘贴到“发送日志”页上的“与应用开发人员共享”文本框中。********</li></ol> |
| 什么是应用锁定功能，它如何帮助我更加安全？ | 若要使你的一次性密码、应用信息和应用设置更加安全，可以在 Microsoft Authenticator 应用中开启应用锁定功能。 从 Microsoft 身份验证器应用的 **"设置"** 屏幕打开应用锁定，这意味着每次打开 Microsoft 身份验证器应用时，系统都会要求您使用 PIN 或生物识别进行身份验证。 此功能提供了额外的保护，您在 Microsoft 身份验证器应用中批准通知的方式不会更改。<br><br>**注意**<br>由于设备注册可能发生在 Microsoft 身份验证器应用之外的其他位置（如在公司门户应用中或在 Android 帐户设置中）进行，因此不能保证应用锁定会阻止用户访问 Microsoft身份验证器应用。 |
| 为什么会收到关于帐户活动的通知？ | 为了帮助你更好地掌握 Microsoft 个人帐户状况的最新信息，我们会向你的 Microsoft Authenticator 应用发送活动通知。 这些通知会在发生更改后立即显示，帮助提高安全性。 我们之前通过电子邮件或短信发送这些通知，现在将其扩展为包括应用。 有关这些活动通知的详细信息，请参阅[存在异常帐户登录时会发生什么](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)。 要更改接收通知的位置，请登录"[哪里可以与您联系您的帐户的非关键帐户提醒](https://account.live.com/SecurityNotifications/Update)页面"。 |
| 使用 iOS 附带的默认邮件应用登录到我的工作或学校帐户时，系统会提示 Microsoft 身份验证器应用提供安全验证信息。 输入该信息并返回到邮件应用后，我收到错误。 我该怎么办？ | 这最有可能发生的原因是，您的登录和邮件应用发生在两个不同的应用中，从而导致初始后台登录过程停止工作并失败。 要尝试解决此问题，我们建议您在登录到邮件应用时选择屏幕右下角的**Safari**图标。 通过移动到 Safari，整个登录过程发生在单个应用中，从而允许您成功登录到应用。 |
| 我的一次性密码 （OTP） 代码不起作用。 我该怎么办？ | 确保设备上的日期和时间正确且正在自动同步。 如果日期和时间错误或不同步，代码将无法工作。 |
| Windows 10 移动操作系统于 2019 年 12 月被弃用。 Windows 移动操作系统上的 Microsoft 身份验证器是否会也被弃用？ | 2020 年 2 月 28 日之后，所有 Windows 移动操作系统上的 Microsoft 身份验证器应用将不受支持。 用户将没有资格在上述日期后收到任何新的更新的应用程序。 2020 年 2 月 28 日之后，目前支持在所有 Windows Mobile 操作系统上使用 Microsoft 身份验证器进行身份验证的 Microsoft 服务将开始停用其支持。 为了对 Microsoft 服务进行身份验证，我们强烈建议所有用户在此日期之前切换到备用身份验证机制。 |

## <a name="next-steps"></a>后续步骤

- 如果您在获取个人 Microsoft 帐户的验证码时遇到问题，请参阅[Microsoft 帐户安全信息&验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文的**故障排除验证码问题**部分。

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如果需要有关安全信息的详细信息，请参阅[安全信息（预览）概述](user-help-security-info-overview.md)

- 如果此文档没有回答问题，请告知我们。 请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp)发布问题，并从社区获取帮助，或在此页面留下评论。
