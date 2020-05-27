---
title: Azure EA 门户管理
description: 本文介绍管理员可在 Azure EA 门户中完成的常见任务。
author: bandersmsft
ms.author: banders
ms.date: 05/07/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: a3551b4f09202b33cee768a69f31a6c5d5d36777
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648196"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 门户管理

本文介绍管理员可在 Azure EA 门户 (https://ea.azure.com) 中完成的常见任务。 Azure EA 门户是一个在线管理门户，可帮助客户管理其 Azure EA 服务的成本。 有关 Azure EA 门户的简介信息，请参阅 [Azure EA 门户入门](ea-portal-get-started.md)一文。

## <a name="associate-an-account-to-a-department"></a>将帐户关联到部门

企业管理员可以将现有帐户与合约下的部门关联。

### <a name="to-associate-an-account-to-a-department"></a>将帐户关联到部门

1. 以企业管理员身份登录到 Azure EA 门户。
1. 在左侧导航区域中选择“管理”。
1. 选择“部门”。
1. 将鼠标悬停在包含帐户的行上，然后选择右侧的铅笔图标。
1. 从下拉菜单中选择部门。
1. 选择“保存”。

## <a name="department-spending-quotas"></a>部门支出配额

EA 客户可为注册中的每个部门设置或更改支出配额。 支出配额的额度是为当前承诺期设置的。 在当前承诺期结束时，系统会将现有支出配额扩展到下一个承诺期，除非已更新了配额值。

部门管理员可以查看支出配额，但只有企业管理员才能更新配额。 在配额达到 50%、75%、90% 和 100% 时，企业管理员和部门管理员都会收到通知。

### <a name="enterprise-administrator-to-set-the-quota"></a>企业管理员设置配额：

 1. 打开 Azure EA 门户。
 1. 在左侧导航区域中选择“管理”。
 1. 选择“部门”选项卡。
 1. 选择“部门”。
 1. 选择“部门详细信息”部分中的铅笔符号或者选择“+ 添加部门”符号，以添加支出配额和新部门。
 1. 在“部门详细信息”下的“支出配额 $”框中，输入以注册所用货币为单位的支出配额的金额（必须大于 0）。
    - 还可以在此时编辑部门名称和成本中心。
 1. 选择“保存”。

现在，可在“部门”选项卡下的“部门列表”视图中看到部门支出配额。当前承诺期结束时，Azure EA 门户会为下一个承诺期继续使用该支出配额。

部门配额的金额与当前货币承诺无关，配额的金额和警报仅适用于第一方使用。 部门支出配额仅用于提供信息，不强制实施支出限制。

### <a name="department-administrator-to-view-the-quota"></a>部门管理员查看配额：

1. 打开 Azure EA 门户。
1. 在左侧导航区域中选择“管理”。
1. 选择“部门”选项卡，查看带有支出配额的“部门列表”视图。

如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

## <a name="enterprise-user-roles"></a>企业用户角色

Azure EA 门户可帮助你管理 Azure EA 的成本和使用情况。 Azure EA 门户中有三个主要角色：

- EA 管理员
- 部门管理员
- 帐户所有者

每个角色具有不同的访问权限和权力级别。

有关用户角色的详细信息，请参阅[企业用户角色](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles)。

## <a name="add-an-azure-ea-account"></a>添加 Azure EA 帐户

Azure EA 帐户是 Azure EA 门户中的一个组织单位。 它用于管理订阅，此外还用于报告。 若要访问和使用 Azure 服务，需要创建一个帐户，或使用已创建的帐户。

有关 Azure 帐户的详细信息，请参阅“添加帐户”。

## <a name="enterprise-devtest-offer"></a>Enterprise 开发/测试套餐

Azure 企业管理员可以使自己组织中的帐户所有者能够基于 EA 开发/测试套餐创建订阅。 为此，请在 Azure EA 门户中选中帐户所有者对应的“开发/测试”框。

选中“开发/测试”框后告知帐户所有者，这样他们就可以设置开发/测试订阅者团队需要的 EA 开发/测试订阅。

该套餐使 Visual Studio 有效订阅者能够按开发/测试费率在 Azure 上运行开发和测试工作负荷。 使用该套餐可以访问完整的开发/测试映像库（包括 Windows 8.1 和 Windows 10）。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>设置 Enterprise 开发/测试套餐：

1. 以企业管理员的身份登录。
1. 在左侧导航区域中选择“管理”。
1. 选择“帐户”选项卡。
1. 选择要启用开发/测试访问权限的帐户所在的行。
1. 选择该行右侧的铅笔符号。
1. 选中“开发/测试”复选框。
1. 选择“保存”。

通过 Azure EA 门户将用户添加为帐户所有者后，与基于 Visual Studio 订阅者的 PAYG 开发/测试套餐或月额度套餐的帐户所有者关联的任何 Azure 订阅都将被转换为 EA 开发/测试套餐。 基于与帐户所有者关联的其他套餐类型（例如 PAYG）的订阅则将被转换为 Microsoft Azure Enterprise 套餐。

开发/测试套餐目前不适用于 Azure 政府版客户。

## <a name="delete-subscription"></a>删除订阅

删除你是帐户所有者的订阅：

1. 使用与你的帐户关联的凭据登录 Azure 门户。
1. 在“中心”菜单上，选择“订阅”。
1. 在页面左上角的订阅选项卡中，选择要取消的订阅，然后选择“取消订阅”以启动“取消”选项卡。
1. 输入订阅名称并选择取消原因，然后选择“取消订阅”按钮。

只有帐户管理员可以取消订阅。

有关详细信息，请参阅[取消订阅之后会发生什么情况？](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)。

## <a name="delete-an-account"></a>删除帐户

只能为没有有效订阅的有效帐户完成帐户删除操作。

1. 在 Enterprise 门户的左侧导航区域中，选择“管理”。
1. 选择“帐户”选项卡。
1. 从“帐户”表中选择要删除的帐户。
1. 选择“帐户”行右侧的“X”符号。
1. 帐户下没有有效订阅后，在“帐户”行下选择“是”，以确认删除帐户。

## <a name="update-notification-settings"></a>更新通知设置

企业管理员会自动注册接收与其注册相关的使用情况通知。 每个企业管理员可以更改每项通知的发送间隔，或者完全禁用通知。

通知联系人显示在 Azure EA 门户中的“通知联系人”部分。 管理通知联系人可确保由组织中的适当人员接收 Azure EA 通知。

若要查看当前通知设置：

1. 在 Azure EA 门户中，导航到“管理” > “通知联系人”。 
2. 电子邮件地址 – 与接收通知的企业管理员的 Microsoft 帐户、工作或学校帐户关联的电子邮件地址。
3. 未开票余额通知频率 – 设置向每个企业管理员发送通知的频率。

若要添加联系人：

1. 选择“+添加联系人”。
2. 输入电子邮件地址，然后确认。
3. 选择“保存”。

新的通知联系人将显示在“通知联系人”部分。 若要更改通知频率，请选择通知联系人，然后选择所选行右侧的铅笔符号。 将频率设置为“每日”、“每周”、“每月”或“无”。   

可以取消“即将达到计费周期结束日期”和“即将达到禁用和取消预配日期”生命周期通知。  禁用生命周期通知会取消有关计费周期和协议结束日期的通知。

## <a name="azure-sponsorship-offer"></a>Azure 赞助套餐

Azure 赞助套餐是一个受限的 Microsoft Azure 赞助帐户。 它是 Microsoft 通过电子邮件邀请提供给有限的客户使用的套餐。 如果你符合 Microsoft Azure 赞助套餐的条件，你的帐户 ID 会收到一封邀请电子邮件。

如需详细信息，请创建一个[激活赞助支持请求](https://aka.ms/azrsponsorship)。

## <a name="conversion-to-work-or-school-account-authentication"></a>转换为工作或学校帐户身份验证

Azure Enterprise 用户的身份验证类型可以从 Microsoft 帐户（MSA 或 Live ID）转换为工作或学校帐户（使用 Azure 中的 Active Directory）。

开始时，请执行以下操作：

1. 使用所需的角色将工作或学校帐户添加到 Azure EA 门户。
1. 如果出现错误，则表明该帐户在 Active Directory 中可能无效。  Azure 使用用户主体名称 (UPN)，该名称并非始终与电子邮件地址相同。
1. 在 Azure EA 门户使用工作或学校帐户进行身份验证。

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>将订阅由使用 Microsoft 帐户转换为使用工作或学校帐户：

1. 使用拥有订阅的 Microsoft 帐户登录到管理门户。
1. 使用帐户所有权转移来转移到新帐户。
1. 现在，Microsoft 帐户应该没有任何可用订阅了，可将其删除。
1. 任何已删除的帐户仍然以停用状态显示在门户的视图中，以供查阅帐单历史记录。  勾选表示只显示有效帐户的复选框，将已删除的帐户从视图中筛选掉。

## <a name="account-subscription-ownership-faq"></a>帐户订阅所有权常见问题解答

本文档对与帐户订阅所有权相关的常见问题做出解答。

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>每个订阅可以有多少个 Azure 帐户所有者？

每个订阅只允许有一个帐户所有者。  可以使用 [portal.azure.com] (https://portal.azure.com) 页面左上角“订阅”选项卡中的“基于角色的访问”或“访问控制 (IAM)”来添加其他角色。

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>可以在多个部门下列出同一个 Azure 帐户所有者吗？

不可以，一个帐户所有者只能与一个部门关联。 该策略帮助确保在 Azure EA 门户的 EA 注册下精确地监控和分配与其所在部门关联的成本和支出。

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>能将 Azure 帐户所有者作为安全组列出吗？

不能，订阅所有者必须使用唯一的 Microsoft 帐户 (MSA) 或 Azure Active Directory (AAD) 身份验证方式。 如果要考虑组织内的连续性，可以考虑创建通用帐户，并使用 AAD 来管理订阅的访问。

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>一个用户可以拥有多个订阅吗？

Azure 帐户所有者可以创建和管理无限多的订阅。

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>我如何访问/查看组织的所有订阅？

如今必须通过策略完成此操作；也就是说，你需要要求对于创建的每个订阅，都使用基于角色的访问将帐户添加到某个订阅角色。

### <a name="where-do-i-go-to-create-a-subscription"></a>去何处创建订阅？

必须由 Azure EA 门户中 EA 注册的管理员将你的帐户添加为帐户所有者角色后，你才能创建 Enterprise Azure (EA) 套餐订阅。 然后，需要登录到 Azure EA 门户，以获得创建 EA 套餐类型的订阅的权利。 我们建议使用 EA 门户的订阅选项卡中的“+ 添加订阅”链接创建第一个 EA 订阅。  但如果帐户获得了授权，就能使用 portal.azure.com 中页面左上角的订阅选项卡更轻松地创建订阅，你可以在其中一步完成订阅的创建和重命名。

### <a name="who-can-create-a-subscription"></a>谁可以创建订阅？

要创建 Enterprise Azure 套餐类型的订阅，你必须在 [EA 门户](https://ea.azure.com)中获得帐户所有者角色。

## <a name="next-steps"></a>后续步骤

- 了解[虚拟机预留项](ea-portal-vm-reservations.md)如何帮助节省资金。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。
