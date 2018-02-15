---
title: "自助密码重置常见问题解答 - Azure Active Directory"
description: "有关 Azure AD 自助密码重置的常见问题解答"
services: active-directory
keywords: "Active Directory 密码管理, 密码管理, Azure AD 自助密码重置"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: seohack1
ms.openlocfilehash: afa609d7b47f3c743f5d3bfd4011e09c762ccbd1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="password-management-frequently-asked-questions"></a>密码管理常见问题

下面是与密码重置相关的所有方面的一些常见问题解答 (FAQ)。

如果遇到 Azure Active Directory (Azure AD) 和自助密码重置 (SSPR) 的一般性问题，而在本文中又找不到答案，可以在 [Azure AD 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)中请求社区帮助。 社区的成员包括工程师、产品经理、MVP 和其他 IT 专业人员。

本“常见问题”主题分为以下几部分：

* [有关密码重置注册的问题](#password-reset-registration)
* [有关密码重置的问题](#password-reset)
* [有关密码更改的问题](#password-change)
* [有关密码管理报告的问题](#password-management-reports)
* [有关密码写回的问题](#password-writeback)

## <a name="password-reset-registration"></a>密码重置注册

* **问：用户是否可以注册其自己的密码重置数据？**

  > **答：**可以。 只要已经启用密码重置功能并且用户已获得许可，他们就可以访问密码重置注册门户 (http://aka.ms/ssprsetup) 来注册其身份验证信息。 用户也可通过访问面板来注册 (http://myapps.microsoft.com)。 若要通过访问面板来注册，需选择个人资料图片，然后选择“个人资料”，再选择“注册密码重置”选项。
  >
  >
* **问：如果为某个组启用密码重置，然后决定为每个人启用它，我的用户是否需要重新注册？**

  > **答：**否。 已填充身份验证数据的用户不需要重新注册。
  >
  >
* **问：我能否代表用户定义密码重置数据？**

  > **答：**能。你可以使用 Azure AD Connect、PowerShell、[Azure 门户](https://portal.azure.com)或 Office 365 管理中心实现此目的。 有关详细信息，请参阅 [Azure AD 自助密码重置使用的数据](active-directory-passwords-data.md)。
  >
  >
* **问：我能否从本地同步安全问题数据？**

  > **答：**否。目前还做不到。
  >
  >
* **问：用户在注册数据时能否选择不让其他用户看到？**

  > **答：**可以。 当用户使用密码重置注册门户注册数据时，数据会保存到私有身份验证字段中，只有全局管理员和用户才能看到这些字段。
  >
  >
* **问：是否用户只有在注册之后才能使用密码重置功能？**

  > **答：**否。 如果为他们定义了足够的身份验证信息，用户就不必进行注册。 只要已正确设置了目录中的相应字段中存储的数据的格式，密码重置将正常工作。
  >
  >
* **问：我是否可以代表用户同步或设置“身份验证电话”、“身份验证电子邮件”或“备用身份验证电话”字段？**

  > **答：**在 [SSPR 数据要求](active-directory-passwords-data.md)一文中定义了能够由全局管理员设置的字段。
  >
  >
* **问：注册门户如何确定为用户显示哪些选项？**

  > **答：**密码重置注册门户只会显示为用户启用的选项。 可以在目录的“配置”选项卡的“用户密码重置策略”部分找到这些选项。例如，如果没有启用安全问题，则用户无法针对该选项进行注册。
  >
  >
* **问：用户何时才会被视为已注册用户？**

  > **答：**如果用户已至少注册了在 [Azure 门户](https://portal.azure.com)中设置的“重置一个密码所需的方法数”，则此用户即会被视为已注册 SSPR。
  >
  >

## <a name="password-reset"></a>密码重置

* **问：是否会阻止用户在短时间内为了重置密码而进行多次尝试？**

  > **答：**是的。密码重置内置了安全功能来防止滥用。 
  >
  > 用户在 24 小时内尝试密码重置不能超过五次，否则会被锁定 24 小时。 
  >
  > 在被锁定 24 小时前的一个小时内，用户可以尝试验证电话号码、发送 SMS 或验证安全性问题并仅回答五次。 
  >
  > 在被锁定 24 小时前，用户可以在 10 分钟内最多发送 10 次电子邮件。
  >
  > 一旦用户重置密码则会重置计数器。
  >
  >
* **问：多长时间会收到来自密码重置的电子邮件、短信或接到电话？**

  > **答：**电子邮件、短信和电话应在一分钟内收到或接到。 正常情况下为 5 到 20 秒。
    >如果在此时间范围内未收到通知：
        > * 请检查垃圾邮件文件夹。
        > * 请检查所联系的号码或电子邮件是否正确。
        > * 请检查是否对目录中的身份验证数据进行了正确的格式设置，例如，+1 4255551234 或 *user@contoso.com*。 
  >
  >
* **问：密码重置支持哪些语言？**

  > **答：**密码重置 UI、短信和语音呼叫实现了相同语言的本地化，这些语言与 Office 365 所支持的语言相同。
  >
  >
* **问：在自己目录的配置选项卡中设置组织品牌项目后，哪些密码重置体验部分会带有品牌信息？**

  > **答：**密码重置门户不但会显示组织徽标，而且支持配置指向某一自定义电子邮件或 URL 的“请与管理员联系”链接。 密码重置发送的任何电子邮件都会在电子邮件正文中包括组织的徽标、颜色、名称，是根据该特定名称的设置自定义的。
  >
  >
* **问：如何告知用户可在何处重置密码？**

  > **答：**请尝试 [SSPR 部署](active-directory-passwords-best-practices.md#email-based-rollout)一文中的一些建议。
  >
  >
* **问：能否在移动设备上使用此页面？**

  > **答：**可以，此页面可以在移动设备上使用。
  >
  >
* **问：当用户重置密码时是否支持解锁本地 Active Directory 帐户？**

  > **答：**可以。 如果用户重置其密码且已通过 Azure AD Connect 部署密码写回，则当该用户重置其密码时，会自动解锁其帐户。
  >
  >
* **问：如何将密码重置直接集成到用户的桌面登录体验中？**

  > **答：**如果是 Azure AD Premium 客户，可以在无需额外付费的情况下安装 Microsoft 标识管理器并部署本地密码重置解决方案。
  >
  >
* **问：我能否针对不同的区域设置设定不同的安全问题？**

  > **答：**否。目前还做不到。
  >
  >
* **问：可为“安全问题”身份验证选项配置多少个问题？**

  > **答：**最多可以在 [Azure 门户](https://portal.azure.com)中配置 20 个自定义安全问题。
  >
  >
* **问：安全问题可设置为多长？**

  > **答：**安全问题可以是 3 到 200 个字符长。
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

  > **答：**否。 一旦用户注册了某个特定问题，他们就不能再次注册该问题。
  >
  >
* **问：是否可以针对注册和重置设置安全问题下限？**

  > **答：**可以，设置一个注册限制和一个重置限制。 注册可能需要三到五个安全问题，重置也可能需要三到五个问题。
  >
  >
* **问：我配置了策略，要求用户使用安全问题进行重置，但 Azure 管理员似乎进行了不同的配置。**

  > **答：**这是预期的行为。 Microsoft 为任意 Azure 管理员角色强制实施默认强双门密码重置策略。 这会阻止管理员使用安全问题。 有关此策略的详细信息，请参阅 [Azure Active Directory 中的密码策略和限制](active-directory-passwords-policy.md#administrator-password-policy-differences)一文。
  >
  >
* **问：如果用户注册的重置问题超出了所需的最大问题数量，如何在重置期间选择安全问题？**

  > **答：**N 个安全问题是从用户已注册的所有问题中随机选择的，其中 N 是针对“重置所需的问题数”选项设置的一个数。 例如，如果用户注册了五个安全问题，但重置密码只需三个，则会从五个问题中随机选择三个在重置时提出。 为了防止 问题攻击，如果用户提供的问题答案不正确，则选择过程会从头开始。
  >
  >
* **问：电子邮件和短信的一次性密码的有效期有多长？**

  > **答：**进行密码重置的会话生存期为 15 分钟。 从密码重置操作开始算，用户有 15 分钟的时间来重置其密码。 此时间段到期后，电子邮件和短信的一次性密码将无效。
  >
  >
* 问：可否阻止用户重置其密码？

  > **答：**可以。如果使用组来启用 SSPR，则可将用户从允许用户重置其密码的组中删除。
  >
  >

## <a name="password-change"></a>密码更改

* **问：我的用户应当到何处去更改其密码？**

  > **答：**用户可以在能够看到其个人资料图片或图标的任何位置（例如在其 [Office 365](https://portal.office.com) 门户或[访问面板](https://myapps.microsoft.com)体验的右上角）更改其密码。 用户可以从[访问面板个人资料页](https://account.activedirectory.windowsazure.com/r#/profile)更改其密码。 如果用户的密码已过期，还可以在 Azure AD 登录页上自动要求他们更改其密码。 最后，如果用户希望更改其密码，可以直接浏览到 [Azure AD 密码更改门户](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)。
  >
  >
* **问：当用户的本地密码过期时，他们是否可以在 Office 门户中收到通知？**

  > **答：**可以。如果使用 Active Directory 联合身份验证服务 (AD FS)，这在目前是可以的。 如果使用 AD FS，请按 [Sending password policy claims with AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396)（使用 AD FS 发送密码策略声明）一文中的说明操作。 如果使用密码哈希同步，这在目前是不可以的。 我们不从本地目录同步密码策略，因此无法将过期通知发布到云体验。 在任一情况下，都还可以[通过 PowerShell 向其密码即将过期的用户发送通知](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx)。
  >
  >
* 问：可否阻止用户更改其密码？

  > **答：**对于仅限云的用户来说，不能阻止密码更改。 对于本地用户，可将“用户不能更改密码”选项设置为选定。 选定的用户不能更改其密码。
  >
  >

## <a name="password-management-reports"></a>密码管理报告

* **问：需要多长时间才能在密码管理报告上看到数据？**

  > **答：**数据应在 5 到 10 分钟内显示在密码管理报告中。 某些情况下，需要一个小时才能显示。
  >
  >
* **问：如何筛选密码管理报告？**

  > **答：**选择报告顶部附近列标签最右侧的小放大镜即可筛选密码管理报告。 如果希望进行更丰富的筛选，可以将报告下载到 Excel 并创建数据透视表。
  >
  >
* **问：密码管理报告中存储的事件数目上限是多少？**

  > **答：**密码管理报告中最多存储 75,000 个密码重置事件或密码重置注册事件，时间跨度为过去的 30 天。 我们正在努力增大此数目，以包含更多事件。
  >
  >
* **问：密码管理报告可向前追溯多久？**

  > **答：**密码管理报告可显示在过去 30 天内发生的操作。 现在，如果需要存档此数据，可以定期下载报告并将它们保存在单独的位置。
  >
  >
* **问：密码管理报告中显示的行数是否存在上限？**

  > **答：**可以。 任一密码管理报告都最多只能显示 75,000 行，不论是 UI 中正在显示的行数还是正在下载的行数都存在此限制。
  >
  >
* **问：是否可以使用一个 API 来访问密码重置数据或注册报告数据？**

  > **答：**可以。 若要了解如何才能访问密码重置报告数据流，请参阅[了解如何以编程方式访问密码重置报告事件](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)。
  >
  >

## <a name="password-writeback"></a>密码写回

* **问：密码写回如何在后台工作？**

  > **答：**请参阅[密码写回的工作原理](active-directory-passwords-writeback.md)一文，了解有关启用密码写回时发生的情况以及数据如何通过系统流回本地环境的说明。
  >
  >
* **问：密码写回需要多长时间才工作？是否和使用密码哈希同步一样也存在同步延迟？**

  > **答：**密码写回是即时的。 它是一种同步管道，其工作方式从根本上不同于密码哈希同步。 密码写回向用户提供关于其密码重置或更改操作成功的实时反馈。 成功的密码写回的平均时间少于 500 毫秒。
  >
  >
* **问：如果我的本地帐户被禁用，我的云帐户和访问权限会受到怎样的影响？**

  > **答：**如果本地 ID 被禁用，则在下一个同步间隔，也会通过 Azure AD Connect 禁用云 ID 和访问权限。 默认情况下，此同步是每隔 30 分钟进行一次。
  >
  >
* **问：如果我的本地帐户受到本地 Active Directory 密码策略的约束，当我更改密码时，SSPR 是否遵循此策略？**

  > **答：**是。SSPR 基于并遵守本地 Active Directory 密码策略。 此策略包括典型的 Active Directory 域密码策略，以及任何已定义并细化的针对用户的密码策略。
  >
  >
* **问：密码写回适用于哪些帐户类型？**

  > **答：**密码写回适用于联合用户和密码哈希同步用户。
  >
  >
* **问：密码写回是否会强制实施我的域密码策略？**

  > **答：**可以。 密码写回会强制实施密码使用期限、历史记录、复杂性、筛选器以及可能对本地域密码实施的任何其他限制。
  >
  >
* **问：密码写回是否安全？如何确保我不会受到黑客攻击？**

  > **答：**是的。密码写回具有很高的安全性。 若要阅读有关密码写回服务实施的四个安全层的详细信息，请查看[密码写回概述](active-directory-passwords-writeback.md)一文中的[密码写回安全模型](active-directory-passwords-writeback.md#password-writeback-security-model)部分。
  >
  >

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](active-directory-passwords-reset-register.md)
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](active-directory-passwords-data.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
