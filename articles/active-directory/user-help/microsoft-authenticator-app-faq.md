---
title: 有关 Microsoft Authenticator 应用的帮助 - Azure Active Directory | Microsoft Docs
description: 提供与 Microsoft Authenticator 应用和 Azure 多重身份验证相关的常见问题与解答列表。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: c2071e662b50e57189e048b028fd5359482c817f
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358643"
---
# <a name="microsoft-authenticator-app-faq"></a>Microsoft Authenticator 应用常见问题解答

本文解答了有关 Microsoft Authenticator 应用的常见问题。 如果没有看到所提问题的答案，请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)。 此外，[使用手机登录时的常见问题解答](microsoft-authenticator-app-phone-signin-faq.md)中也提供了有关手机登录过程的详细信息。

Microsoft Authenticator 应用替代了 Azure Authenticator 应用，建议使用 Azure 多重身份验证时使用该应用。 Microsoft Authenticator 应用适用于 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)。

## <a name="frequently-asked-questions"></a>常见问题
|问题|解决方案|
|--------|--------|
|哪些数据是 Authenticator 以我的名义存储的，我如何删除它？| Microsoft Authenticator 应用存储添加帐户时创建的帐户信息。 使用该应用时，将会创建诊断日志用于调试，其中只存储有助于 Microsoft 诊断不可预见问题的数据。 可以打开该应用，并依次选择“帮助”、“发送日志”、“查看日志”按钮来查看日志数据。<br><br>在“帐户”屏幕上删除帐户磁贴可以删除所有数据，包括日志文件。 有关 Microsoft 如何使用数据的详细信息，请参阅[服务信任门户](https://servicetrust.microsoft.com/ViewPage/HomePage.)|
|此应用中的代码有哪些用途？|打开 Microsoft Authenticator 应用时，会看到已添加的帐户，它们以带有六位数或八位数编号的磁贴形式显示。<br><br>你将使用这些代码来验证自己声明的身份。 使用用户名和密码登录后，需要键入帐户磁贴中显示相同的验证码。 例如，如果以 Alain 的身份登录到 Contoso 帐户，应使用验证码 427303。<br><br>![应用中的“帐户”屏幕](./media/microsoft-authenticator-app-faq/auth-app-accounts.png)|
|为何代码旁边的数字不断地倒计数？|活动验证码的旁边可能会出现 30 秒倒计时。 此计时器可防止使用同一代码登录两次。 与密码不同，你不需要记住此数字。 此机制确保只有有权访问你手机的人才知道验证码。|
|帐户磁贴为何灰显？|某些组织要求 Microsoft Authenticator 应用使用单一登录并保护组织资源。 在此情况下，该帐户不会用于双重验证，并且灰显或处于非活动状态。 此类帐户往往称为“代理”帐户。|
|所有图标的含义是什么？|![手机铵键图标](./media/microsoft-authenticator-app-faq/icon.png)<br>手机按键图标表示帐户已升级，可以使用无密码手机登录。 可以针对所有个人 Microsoft 帐户以及 Authenticator 应用中的单个工作或学校帐户启用此图标。 有关手机登录的详细信息，请参阅[使用手机（而不是密码）登录](microsoft-authenticator-app-phone-signin-faq.md)<br><br>![锁定图标](./media/microsoft-authenticator-app-faq/lock.png)<br>锁定图标表明工作或学校帐户已注册到组织。 组织可能希望你注册设备，以便知道哪些设备正在访问受保护的资源（例如文件和应用），并可能启用条件访问，以减少不当地访问这些资源的风险。|
|什么是设备注册？|组织可能希望你注册设备，以便知道哪些设备正在访问受保护的资源（例如文件和应用），并可能启用条件访问，以减少不当地访问这些资源的风险。 可以使用“设置”来取消注册设备，但这可能会失去 Outlook 中电子邮件、OneDrive 中文件的访问权限，同时，可能无法使用手机登录。|
|是否需要连接到 Internet 或网络才能获取和使用验证码？|无需连接到 Internet 或数据网络即可获取验证码，因此，无需手机服务即可登录。 此外，应用在关闭后会停止运行，因为不会耗尽电池。|
|仅当应用处于打开状态时才会收到通知。 如果应用关闭，则无法收到通知。|如果接收的是通知而不是警报，则即使打开了铃声，也应该检查应用设置。 请确保在应用中启用声音或振动通知。 如果完全收不到通知，应检查是否存在以下情况：<ul><li>手机是否进入“免打扰”或“静音”模式？ 这些模式可能阻止应用发送通知。</li><li>是否能从其他应用收到通知？ 如果不能，原因可能是手机出现网络连接问题，或者 Android 或 Apple 的通知通道出现问题。 可以尝试通过手机设置解决网络连接问题，但可能需要联系服务提供商来帮助解决 Android 或 Apple 通知通道问题。</li><li>是否应用中的某些帐户可以收到通知，而其他帐户则不能？ 如果是，请从应用中删除有问题的帐户，然后再次添加并允许其发送通知，看看是否能解决问题。</li></ul>如果尝试了所有这些步骤但仍有问题，我们建议发送日志文件做诊断。 打开应用，转到“帮助”并选择“发送日志”。 然后，转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)告知遇到的问题，以及目前为止已尝试过的步骤。|
|我在应用中使用了验证码，但如何切换到推送通知？|可以针对工作或学校帐户（如果管理员已启用）或个人 Microsoft 帐户设置此方法，并通知并不适用于 Google 或 Facebook 等第三方帐户。<br><br>若要将个人帐户切换到通知，必须在帐户中重新注册设备，并设置推送通知。<br><br>组织会决定是否允许工作或学校帐户使用一键式通知，因此，组织可能已禁用此功能。|
|通知是否适用于非 Microsoft 帐户|不适用，通知仅适用于 Microsoft 帐户和 Azure Active Directory 帐户。 如果工作单位或学校使用的是 Azure AD 帐户，则可能会禁用此功能。|
|我购买了新的设备，或者从备份还原了我的设备。 如何再次在 Microsoft Authenticator 应用中设置我的帐户？|如果运行 iOS 设备，已启用“自动备份”，并且在旧设备上创建了帐户的备份，则可以使用该备份在新设备上恢复帐户凭据。 有关详细信息，请参阅[使用 Microsoft Authenticator 应用备份和恢复帐户凭据](microsoft-authenticator-app-backup-and-recovery.md)一文。|
|我丢失了设备或者改用了新设备。 如何确保不会继续向旧设备发送通知？|将 Microsoft Authenticator 应用添加到新 iOS 设备不会自动从旧设备上删除该应用。 从旧设备中删除该应用并不足够。 必须从旧设备中删除该应用，同时告知 Microsoft 或组织忘记旧设备，并从帐户中注销该设备。<ul><li>**使用个人 Microsoft 帐户从设备中删除应用。** 转到[帐户安全](https://account.microsoft.com/security) 页的双重验证区域，选择关闭旧设备的验证。</li><li>**使用工作或学校 Microsoft 帐户从设备中删除应用。** 转到[我的应用](https://myapps.microsoft.com/)页的双重验证区域或转到组织的自定义门户，选择关闭旧设备的验证。</li></ul>|
|如何从应用中删除帐户？|<ul><li>**iOS。** 在主屏幕中，依次选择菜单按钮、“编辑帐户”。 依次点击帐户名旁边的红色图标、“删除帐户”。</li><li>**Windows Phone。** 在主屏幕中，依次选择菜单按钮、“编辑帐户”。 点击帐户名称旁边的 **X**。</li><li>**Android。** 在主屏幕中，依次选择菜单按钮、“编辑帐户”。 点击帐户名称旁边的 **X**。</li></ul>如果拥有已注册到组织的设备，可能需要完成一个额外步骤才能删除帐户。 在这些设备上，Microsoft Authenticator 应用自动注册为设备管理员。 如果要完全卸载该应用，首先需要在应用设置中取消注册它。|
|应用为什么请求这么多的权限？|下面是可能需要的权限完整列表及它们在应用中的用法。 所见到的特定权限取决于所持有的电话类型。<ul><li>**相机。** 在添加工作、学校或非 Microsoft 帐户时用于扫描 QR 码。</li><li>**联系人和电话。** 在使用个人 Microsoft 帐户登录时，用于通过查找手机中的现有帐户来简化过程。</li><li>**短信。** 用于确保在首次使用个人 Microsoft 帐户登录时， 电话号码与记录中的号码匹配。 我们会将包含 6-8 位数验证码的短信发送到下载应用的手机。 我们不会要求在应用中查找并输入此代码，而是在短信中发送此代码。</li><li>**在其他应用上绘制。** 身份验证的通知也会显示在可能正运行的其他任何应用上。</li><li>**从 Internet 接收数据。** 必须使用此权限发送通知。</li><li>**防止手机休眠。** 如果向组织注册设备，组织可以更改手机上的这项策略。</li><li>**控制振动。** 可以选择在收到验证身份的通知时是否希望手机振动。</li><li>**使用指纹硬件。** 每当验证身份时，某些工作和学校帐户需要其他的 PIN。 为了使过程更加简单，我们允许使用指纹而不是输入 PIN。</li><li> **查看网络连接。** 添加 Microsoft 帐户时，应用需要网络/Internet 连接。</li><li>**读取存储内容。** 仅当通过应用设置报告技术问题时才使用此权限。 收集存储中的某些信息来诊断问题。</li><li>**完全网络访问。** 必须使用此权限发送通知以验证身份。</li><li>**启动时运行。** 如果重启手机，此权限可确保继续接收通知以验证身份。</li></ul>|
|为何 Microsoft Authenticator 应用允许在不解锁设备的情况下批准请求？|同意验证请求时无需解锁设备，因为仅需证明的是你带了你的电话。 双重验证要求提供两项证明：一件你知道的事和一件你拥有的物品。 你只需知道密码。 拥有的物品是手机（已使用 Microsoft Authenticator 应用设置，并已注册为 MFA 证明）。因此，拥有手机和批准请求符合第二个身份验证因素的标准。|
|在 Apple Watch 中打开 Microsoft Authenticator 应用时，为何会显示我的所有帐户？|在 Apple Watch 伴侣应用中，Microsoft Authenticator 应用仅支持使用 Microsoft 个人帐户或者学校或工作帐户发送推送通知。 对于其他帐户（例如 Google 或 Facebook），必须在手机上打开 Authenticator 应用才能查看验证码。|
|为何无法在 Apple Watch 上批准或拒绝通知？|首先，请确保在 iPhone 上将 Microsoft Authenticator 应用升级到 6.0.0 或更高版本。 然后，在 Apple Watch 上打开 Microsoft Authenticator 伴侣应用，并查看其下面带有“设置”按钮的所有帐户。 必须完成设置过程才能批准这些帐户的通知。|
|为何出现错误“在 Apple Watch 上使用 Microsoft Authenticator 伴侣应用时无法与手机通信”？|如果手机与手表无法通信，可以尝试以下方法：<ol><li>强制退出 Microsoft Authenticator 手机应用，并在 iPhone 上再次打开它。</li><li>在 Apple Watch 上强制退出伴侣应用。<ol><li> 在 Watch 上打开 Microsoft Authenticator 伴侣应用</li><li>按住边侧按钮，直到“关机”屏幕出现。</li><li>松开边侧按钮，并按住 Digital Crown 强制退出活动的应用。</li></ol></li><li>关闭手机和 Watch 的蓝牙与 Wi-Fi，然后重新打开。</li><li>重启 iPhone 和 Watch。</li></ol>|
|为何适用于 Apple Watch 的 Microsoft Authenticator 伴侣应用在手表上不同步或显示？|如果该应用未显示在 Watch 上，请尝试以下方法： <ol><li>确保 Watch 运行 watchOS 4.0 或更高版本。</li><li>再次同步 Watch。</li></ol>|
|我的 Apple Watch 伴侣应用已崩溃。 是否可以向你们发送崩溃日志以便调查？ |首先，请务必选择与我们共享分析数据。 如果你是 TestFlight 用户，则已注册。 否则，可以转到“设置”>“隐私”>“分析”，并同时选择“共享 iPhone 和 Watch 分析”和“与应用开发人员共享”选项。<br><br>注册后，可以尝试重现崩溃，因此自动将崩溃日志发送给我们进行调查。 但是，如果无法重现崩溃，可以手动复制日志文件并将其发送给我们。<ol><li>在手机上打开 Watch 应用，转到“设置”>“通用”，然后单击“复制 Watch 分析数据”。</li><li>在“设置”>“隐私”>“分析”>“分析数据”下找到相应的崩溃信息，然后手动复制整个文本。</li><li>在手机上打开 Microsoft Authenticator 应用，并将复制的文本粘贴到“发送日志”页上的“与应用开发人员共享”文本框中。</li></ol>|

## <a name="next-steps"></a>后续步骤

-   如果在手机登录方面还有其他问题，请参阅[使用手机（而不是密码）登录](microsoft-authenticator-app-phone-signin-faq.md)

-   如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

-   如需有关安全信息的详细信息，请参阅[管理安全信息](security-info-manage-settings.md)

- 如果此文档没有回答问题，请告知我们。 请转到 [Microsoft Authenticator 应用论坛](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)发布问题，并从社区获取帮助，或在此页面留下评论。