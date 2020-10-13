---
title: 有关 Microsoft Authenticator 应用的问题和解答 - Azure AD
description: 有关 Microsoft 身份验证应用和双重验证的常见问题和解答 (FAQ)。
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 09/23/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 00771f950271cfbc86994c01b6d866c5983f1b34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278662"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>常见问题 (常见问题解答) Microsoft Authenticator 应用

本文解答了有关 Microsoft Authenticator 应用的常见问题。 如果没有看到所提问题的答案，请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。

Microsoft Authenticator 应用取代了 Azure Authenticator 应用，在使用 Azure 多重身份验证时，建议使用此应用。 Microsoft Authenticator 应用可用于 [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 和 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)。

## <a name="frequently-asked-questions"></a>常见问题

| 问题 | Answer |
| -------- | ------ |
| 为什么在 watchOS 7 上出现 Apple Watch 问题？ | 在 watchOS 7 上批准通知时出现问题，我们正在与 Apple 合作以解决此问题。 同时，需要在你的手机上批准需要 Microsoft Authenticator watchOS 应用的任何通知。  |
| 注册设备是否表示同意授权公司或服务访问我的设备？ | 注册设备可使你的设备访问组织的服务，但不允许组织访问你的设备。 |
| 什么是应用锁定，如何使用它来帮助使我更安全？ | 应用锁定有助于使你的一次性密码、应用信息和应用设置更安全。 启用应用锁定后，每次打开身份验证器时，系统都会要求使用设备 PIN 或生物识别进行身份验证。 应用锁定还有助于确保你是唯一可以通过在批准登录通知时提示你提供 PIN 或生物识别来批准通知的人。 可以在 "验证器设置" 页上打开或关闭应用锁定。 默认情况下，当你在设备上设置 PIN 或生物识别时，应用锁定处于开启状态。<br><br>遗憾的是，不能保证应用锁定会阻止他人访问身份验证器。 这是因为设备注册可能发生在验证器之外的其他位置中，如 Android 帐户设置或公司门户应用。 |
| 我有一个 Windows Mobile 设备，而 Windows Mobile 上的 Microsoft Authenticator 已不推荐使用。 能否继续使用该应用进行身份验证？ | 在2020年7月15日之后，使用 Windows Mobile 上的 Microsoft Authenticator 的所有身份验证都将停用。 强烈建议使用备用身份验证方法，避免被锁定。<br>适用于企业用户的其他选项包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator。</li><li>[设置 SMS](multi-factor-authentication-setup-phone-number.md) 以接收验证码。</li><li>设置电话号码以接收电话号码来 [验证其身份](multi-factor-authentication-setup-office-phone.md)。</li></ul><br>适用于个人 Microsoft 帐户用户的其他选项包括：<br><ul><li>设置适用于 [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft Authenticator。</li><li>通过从 " [Microsoft 帐户安全性" 页](https://account.microsoft.com/security/)更新安全信息，设置 (短信或电子邮件) 的备用登录方法。</li></ul> |
| 我是否能在 Android 验证器上获取我的一次性密码 (OTP) 代码的屏幕截图？ | 从身份验证器 Android 的 release 6.2003.1704 开始，默认情况下所有 OTP 代码都会在执行身份验证器的屏幕截图时隐藏。 如果要在屏幕截图中查看 OTP 代码或允许其他应用捕获验证器屏幕，可以。 只需在验证器中打开 **屏幕捕获** 设置，然后重新启动应用程序。 |
| 哪些数据是 Authenticator 以我的名义存储的，我如何删除它？ | 验证器应用将收集三种类型的信息：<ul><li>你在添加帐户时提供的帐户信息。 可以通过删除帐户删除此数据。</li><li>诊断日志数据，在选择该应用的“帮助”菜单中的“发送日志”将日志发送到 Microsoft 之前，该数据只会存在于和保留在该应用中 。 这些日志可以包含个人数据，例如电子邮件地址、服务器地址或 IP 地址。 它们还可以包含设备数据，例如设备名称和操作系统版本。 收集的任何个人数据仅限于帮助排查应用问题所需的信息。 你可以随时在应用程序中浏览这些日志文件，以查看收集的信息。 如果发送日志文件，则身份验证应用工程师仅将其用于排查客户报告的问题。</li><li>非个人识别使用情况数据，例如“已启动添加帐户流/已成功添加了帐户”或者“通知已批准”。 此数据是我们的工程决策不可或缺的一部分。 你的使用情况可帮助我们确定如何以对你很重要的方式来改进应用。 首次使用该应用时，将看到此数据收集的通知。 然后，它会通知你可以在应用的 " **设置** " 页上关闭该功能。 您可以随时打开或关闭此设置。</li></ul> |
| 此应用中的代码有哪些用途？ | 打开身份验证器时，你会看到已添加的帐户作为磁贴。 你的工作或学校帐户以及你的个人 Microsoft 帐户将有六位或八位数字， (通过点击 "帐户" 磁贴) 来访问该帐户的全屏视图。 对于其他帐户，你将在应用的 " **帐户** " 页中看到六位或八位数字。<br>你将使用这些代码作为一次性密码来验证你是否为你所说的。 使用用户名和密码登录后，需要键入与该帐户关联的验证码。 例如，如果要 Katy 登录到 Contoso 帐户，请点击 "帐户" 磁贴，并使用验证码895823。 对于 Outlook 帐户，你可以执行相同的步骤。<br>点击 "Contoso 帐户" 磁贴。<br>![验证器应用中的帐户磁贴](media/user-help-auth-app-faq/katy-signin.png)<br>点击 Contoso 帐户磁贴后，验证代码在全屏显示。<br>![验证器中的帐户磁贴中的验证码](media/user-help-auth-app-faq/verification-code.png) |
| 为何代码旁边的数字不断地倒计数？ | 活动验证码的旁边可能会出现 30 秒倒计时。 此计时器可防止使用同一代码登录两次。 与密码不同，你不需要记住此数字。 此机制确保只有有权访问你手机的人才知道验证码。 |
| 帐户磁贴为何灰显？ | 某些组织要求使用身份验证器来使用单一登录，并保护组织资源。 在此情况下，该帐户不会用于双重验证，并且灰显或处于非活动状态。 此类帐户往往称为“代理”帐户。 |
| 什么是设备注册？ | 你的组织可能要求你注册设备以跟踪对受保护资源（如文件和应用）的访问。 他们还可能会启用条件访问，以减少对这些资源进行不必要的访问。 可以在“设置”中取消注册设备，但这可能会失去 Outlook 中电子邮件、OneDrive 中文件的访问权限，同时，可能无法使用手机登录。 |
| 是否需要连接到 Internet 或网络才能获取和使用验证码？ | 无需连接到 Internet 或数据网络即可获取验证码，因此，无需手机服务即可登录。 此外，应用在关闭后会停止运行，因为不会耗尽电池。 |
| 仅当应用处于打开状态时才会收到通知。 如果应用关闭，则无法收到通知。 | 如果接收的是通知而不是警报，则即使打开了铃声，也应该检查应用设置。 请确保在应用中启用声音或振动通知。 如果根本没有收到通知，应检查以下条件：<ul><li>手机是否进入“免打扰”或“静音”模式？ 这些模式可能阻止应用发送通知。</li><li>是否能从其他应用收到通知？ 如果不能，原因可能是手机出现网络连接问题，或者 Android 或 Apple 的通知通道出现问题。 可以尝试通过电话设置来解析网络连接。 可能需要与服务提供商联系，以帮助提供 Android 或 Apple 通知通道。</li><li>是否应用中的某些帐户可以收到通知，而其他帐户则不能？ 如果是，请从应用中删除有问题的帐户，然后再次添加并允许其发送通知，看看是否能解决问题。</li></ul>如果尝试了所有这些步骤但仍有问题，我们建议发送日志文件做诊断。 打开应用，转到“帮助”并选择“发送日志”。   完成此操作后，请前往 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) ，告诉我们你遇到的问题以及你尝试执行的步骤。 |
| 我在应用中使用了验证码，但如何切换到推送通知？ | 你可以为工作或学校帐户设置通知 (如果你的管理员) 或你的个人 Microsoft 帐户允许。 对于第三方帐户（如 Google 或 Facebook），通知将不起作用。<br>若要将你的个人帐户切换到通知，你必须使用该帐户重新注册设备。 转到“添加帐户”，选择“个人 Microsoft 帐户”，然后使用你的用户名和密码进行登录。<br>你的组织决定是否允许你的工作或学校帐户使用一键式通知。|
|通知是否适用于非 Microsoft 帐户 | 不适用，通知只适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果工作单位或学校使用的是 Azure AD 帐户，则可能会禁用此功能。 |
| 我购买了新的设备，或者从备份还原了我的设备。 如何实现在身份验证器中再次设置我的帐户？ | 如果在旧设备上启用了 **云备份** ，则可以使用旧的备份在新的 IOS 或 Android 设备上恢复帐户凭据。 有关详细信息，请参阅在验证器中 [备份和恢复帐户凭据](user-help-auth-app-backup-recovery.md) 一文。 |
| 我丢失了设备或者改用了新设备。 如何确保不会继续向旧设备发送通知？ | 将身份验证器添加到新设备不会自动从旧设备中删除该应用。 从旧设备中删除该应用并不足够。 您必须从旧设备中删除该应用程序，并告诉 Microsoft 或您的组织忘记并取消注册旧设备。<ul><li>**使用个人 Microsoft 帐户从设备中删除应用。** 转到[帐户安全](https://account.microsoft.com/security) 页的双重验证区域，选择关闭旧设备的验证。</li><li>**使用工作或学校 Microsoft 帐户从设备中删除应用。** 转到 [MyApps 页面](https://myapps.microsoft.com/) 或组织的自定义门户的双重验证区域，以关闭旧设备的验证。</li></ul> |
| 如何从应用删除帐户？ | 点击要从应用中删除的帐户的 "帐户" 磁贴，以查看帐户 "全屏"。 点击 " **删除帐户** " 以从应用中删除帐户。<br>如果你的设备已注册到组织，你可能需要额外的步骤来删除帐户。 在这些设备上，验证器自动注册为设备管理员。 如果想要彻底卸载应用，需受先在应用设置中取消注册应用。 |
| 应用为什么请求这么多的权限？ | 下面是可能需要的权限完整列表及它们在应用中的用法。 所见到的特定权限将取决于所持有的电话类型。<ul><li>使用生物识别硬件。 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 应用要求你同意使用生物识别或面部识别，而不是输入 PIN。</li><li>**相机。** 在添加工作、学校或非 Microsoft 帐户时用于扫描 QR 码。</li><li>**联系人和电话。** 应用需要此权限才能在手机上搜索工作或学校 Microsoft 帐户，并将其添加到应用。</li><li>**短信。** 用于确保首次使用个人 Microsoft 帐户登录时，电话号码与记录中的数字匹配。 我们向手机发送一条短信，其中包含6-8 位验证码。 不需要查找此代码并输入它，因为身份验证器会自动在短信中查找它。</li><li>**在其他应用上绘制。** 验证身份的通知也会显示在任何其他正在运行的应用上。</li><li>**从 Internet 接收数据。** 必须使用此权限发送通知。</li><li>**防止手机休眠。** 如果向组织注册设备，组织可以更改手机上的这项策略。</li><li>**控制振动。** 可以选择在收到验证身份的通知时是否希望手机振动。</li><li>**使用指纹硬件。** 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许使用指纹而不是输入 PIN。</li><li> **查看网络连接。** 添加 Microsoft 帐户时，应用需要网络/Internet 连接。</li><li>**读取存储内容。** 仅当通过应用设置报告技术问题时才使用此权限。 会从存储中收集某些信息来诊断问题。</li><li>**完全网络访问。** 必须使用此权限发送通知以验证身份。</li><li>**启动时运行。** 如果重启手机，此权限可确保继续接收通知以验证身份。</li></ul> |
| 为什么使用身份验证器在不解锁设备的情况下批准请求？ | 同意验证请求时无需解锁设备，因为仅需证明的是你带了你的电话。 双重验证要求提供两项证明：一件你知道的事和一件你拥有的物品。 你只需知道密码。 你使用的是手机 (使用身份验证器进行设置，并将其注册为 MFA 证明。 ) 因此，让手机和批准请求符合第二个身份验证因素的标准。 |
| 为什么打开 Apple Watch 上的身份验证器时，我的所有帐户都不显示？ | 身份验证器仅支持 Apple Watch 配套应用上包含推送通知的 Microsoft 个人或学校或工作帐户。 对于其他帐户（如 Google 或 Facebook），必须打开手机上的验证器应用以查看验证代码。 |
| 为何无法在 Apple Watch 上批准或拒绝通知？ | 首先，请确保已升级到 iPhone 上的6.0.0 或更高版本的身份验证器。 然后，在 Apple Watch 上打开 Microsoft Authenticator 伴侣应用，并查看其下面带有“设置”按钮的所有帐户。  完成设置过程以批准这些帐户的通知。 |
| 我收到 Apple Watch 和手机之间出现通信错误的消息。 我可以做什么来排除故障？ | 当 Watch 屏幕在完成与手机通信之前进入睡眠状态时会发生此错误。<br><b>如果在安装过程中发生错误：</b><br>再次尝试运行安装程序，在完成之前，请确保 Watch 处于待机状态。 同时打开手机上的应用，并响应任何出现的提示。<br>如果你的手机和手表仍未通信，你可以尝试以下操作：<ol><li>强制退出 Microsoft Authenticator 手机应用，并在 iPhone 上再次打开它。</li><li>在 Apple Watch 上强制退出伴侣应用。<ol><li> 在 Watch 上打开 Microsoft Authenticator 伴侣应用</li><li>按住边侧按钮，直到“关机”屏幕出现。 </li><li>松开边侧按钮，并按住 Digital Crown 强制退出活动的应用。</li></ol></li><li>关闭手机和 Watch 的蓝牙与 Wi-Fi，然后重新打开。</li><li>重启 iPhone 和 Watch。</li></ol><b>如果尝试批准通知时出现错误：</b><br>下次试图批准 Apple Watch 上的通知时，在请求完成并听到表示成功的提示音之前，请保持屏幕处于待机状态。 |
| 为何适用于 Apple Watch 的 Microsoft Authenticator 伴侣应用在手表上不同步或显示？ | 如果应用未显示在手表上，请尝试以下操作： <ol><li>确保 Watch 运行 watchOS 4.0 或更高版本。</li><li>再次同步 Watch。</li></ol> |
| 我的 Apple Watch 伴侣应用已崩溃。 是否可以向你们发送崩溃日志以便调查？ | 首先，请务必选择与我们共享分析数据。 如果你是 TestFlight 用户，则已注册。 否则，可以转到“设置”>“隐私”>“分析”，并同时选择“共享 iPhone 和 Watch 分析”和“与应用开发人员共享”选项。  <br>注册后，可以尝试重现崩溃，因此自动将崩溃日志发送给我们进行调查。 但是，如果无法重现崩溃，可以手动复制日志文件并将其发送给我们。<ol><li>在手机上打开 Watch 应用，转到“设置”>“通用”，然后单击“复制 Watch 分析数据”。 </li><li>在“设置”>“隐私”>“分析”>“分析数据”下找到相应的崩溃信息，然后手动复制整个文本。 </li><li>在手机上打开身份验证器，并将复制的文本粘贴到 "**发送日志**" 页上的 "**与应用程序开发人员共享**" 文本框中。</li></ol> |
| 什么是应用锁定功能，它如何帮助我更加安全？ | 为了使你的密码、应用信息和应用设置更安全，你可以在验证器中打开应用锁定功能。 如果从 "验证器**设置**" 页打开**应用锁定**，则会要求你在每次打开身份验证器时使用 PIN 或生物识别进行身份验证。 此功能提供额外的保护，在验证器中批准通知的方式不会发生变化。<br>**注意**<br>遗憾的是，不能保证应用锁定会阻止他人访问身份验证器。 这是因为设备注册可能发生在验证器之外的其他位置，例如在公司门户应用或 Android 帐户设置中。 |
| 为什么会收到关于帐户活动的通知？ | 只要更改了个人 Microsoft 帐户，就会立即将活动通知发送到身份验证器，从而使你更安全。 我们以前仅通过电子邮件和短信发送了这些通知。 有关这些活动通知的详细信息，请参阅[存在异常帐户登录时会发生什么](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)。 要更改接收通知的位置，请登录到帐户的[我们可以通过非关键帐户警报在何处与你联系](https://account.live.com/SecurityNotifications/Update)页面。 |
| 使用 iOS 附带的默认邮件应用登录到我的工作或学校帐户时，会收到由身份验证程序提示我输入安全验证信息的提示。 在输入该信息并返回邮件应用后，我收到一个错误。 我该怎么办？ | 出现这种情况的原因很可能是，登录操作和邮件应用操作发生在两个不同的应用中，导致初始后台登录过程停止工作并发生故障。 若要尝试解决此问题，我们建议在登录到邮件应用时选择屏幕右下角的“Safari”图标。 通过移动到 Safari，整个登录过程将在单个应用中进行，从而使你能够成功登录到应用。 |
| 我的一次性密码 (OTP) 代码不起作用。 我该怎么办？ | 请确保设备上的日期和时间正确，并且会自动同步。 如果日期和时间错误或不同步，该代码将不起作用。 |
| Windows 10 移动版操作系统已于 2019 年 12 月弃用。 是否也会弃用 Windows 移动版操作系统上的 Microsoft Authenticator？ | 2020年2月28日以后，将不支持所有 Windows Mobile 操作系统上的身份验证器。 在上述日期之后，用户将无法收到任何新的应用更新。 2020 年 2 月 28 日之后，当前支持在所有 Windows 移动版操作系统上使用 Microsoft Authenticator 进行身份验证的 Microsoft 服务将开始停止提供其支持。 为了向 Microsoft 服务进行身份验证，我们强烈建议所有用户在此日期之前切换到备用身份验证机制。 |

## <a name="next-steps"></a>后续步骤

- 如果在获取个人 Microsoft 帐户的验证码时遇到问题，请参阅 [Microsoft 帐户安全信息和验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的“排查验证码问题”部分。

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如需有关安全信息的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)

- 如果此文档没有回答问题，请告知我们。 转到 [Microsoft 验证器应用论坛](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) 发布问题并从社区获取帮助或者在此页面上留言，我们会尽快解答相关问题。