---
title: 排查 Azure EA 门户访问问题
description: 本文介绍 Azure EA 门户中 Azure 企业协议 (EA) 可能会发生的一些常见问题。
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: fdcce868cb53c0fdd6a51f2b351491e1ee998d61
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691184"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>排查 Azure EA 门户访问问题

本文介绍 Azure 企业协议 (EA) 可能会发生的一些常见问题。 Azure EA 门户用于管理企业协议用户和成本。 你可能会在配置或更新 Azure EA 门户访问权限时遇到这些问题。

## <a name="issues-adding-an-admin-to-an-enrollment"></a>将管理员添加到许可登记表时的问题

企业许可登记表有不同类型的身份验证级别。 如果身份验证级别应用不正确，则当你尝试登录到 Azure EA 门户时，可能会出现问题。

可以使用 Azure EA 门户为不同身份验证级别的用户授予访问权限。 企业管理员可以根据其组织的安全要求更新身份验证级别。

### <a name="authentication-level-types"></a>身份验证级别类型

- 仅 Microsoft 帐户 - 适用于需要通过 Microsoft 帐户来使用、创建和管理用户的组织。
- 工作或学校帐户 - 适用于已设置联合到云的 Active Directory 且所有帐户都在单个租户上的组织。
- 跨租户工作或学校帐户 - 适用于已设置联合到云的 Active Directory 且会将帐户置于多个租户中的组织。
- 混合帐户 - 允许通过 Microsoft 帐户和/或通过工作或学校帐户添加用户。

第一个添加到许可登记表的工作或学校帐户决定了域是 _default_ 域还是 _master_ 域。 若要添加其他租户中的工作或学校帐户，必须将“许可登记表”下的身份验证级别更改为跨租户身份验证。

若要更新“身份验证级别”，请执行以下操作：

1. 以企业管理员身份登录到 Azure EA 门户。
2. 单击左侧导航面板上的“管理”  。
3. 单击“许可登记表”选项卡  。
4. 在“许可登记表详细信息”  下，选择“身份验证级别”。 
5. 单击铅笔符号。
6. 单击“ **保存**”。

![显示身份验证级别的示例 ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft 帐户必须有一个在 [https://signup.live.com](https://signup.live.com/) 创建的关联 ID。

工作或学校帐户适用于已设置具有联合功能的 Active Directory 且所有帐户都在单个租户上的组织。 可以添加通过工作或学校帐户进行联合用户身份验证的用户，前提是公司的内部 Active Directory 已启用联合身份验证功能。

如果组织不使用 Active Directory 联合身份验证，则不能使用工作或学校电子邮件地址， 而只能注册新的电子邮件地址，或者在创建新的电子邮件地址后，将其注册为 Microsoft 帐户。

## <a name="unable-to-access-the-azure-ea-portal"></a>无法访问 Azure EA 门户

如果在尝试登录到 Azure EA 门户时出现错误消息，请使用以下故障排除步骤：

- 确保使用正确的 Azure EA 门户 URL，即 https://ea.azure.com 。
- 确定你对 Azure EA 门户的访问权限是作为工作或学校帐户添加的，还是作为 Microsoft 帐户添加的。
  - 如果使用工作帐户，请输入工作电子邮件和工作密码。 工作密码由组织提供。 如果密码存在问题，可以联系 IT 部门，了解如何重置密码。
  - 如果使用 Microsoft 帐户，请输入 Microsoft 帐户电子邮件地址和密码。 如果忘记了 Microsoft 帐户密码，可以在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 重置它。
- 使用 InPrivate 或 Incognito 浏览器会话登录，这样系统就不会保留以前的或现有的会话的 Cookie 或缓存信息。 清除浏览器的缓存，使用 InPrivate 或 Incognito 窗口打开 https://ea.azure.com 。
- 如果在使用 Microsoft 帐户时出现“用户无效”错误，则可能是因为你有多个 Microsoft 帐户，  而你尝试登录时使用的帐户不是主电子邮件地址。
另外，出现“用户无效”错误还可能是因为在将用户添加到许可登记表时使用了错误的帐户类型。  例如，使用了工作或学校帐户而不是 Microsoft 帐户。 在此示例中，可以让另一位 EA 管理员添加正确的帐户，或者需要联系[支持部门](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c)。
  - 如需查看主别名，请访问 [https://account.live.com](https://account.live.com)。 然后单击“你的信息”，再单击“管理登录 Microsoft 的方式”   。 按提示验证备用电子邮件地址，获取一个访问敏感信息的代码。 输入安全代码。 如果不希望设置双重身份验证，请选择“稍后设置”。 
  - 此时会出现“管理登录 Microsoft 的方式”页，可在其中查看你的帐户别名  。 检查主别名是否是用来登录 Azure EA 门户的别名。 如果不是，可将其设置为主别名。 也可改用 Azure EA 门户的主别名。

## <a name="no-activation-email-received"></a>没有收到激活电子邮件

Azure EA 门户中的激活电子邮件从 *waep@microsoft.com* 发送。 如果没有收到激活电子邮件，请查看垃圾邮件文件夹中是否有该电子邮件。 该电子邮件发送时的 service_subject 为“邀请查看/管理 Microsoft Azure”。  它发送到每位新添加的 EA 管理员。

如果确定自己已被设置为 EA 管理员，则不需等着接收激活电子邮件来登录 Azure EA 门户。 可以访问 https://ea.azure.com ，然后使用电子邮件地址（工作帐户、学校帐户或 Microsoft 帐户）和密码登录。

## <a name="azure-ea-activation-faq"></a>Azure EA 激活常见问题解答

本文的此部分概述关于 Azure EA 激活的常见问题的解决方案。

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>我想向自己的合约添加新的 EA 管理员

现有的企业管理员可以添加新的企业管理员。 如果你是 EA 管理员，请登录 EA 门户 > 单击“管理”> 在右上角单击“+ 添加管理员”，以添加新的 EA 管理员   。 请确保拥有用户的电子邮件地址和首选登录方法（例如工作/学校身份验证或 Microsoft Live ID），以便添加用户。

如果你不是 EA 管理员，请联系公司的 EA 管理员，请求他们将你添加到合约中。 在他们将你添加到合约后，你会收到一封激活电子邮件。

但是，如果 EA 管理员无法提供帮助，我们可以代表他们进行添加，前提是你能向我们提供：
- 合约编号
- 要添加的电子邮件地址和身份验证类型（工作/学校/MS）
- 来自 EA 管理员的批准电子邮件

如果拥有所有必备信息，请在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交请求。

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>我想更新合约上的第一个 EA 管理员

可以通过更新门户上的通知联系人和联机管理员的方式，在批量许可服务中心中更新第一个 EA 管理员。 EA 门户大约需要 24 小时才能完成此更新。 更新后，新的 EA 管理员将收到激活电子邮件。

如果你没有 VLSC 门户的访问权限，或者如果你的初始 EA 管理员无法再管理合约并且无权访问 EA 门户，请在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交请求，请求更新并提供以下信息：
- 注册编号
- 要添加的电子邮件地址和身份验证类型（工作/学校/MS）
- 更改初始 EA 管理员的原因
- 来自初始 EA 管理员的批准电子邮件

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>我当前的 EA 管理员已不在公司任职

一个 EA 合约可以有多个 EA 管理员，你可以与其他 EA 管理员联系，请他们添加新的 EA 管理员/帐户所有者/部门管理员。 但是，如果你不知道公司内 EA 管理员的身份或合约上没有其他可用的 EA 管理员，请与我们联系并提供以下信息：
- 注册编号
- 要添加的电子邮件地址和身份验证类型（工作/学校/MS）
- 提供信息表明当前的 EA 管理员已不在公司任职

请注意，如果合约中有其他 EA 管理员，我们将联系这些 EA 管理员，请其批准合约在管理方面的更改。

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>我的合约显示为挂起状态。 如何激活我的合约？

如果初始 EA 管理员之前未登录过合约，则合约将处于挂起状态。 如果你是 EA 管理员，请登录 Azure EA 门户。 在含有所有合约编号的登陆页上，你可能看不到处于挂起状态的合约。 请取消选中 EA 门户右上角的“可用”框，这样就能显示挂起的合约。 请单击合约访问信息，在合约的“管理”页上，状态将从“挂起”更新为“可用”。

### <a name="why-is-my-account-stuck-in-pending-status"></a>为什么我的帐户始终为挂起状态？

首次向合约添加新的帐户所有者 (AO) 时，这些帐户所有者的状态将始终显示为“挂起”。 AO 收到激活欢迎电子邮件时，可以登录以激活其帐户。 登录会将帐户的状态从“挂起”更新为“可用”。

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>我在登录到 Azure EA 门户时收到错误

可能有几个原因导致登录期间 Azure EA 门户上显示错误消息，请按以下故障排除步骤操作：

 1. 请确保使用正确的 EA 门户 URL：[https://ea.azure.com](https://ea.azure.com)。
 1. 确定添加你对 Azure EA 门户的访问权限的方式（是使用的工作或学校帐户，还是 Microsoft Live ID）。 如果使用的是工作帐户，请输入工作电子邮件地址和工作密码。 如果使用的是 Microsoft Live ID，请输入 Live ID 电子邮件地址和 Microsoft Live ID 密码。 如果忘记了 Microsoft Live ID 密码，请在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 重置。
 1. 建议使用专用浏览器进行登录，以便其不保留以前/现有的会话中的 Cookie 或缓存。 清除缓存，并使用隐私浏览模式/无痕窗口打开 [https://ea.azure.com](https://ea.azure.com)。
 1. 如果你在使用 Microsoft 帐户时出现“用户无效”错误，可能是因为你拥有多个 Microsoft 帐户，而你尝试用于登录的帐户不是主别名。 如需查看主别名，请前往 account.live.com：
    - 转到“你的信息”>“管理登录电子邮件帐户或电话号码”。
    - 按照屏幕上的提示验证备用电子邮件地址，获取一个用于访问敏感信息的代码。
    - 输入安全代码。
    - 如果希望以后设置双重身份验证，请选择“稍后设置”。
    - 你将在“管理帐户别名”页看到你拥有的帐户别名。 仔细检查主别名是否是用来登录 Azure EA 门户的别名。 如果不是，可以将其设置为主别名，也可以改为使用 EA 门户的主别名。

如果上述故障排除步骤失败，请在 [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) 提交请求并提供信息，例如以下信息：
- 使用的浏览器和版本。
- 错误消息的屏幕截图。
- 显示错误消息的页面的 URL。  
- 发生错误时的日期、时间和时区。
- 此外，日志文件会有所帮助。 下面是使用以下信息捕获网络痕迹的步骤：
  1. 打开 Internet Explorer。
  1. 按 F12 在 IE 底部打开一个框。
  1. 选择“网络”选项卡  。
  1. 单击“开始捕获”  。
  1. 执行导致错误的操作。
  1. 出现错误后，单击“停止捕获”  。
  1. 保存该文件，并在支持请求中包含该信息。
  1. 确保在支持请求中提供合约编号和电子邮件地址。

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>“工作/学校帐户”和“Microsoft 帐户”有什么不同？

**Microsoft 帐户**：已与 [https://signup.live.com](https://signup.live.com) 上的 Live ID 关联的帐户。

**工作/学校帐户：** 仅适用于已设置与云联合的 Active Directory 且所有帐户都在一个租户上的公司。 如果公司的内部 Active Directory 与云联合，则可以使用工作或学校帐户授权类型添加用户。

  从 2016 年 9 月起，Microsoft 不再允许将工作或学校电子邮件地址注册为 Microsoft 帐户。 有关更多详细信息，请参阅以下材料： [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/)。

  如果你的组织未与云联合，你就无法使用你的工作或学校电子邮件地址。 请注册或创建一个新的电子邮件地址，将其注册为 Microsoft 帐户。

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>我忘记了 Azure EA 门户的密码

如果忘记了 Microsoft Live ID 密码，请在 [https://account.live.com/password/reset](https://account.live.com/password/reset) 重置。

如果你忘记了工作密码，请联系贵公司的 IT 管理员。

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>我拥有一个有效的工作或学校帐户，但无法将其添加到 EA 门户

如果拥有的工作或学校帐户位于另一个租户下，请将合约详细信息页上的授权级别更改为“跨租户的工作或学校帐户”，这样就能添加帐户了。

## <a name="next-steps"></a>后续步骤

- Azure EA 门户管理员应阅读 [Azure EA portal administration](ea-portal-administration.md)（Azure EA 门户管理）来了解常见的管理任务。
