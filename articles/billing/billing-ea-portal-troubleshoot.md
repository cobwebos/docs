---
title: 排查 Azure EA 门户访问问题
description: 本文介绍 Azure EA 门户中 Azure 企业协议 (EA) 可能会发生的一些常见问题。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: troubleshooting
ms.service: billing
manager: boalcsva
ms.openlocfilehash: a2214ef0dd3b7c4f706ebbb529b6c44e956f9242
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900873"
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

![显示身份验证级别的示例 ](./media/billing-ea-portal-troubleshoot/create-ea-authentication-level-types.png)

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

## <a name="no-activation-email-received"></a>没有收到激活电子邮件

Azure EA 门户中的激活电子邮件从 *waep@microsoft.com* 发送。 如果没有收到激活电子邮件，请查看垃圾邮件文件夹中是否有该电子邮件。 该电子邮件发送时的 service_subject 为“邀请查看/管理 Microsoft Azure”。  它发送到每位新添加的 EA 管理员。

如果确定自己已被设置为 EA 管理员，则不需等着接收激活电子邮件来登录 Azure EA 门户。 可以访问 https://ea.azure.com ，然后使用电子邮件地址（工作帐户、学校帐户或 Microsoft 帐户）和密码登录。

如需查看主别名，请访问 [https://account.live.com](https://account.live.com)。 然后单击“你的信息”，再单击“管理登录 Microsoft 的方式”。   按提示验证备用电子邮件地址，获取一个访问敏感信息的代码。 输入安全代码。 如果不希望设置双重身份验证，请选择“稍后设置”。 

此时会出现“管理登录 Microsoft 的方式”页，可以在其中查看你的帐户别名。  检查主别名是否是用来登录 Azure EA 门户的别名。 如果不是，可将其设置为主别名， 也可改用 Azure EA 门户的主别名。

## <a name="next-steps"></a>后续步骤

- Azure EA 门户管理员应阅读 [Azure EA portal administration](billing-ea-portal-administration.md)（Azure EA 门户管理）来了解常见的管理任务。
