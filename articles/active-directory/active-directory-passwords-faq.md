---
title: "常见问题解答：Azure AD SSPR | Microsoft Docs"
description: "有关 Azure AD 自助密码重置的常见问题解答"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 00acd4090ed981ab2b05e955e93d1c689ea1a2e6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---
# <a name="password-management-frequently-asked-questions"></a>密码管理常见问题

下面是与密码重置相关的所有方面的一些常见问题。

如果你遇到 Azure AD 和自助密码重置的一般性问题，而在本文中又找不到答案，可以在 [Azure AD 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)中请求社区帮助。 社区的成员包括工程师、产品经理、 MVP 和其他 IT 专业人员。

本“常见问题”主题分为以下几部分：

* [**有关密码重置注册的问题**](#password-reset-registration)
* [**有关密码重置的问题**](#password-reset)
* [**有关密码更改的问题**](#password-change)
* [**有关密码管理报告的问题**](#password-management-reports)
* [**有关密码写回的问题**](#password-writeback)

## <a name="password-reset-registration"></a>密码重置注册
* **问：用户是否可以注册其自己的密码重置数据？**

  > **答：**可以。只要已经启用密码重置功能并且用户已获得许可，他们就可以访问密码重置注册门户 （网址为 http://aka.ms/ssprsetup ） 来注册其身份验证信息。 用户还可以通过以下方式进行注册：转到访问面板 ( http://myapps.microsoft.com )，单击配置文件选项卡，然后单击“注册密码重置”选项。
  >
  >
* **问：我能否代表用户定义密码重置数据？**

  > **答：**可以使用 Azure AD Connect、PowerShell、[Azure 门户](https://portal.azure.com)或 Office 管理门户实现此目的。 有关详细信息，请参阅 [Azure AD 自助密码重置使用的数据](active-directory-passwords-data.md)一文。
  >
  >
* **问：我能否从本地同步安全问题数据？**

  > **答：**目前还做不到。
  >
  >
* **问：用户在注册数据时能否选择不让其他用户看到？**

  > **答：**可以。当用户使用密码重置注册门户注册数据时，数据将会保存到私有身份验证字段中，只有全局管理员和用户才能看到这些字段。
    >
    > [!NOTE]
    > 如果 **Azure 管理员帐户**注册了用户的身份验证电话号码，该号码也将填充到移动电话字段并且会公开。
    >
  >
  >
* **问：是否用户只有在注册之后才能使用密码重置功能？**

  > **答：**不是。如果为他们定义了足够的身份验证信息，用户就不必进行注册。 只要你已正确设置了目录中的相应字段中存储的数据的格式，密码重置将正常工作。
  >
  >
* **问：我是否可以代表用户同步或设置“身份验证电话”、“身份验证电子邮件”或“备用身份验证电话”字段？**

  > **答：**目前还做不到。
  >
  >
* **问：注册门户如何知道为用户显示哪些选项？**

  > **答：**密码重置注册门户只会你为用户启用的选项。 可以在目录的“配置”选项卡的“用户密码重置策略”部分下找到这些选项。 例如，这意味着如果你没有启用安全问题，则用户无法针对该选项进行注册。
  >
  >
* **问：用户何时才会被视为已注册用户？**

  > **答：**如果用户已至少注册了在 [Azure 门户](https://portal.azure.com)中设置的“所需重置方法数量”，则此用户即会被视为已注册 SSPR。
  >
  >
## <a name="password-reset"></a>密码重置
* **问：多长时间会收到来自密码重置的电子邮件、短信或接到电话？**

  > **答：**电子邮件、短信和电话应该会在 1 分钟内到达，通常在 5 秒到 20 秒之间。
    >如果在此时间范围内未收到通知：
        > * 请检查垃圾邮件文件夹。
        > * 请检查所联系的号码或电子邮件是否正确。
        > * 请检查是否对目录中的身份验证数据进行了正确的格式设置。
                >     * 示例：“+1 4255551234”或“user@contoso.com”
  >
  >
* **问：密码重置支持哪些语言？**

  > **答：**密码重置 UI、短信和语音呼叫实现了相同语言的本地化，这些语言与 Office 365 所支持的语言相同。
  >
  >
* **问：在自己目录的配置选项卡中设置组织品牌后，哪些密码重置体验部分会带有品牌信息？**

  > **答：**密码重置门户不但会显示组织徽标，而且支持配置指向某一自定义电子邮件或 URL 的“请与管理员联系”链接。 密码重置发送的任何电子邮件都将在电子邮件正文中包括你的组织的徽标、颜色、名称，以及自定义的发件人姓名。
  >
  >
* **问：如何告知用户可在何处重置密码？**

  > **答：**可以直接将用户转到 https://passwordreset.microsoftonline.com ，也可以告知他们单击任何工作或学校登录页中的“无法访问你的帐户”链接。 也可以在用户能够轻松访问的任何位置发布这些链接。
  >
  >
* **问：能否在移动设备上使用此页面？**

  > **答：**可以，此页面可以在移动设备上使用。
  >
  >
* **问：当用户重置密码时是否支持解锁本地 Active Directory 帐户？**

  > **答：**是的。如果用户重置其密码且已使用 Azure AD Connect 部署密码写回，则当该用户重置其密码时，会自动解锁其帐户。
  >
  >
* **问：如何将密码重置直接集成到用户的桌面登录体验中？**

  > **答：**如果你是 Azure AD Premium 客户，可以在无需额外付费的情况下安装 Microsoft 标识管理器并部署本地密码重置解决方案来满足此要求。
  >
  >
* **问：我能否针对不同的区域设置设定不同的安全问题？**

  > **答：**目前还做不到。
  >
  >
* **问：可为“安全问题”身份验证选项配置多少个问题？**

  > **答：**最多可以在 [Azure 门户](https://portal.azure.com)中配置 20 个自定义安全问题。
  >
  >
* **问：安全问题可设置为多长？**

  > **答：**安全问题的长度可以是 3 到 200 个字符。
  >
  >
* **问：安全问题的答案可设置为多长？**

  > **答：**答案的长度可以是 3 到 40 个字符。
  >
  >
* **问：重复的安全问题答案是否会被拒绝？**

  > **答：**是的，我们将拒绝重复的安全问题答案。
  >
  >
* **问：用户是否可以注册多个相同的安全问题？**

  > **答：**不能。一旦用户注册了某个特定问题，他们就不能再次注册该问题。
  >
  >
* **问：是否可以针对注册和重置设置安全问题下限？**

  > **答：**可以，设置一个注册限制和一个重置限制。 可以要求注册使用 3-5 个安全问题，要求重置使用 3-5 个安全问题。
  >
  >
* **问：如果用户注册的重置问题超出了所需的最大问题数量，如何在重置期间选择安全问题？**

  > **答：**N 个安全问题是从用户已注册的问题总数中随机选择的，其中 N 是“重置所需的问题数量”。 例如，如果用户注册了 5 个安全问题，但重置只需要 3 个，则在重置时将随机选择并提供 5 个问题中的 3 个。 如果用户提供的问题答案不正确，则选择过程会再次出现以防止问题攻击。
  >
  >
* **问：是否能阻止用户在短时间内多次尝试密码重置？**

  > **答：**可以。密码重置内置了安全功能来防止滥用。 用户在一小时内尝试密码重置不能超过 5 次，过多尝试将被锁定 24 小时。 用户在一小时内尝试验证某个电话号码不能超过 5 次，过多尝试将被锁定 24 小时。 用户在一小时内尝试单个身份验证方法不能超过 5 次，过多尝试将被锁定 24 小时。
  >
  >
* **问：电子邮件和短信的一次性密码的有效期有多长？**

  > **答：**进行密码重置的会话生存期为 105 分钟。 从密码重置操作开始算，用户有 105 分钟的时间来重置其密码。 此时间段到期后，电子邮件和短信的一次性密码将无效。
  >
  >

## <a name="password-change"></a>密码更改
* **问：我的用户应当到何处去更改其密码？**

  > **答：**用户可以在能够看到其个人资料图片或图标的任何位置（例如在其 [Office 365](https://portal.office.com) 或[访问面板](https://myapps.microsoft.com)体验的右上角）更改其密码。 用户可以从[访问面板个人资料页](https://account.activedirectory.windowsazure.com/r#/profile)更改其密码。 如果用户的密码已过期，还可以在 Azure AD 登录屏幕上自动要求他们更改其密码。 最后，如果用户希望更改其密码，可以直接导航到 [Azure AD 密码更改门户](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)。
  >
  >
* **问：当用户的本地密码过期时，他们是否可以在 Office 门户中收到通知？**

  > **答：**如果你根据[使用 ADFS 发送密码策略声明](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396)中的说明使用 ADFS，则目前这是可以的。 如果你使用密码哈希同步，则目前这不可以。 这是因为我们不从本地同步密码策略，因此我们无法将过期通知发布到云体验。 在任一情况下，都还可以[使用 PowerShell 向其密码即将过期的用户发送通知](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)。
  >
  >

## <a name="password-management-reports"></a>密码管理报告
* **问：需要多长时间才能在密码管理报告上看到数据？**

  > **答：**数据应在 5 到 10 分钟内显示在密码管理报告中。 某些情况下，需要一个小时才能显示。
  >
  >
* **问：如何筛选密码管理报告？**

  > **答：**单击报告顶部附近的列标签最右侧的小放大镜，即可筛选密码管理报告。 如果希望进行更丰富的筛选，可以将报告下载到 excel 并创建数据透视表。
  >
  >
* **问：密码管理报告中存储的事件数目上限是多少？**

  > **答：**密码管理报告中最多存储 75,000 个密码重置事件或密码重置注册事件，时间跨度为过去的 30 天。  我们正在努力增大此数目，以包含更多事件。
  >
  >
* **问：密码管理报告可向前追溯多久？**

  > **答：**密码管理报告可显示在过去 30 天内发生的操作。 现在，如果你需要存档此数据，可以定期下载报告并将它们保存在单独的位置。
  >
  >
* **问：密码管理报告中显示的行数是否存在上限？**

  > **答：**有。任一密码管理报告都最多只能显示 75,000 行，不论是 UI 中正在显示的行数还是正在下载的行数都存在此限制。
  >
  >
* **问：是否可以使用一个 API 来访问密码重置数据或注册报告数据？**

  > **答：**可以。请参阅以下文档，了解如何访问密码重置报告数据流。  [了解如何以编程方式访问密码重置报告事件](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)。
  >
  >

## <a name="password-writeback"></a>密码写回
* **问：密码写回如何在后台工作？**

  > **答：**请参阅[密码写回的工作原理](active-directory-passwords-writeback.md)，了解有关启用密码写回时发生的情况以及数据如何通过系统流回本地环境的说明。
  >
  >
* **问：密码写回需要多长时间才工作？是否和使用密码哈希同步一样也存在同步延迟？**

  > **答：**密码写回是即时的。 它是一种同步管道，其工作方式从根本上不同于密码哈希同步。 密码写回向用户提供关于其密码重置或更改操作成功的实时反馈。 成功的密码写回的平均时间少于 500 毫秒。
  >
  >
* **问：如果我的本地帐户被禁用，我的云帐户/访问权限会受到怎样的影响？**

  > **答：**如果你的本地 ID 被禁用，则在下一个同步间隔（默认情况下，此间隔为 30 分钟），也会通过 AAD Connect 禁用你的云 ID/访问权限。
  >
  >
* **问：如果我的本地帐户受到本地 Active Directory 密码策略的约束，当我更改密码时，SSPR 是否遵循此策略？**

  > **答：**SSPR 依赖并遵守本地 AD 密码策略，包括典型的 AD 域密码策略，以及面向给定用户的任何已定义细致密码策略。
  >
  >
* **问：密码写回适用于哪些帐户类型？**

  > **答：**密码写回适用于联合用户和密码哈希同步用户。
  >
  >
* **问：密码写回是否会强制实施我的域密码策略？**

  > **答：**是的。密码写回会强制实施密码使用期限、历史记录、复杂性、筛选器和你可能对本地域密码实施的任何其他限制。
  >
  >
* **问：密码写回是否安全？如何确保我不会受到黑客攻击？**

  > **答：**是的。密码写回具有很高的安全性。 若要阅读有关密码写回服务实施的四个安全层的详细信息，请查看“密码写回的工作原理”中的[密码写回安全模型](active-directory-passwords-writeback.md#password-writeback-security-model)部分。
  >
  >

## <a name="next-steps"></a>后续步骤

以下链接提供有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能、在何时在何处访问
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**密码写回**](active-directory-passwords-writeback.md) - 如何对本地目录使用密码写回
* [**深入技术探究**](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题

