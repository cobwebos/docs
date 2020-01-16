---
title: Azure EA 门户管理
description: 本文介绍管理员可在 Azure EA 门户中完成的常见任务。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 469bd66a6074db34d1efb6f82fda229400239058
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992304"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 门户管理

本文介绍管理员可在 Azure EA 门户 (https://ea.azure.com) 中完成的常见任务。 Azure EA 门户是一个在线管理门户，可帮助客户管理其 Azure EA 服务的成本。 有关 Azure EA 门户的简介信息，请参阅 [Azure EA 门户入门](ea-portal-get-started.md)一文。

## <a name="add-a-new-enterprise-administrator"></a>添加新的企业管理员

企业管理员拥有管理 Azure EA 注册的大部分特权。 设置 EA 协议时，会创建初始的 Azure EA 管理员。 但是，你随时可以添加或删除新管理员。 新管理员只能由现有的管理员添加。 有关添加其他企业管理员的详细信息，请参阅[创建其他企业管理员](ea-portal-get-started.md#create-another-enterprise-admin)。有关计费配置文件角色和任务的详细信息，请参阅[计费配置文件角色和任务](understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-user-state-from-pending-to-active"></a>将用户状态从“挂起”更新为“活动”

首次将新的帐户所有者 (AO) 添加到 Azure EA 注册时，其状态将显示为“挂起”。 当新帐户所有者收到激活欢迎电子邮件时，他们可以登录以激活其帐户。 激活帐户后，其帐户状态将从“挂起”更新为“可用”。 帐户所有者需要阅读“警告”消息，然后单击“继续”。 系统可能会提示新用户输入其名字和姓氏来创建商务帐户。 如果出现此提示，他们必须添加所需的信息以继续，然后其帐户将会激活。

## <a name="add-a-department-admin"></a>添加部门管理员

Azure EA 管理员创建部门后，Azure 企业管理员可以添加部门管理员并将每个管理员关联到某个部门。 部门管理员可以创建新帐户。 需要添加新帐户才能创建 Azure EA 订阅。

有关添加部门管理员的详细信息，请参阅[创建 Azure EA 部门管理员](ea-portal-get-started.md#add-a-department-admin)。

## <a name="associate-an-account-to-a-department"></a>将帐户关联到部门

企业管理员可以将现有帐户与合约下的部门关联。

### <a name="to-associate-an-account-to-a-department"></a>将帐户关联到部门

1. 以企业管理员身份登录到 Azure EA 门户。
1. 在左侧导航区域中单击“管理”。
1. 单击“部门”。
1. 将鼠标悬停在含有所需帐户的行上，然后单击右侧的铅笔图标。
1. 从下拉菜单中选择所需的部门。
1. 单击“ **保存**”。

## <a name="department-spending-quotas"></a>部门支出配额

EA 客户能够为合约中的每个部门设置或更改支出配额。 支出配额的额度是为当前承诺期设置的。 在当前承诺期结束时，系统会将现有支出配额扩展到下一个承诺期，除非已更新了配额值。

部门管理员可以查看支出配额，但只有企业管理员才能更新配额。 在配额达到 50%、75%、90% 和 100% 时，企业管理员和部门管理员都会收到通知。

### <a name="enterprise-administrator-to-set-the-quota"></a>企业管理员设置配额：

 1. 打开 Azure EA 门户。
 1. 在左侧导航区域中单击“管理”。
 1. 单击“部门”选项卡。
 1. 单击所需的部门。
 1. 单击“部门详细信息”部分中的铅笔图标或者单击“+ 添加部门”按键，即可添加支出配额和新部门。
 1. 在“部门详细信息”下的“支出配额 $”框中，输入以合约所用货币为单位的支出配额的金额（必须大于 0）。
    - 还可以在此时编辑部门名称和成本中心。
 1. 点击“保存”。

"部门" 选项卡下的 "部门列表" 视图中将显示部门支出配额。在当前承诺结束时，Azure EA 门户将为下一个承诺条款维护支出配额。

部门配额的金额与当前货币承诺无关，配额的金额和警报仅适用于第一方使用。 部门支出配额仅用于提供信息，不强制实施支出限制。

### <a name="department-administrator-to-view-the-quota"></a>部门管理员查看配额：

1. 打开 Azure EA 门户。
1. 在左侧导航区域中单击“管理”。
1. 单击“部门”选项卡，查看带有支出配额的“部门列表”视图。

如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

## <a name="enterprise-user-roles"></a>企业用户角色

Azure EA 门户可帮助你管理 Azure EA 的成本和使用情况。 Azure EA 门户中有三个主要角色：

- EA 管理员
- 部门管理员
- 帐户所有者

每个角色具有不同的访问权限和权力级别。

有关用户角色的详细信息，请参阅[企业用户角色](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles)。

## <a name="add-an-azure-ea-account"></a>添加 Azure EA 帐户

Azure EA 帐户是 Azure EA 门户中的一个组织单位，用于管理订阅和报告。 若要访问和使用 Azure 服务，需要创建一个帐户，或使用已创建的帐户。

有关 Azure 帐户的详细信息，请参阅“添加帐户”。

## <a name="enterprise-devtest-offer"></a>Enterprise 开发/测试套餐

作为 Azure 企业管理员，你现在可以使自己组织内的帐户所有者能够基于 EA 开发/测试套餐创建订阅。 方式是在 Azure EA 门户中为帐户所有者勾选“开发/测试”框。

选中“开发/测试”框后告知帐户所有者，这样他们就可以设置开发/测试订阅者团队需要的 EA 开发/测试订阅。

此套餐使有效的 Visual Studio 订阅者能够以开发/测试优惠费率在 Azure 上运行开发和测试工作负荷，并可访问完整的开发/测试映像库（包括 Windows 8.1 和 Windows 10）。

### <a name="to-set-up-the-enterprise-devtest-offer"></a>设置 Enterprise 开发/测试套餐：

1. 以企业管理员的身份登录。
1. 在左侧导航区域中单击“管理”。
1. 单击“帐户”选项卡。
1. 单击要启用开发/测试访问权限的帐户所在的行。
1. 单击该行右侧的铅笔图标。
1. 选中“开发/测试”复选框。
1. 点击“保存”。

通过 Azure EA 门户将用户添加为帐户所有者后，与基于 Visual Studio 订阅者的 PAYG 开发/测试套餐或月额度套餐的帐户所有者关联的任何 Azure 订阅都将被转换为 EA 开发/测试套餐。 基于与帐户所有者关联的其他套餐类型（例如 PAYG）的订阅则将被转换为 Microsoft Azure Enterprise 套餐。

开发/测试套餐目前不适用于“Azure 政府”服务的客户。

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>将企业帐户转移到新注册

帐户传输将帐户所有者从一个注册移到另一个注册。 帐户所有者下的所有相关订阅都将移动到目标注册。 当你有多个活动的注册，并且只想要移动选定的帐户所有者时，将执行此项。

将企业帐户转移到新注册时，请记住以下几点：

- 仅转移请求中指定的帐户。 如果选择所有帐户，会转移所有这些帐户。
- 源注册的状态将保留为活动或已扩展。 在该注册过期之前，可以继续使用它。

### <a name="effective-transfer-date"></a>转移生效日期

你可以将帐户传输 backdate 到最早的时间，作为目标注册的开始日期或帐户的开始日期（以晚于开始日期为准）。 转移帐户后，帐户中在转移生效日期之前的所有使用情况信息将保留在要从中转移的注册中。 传输日期之后的使用情况信息将移动到目标注册。

### <a name="prerequisites"></a>必备组件

请求帐户转移时，请提供以下信息：

- 要传输的帐户的目标注册、帐户名和帐户所有者电子邮件的编号
- 对于源注册，请提供要转移的注册编号和帐户
- 对于帐户传输生效日期，该日期可以追溯为 "目标注册" 的开始日期，或 "帐户开始日期"，以获得更晚的开始日期

转移帐户之前要注意的其他要点：

- 目标和源注册需要经过 EA 管理员的批准
- 如果帐户转移不符合要求，请考虑进行注册转移。
- 帐户转移与特定帐户相关的所有服务和订阅。
- 传输完成后，在源注册下，传输的帐户将显示为非活动状态，并在目标注册下显示为活动状态。
- 帐户显示与源注册的有效转移日期相对应的终止日期，并将其显示为目标注册的起始日期。
- 在有效转移日期之前该帐户发生的任何使用情况均保留在源注册中。


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>将企业注册转移到新注册

在以下情况时考虑注册传输：

- 当前注册的承诺期限已结束。
- 注册处于过期/扩展状态，并协商了新协议。
- 如果你有多个注册，并且想要合并单个注册下的所有帐户和计费。

请求将整个企业注册转移到某个注册时，将发生以下操作：

- 所有 Azure 服务、订阅、帐户、部门和整个注册结构（包括所有 EA 部门管理员）都将传输到新的目标注册。
- 注册状态将设置为“已转移”。 转移的注册仅可用于历史使用情况报告。
- 无法将角色或订阅添加到处于已转移状态的注册。 已转移状态可防止注册进一步产生使用费。
- 协议中任何剩余的货币承诺余额将会丢失，包括将来的条款。
-   如果作为转移源的合约中购买了 RI，系统仍会将 RI 购买费保留在源合约中，但会转移所有 RI 权益以供在新合约中使用。
-   不会向新合约转移旧合约中已产生的市场一次性购买费和任何固定费用。 将传输基于使用量的市场费用。
-   在迟期合约转移后，购买所产生的任何一次性费用都保留在源合约中。

### <a name="effective-transfer-date"></a>转移生效日期

有效的传输日期可以在目标注册的开始日期之前或之后。

源注册的使用费根据货币承诺或超额计收。 在转移生效日期之后发生的使用费将转移到新注册并相应地收费。

目前支持追溯传输，作为目标注册的开始日期。 提供所选传输日期不影响已颁发的超额发票的使用情况。

### <a name="prerequisites"></a>必备组件

请求注册转移时，请提供以下信息：

- 对于源注册，为注册号。
- 对于目标注册，请提供要转移到的注册编号。
- 注册转移生效日期可以是目标注册开始日期当日或之后的某个日期 所选日期不能影响任何已开具的超额发票的使用。

转移注册之前要注意的其他要点：

- 需要从目标和源注册 EA 管理员批准。
- 如果注册转移不符合要求，请考虑进行帐户转移。
- 源注册状态将更新为 "已传输"，并将仅可用于历史使用情况报告。

### <a name="monetary-commitment"></a>货币承诺

货币承诺不可在注册之间转移。 货币承诺余额在契约上关联到订购服务的注册。 在帐户或注册转移过程中，不会转移货币承诺。

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>帐户和注册传输不影响任何服务

帐户或注册传输过程中没有停机时间。 如果已提供所有必备信息，则转移可以在请求的同一日完成。

## <a name="change-account-owner"></a>更改帐户所有者

Azure EA 门户可将一个帐户所有者的订阅转移到另一个帐户所有者。 有关详细信息，请参阅[更改帐户所有者](ea-portal-get-started.md#change-account-owner)。

## <a name="subscription-transfer-effects"></a>订阅转移的影响

将 Azure 订阅转移到同一 Azure Active Directory 租户中的某个帐户后，以前拥有[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将保留其访问权限。

若要查看哪些用户对订阅拥有 RBAC 访问权限：

1. 在 Azure 门户中，打开 **订阅**。
2. 选择要查看的订阅，然后选择“访问控制(IAM)”。
3. 选择“角色分配”。 角色分配页将列出对订阅拥有 RBAC 访问权限的所有用户。

如果将订阅转移到了不同 Azure AD 租户中的某个帐户，则以前拥有 [RBAC](../../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将失去其访问权限。 尽管失去了 RBAC 访问权限，但他们可以通过安全机制来访问订阅，这些机制包括：

- 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../../cloud-services/cloud-services-certs-create.md)。
- 存储空间等服务的访问密钥。 有关详细信息，请参阅 [Azure 存储帐户概述](../../storage/common/storage-account-overview.md)。
- Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其 Azure 资源的访问，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单上，选择“所有资源”。
3. 选择资源。
4. 在资源页上，单击“设置”以查看和更新现有的机密。

## <a name="delete-subscription"></a>删除订阅

删除你是帐户所有者的订阅：

1. 使用与你的帐户关联的凭据登录 Azure 门户。
1. 在“中心”菜单上，选择“订阅”。
1. 在页面左上角的订阅选项卡中，选择要取消的订阅，然后单击“取消订阅”以启动“取消”选项卡。
1. 输入订阅名称并选择取消原因，然后单击“取消订阅”按钮。

请注意，只有帐户管理员可以取消订阅。

## <a name="delete-an-account"></a>删除帐户

只能为没有有效订阅的有效帐户完成帐户删除操作。

1. 在 Enterprise Portal 的左侧导航区域中，选择“管理”。
1. 单击“帐户”选项卡。
1. 从“帐户”表中选择要删除的帐户。
1. 单击“帐户”行右侧的 X 图标。
1. 帐户下没有有效订阅后，在“帐户”行下单击“是”按钮，确认删除帐户。

## <a name="update-notification-settings"></a>更新通知设置

企业管理员会自动注册接收与其注册相关的使用情况通知。 每个企业管理员可以更改每项通知的发送间隔，或者完全禁用通知。

通知联系人显示在 Azure EA 门户中的“通知联系人”部分。 管理通知联系人可确保由组织中的适当人员接收 Azure EA 通知。

若要查看当前通知设置：

1. 在 Azure EA 门户中，导航到“管理” > “通知联系人”。
2. 电子邮件地址 – 与接收通知的企业管理员的 Microsoft 帐户、工作或学校帐户关联的电子邮件地址。
3. 未开票余额通知频率 – 设置向每个企业管理员发送通知的频率。

若要添加联系人：

1. 单击“+添加联系人”。
2. 输入电子邮件地址，然后确认。
3. 单击“ **保存**”。

新的通知联系人将显示在“通知联系人”部分。 若要更改通知频率，请选择通知联系人，然后单击所选行右侧的铅笔符号。 将频率设置为“每日”、“每周”、“每月”或“无”。

可以取消“即将达到计费周期结束日期”和“即将达到禁用和取消预配日期”生命周期通知。 禁用生命周期通知会取消有关计费周期和协议结束日期的通知。

## <a name="manage-partner-administrators"></a>管理合作伙伴管理员

Azure EA 门户中的每个合作伙伴管理员都能够添加或删除其他合作伙伴管理员。 合作伙伴管理员与间接合约的合作伙伴组织关联，不直接与合约关联。

### <a name="add-a-partner-administrator"></a>添加合作伙伴管理员

要查看与当前用户相同的合作伙伴组织关联的所有合约的列表，请单击“合约”选项卡，然后选中所需的合约框。

1. 以合作伙伴管理员身份登录。
1. 在左侧导航区域中单击“管理”。
1. 单击“合作伙伴”选项卡。
1. 单击“+ 添加管理员”并填写电子邮件地址、通知联系人和通知详细信息。
1. 点击“添加”。

### <a name="remove-a-partner-administrator"></a>添加合作伙伴管理员

要查看与当前用户相同的合作伙伴组织关联的所有合约的列表，请单击“合约”选项卡，然后选中所需的合约框。

1. 以合作伙伴管理员身份登录。
1. 在左侧导航区域中单击“管理”。
1. 单击“合作伙伴”选项卡。
1. 在“管理员”部分下，选择与要删除的管理员相应的行。
1. 点击右侧的 X 图标。
1. 确认要删除。

## <a name="manage-partner-notifications"></a>管理合作伙伴通知

合作伙伴管理员可以管理其注册使用情况通知的接收频率。 他们每周会自动收到未开票余额的通知。 他们可将每项通知的频率更改为每月、每周、每日，或完全禁用通知。

如果用户未收到某项通知，请使用以下步骤验证该用户的通知设置是否正确。

1. 以合作伙伴管理员的身份登录到 Azure EA 门户。
2. 单击“管理”，然后单击“合作伙伴”选项卡。
3. 在“管理员”部分下查看管理员列表。
4. 若要编辑通知首选项，请将鼠标悬停在相应的管理员上，然后单击铅笔符号。
5. 根据需要更新通知频率和生命周期通知。
6. 根据需要添加联系人，然后单击“添加”。
7. 单击“ **保存**”。

![显示“添加联系人”框的示例 ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>合作伙伴管理员查看合约

合作伙伴管理员可以在 Azure EA 门户中查看其所有直接和间接合约的列表视图。 将显示包含每个合约的概述的框以及合约编号、合约名称、余额和超额金额。

### <a name="view-a-list-of-enrollments"></a>查看合约列表

1. 以合作伙伴管理员身份登录。
1. 在页面左侧的导航栏上单击“管理”"。
1. 单击“许可登记表”选项卡。
1. 选中所需合约的框。

含所有合约的视图保留在页面顶部，其中每个合约都有一个框。 此外，还可以通过单击页面左侧导航中的当前合约编号，在合约之间导航。 系统将显示一个弹出窗口，使你能够通过单击相应的框来搜索合约或选择其他合约。

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

一个帐户所有者只能与一个部门关联。  这是为了确保在 Azure EA 门户的 EA 合约下精确地监控和分配与其所在部门关联的成本/支出

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>能将 Azure 帐户所有者作为安全组列出吗？

不能，订阅所有者必须使用唯一的 Microsoft 帐户 (MSA) 或 Azure Active Directory (AAD) 身份验证方式。 如果要考虑组织内的连续性，可以考虑创建通用帐户，并使用 AAD 来管理订阅的访问。

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>一个用户可以拥有多个订阅吗？

Azure 帐户所有者可以创建和管理无限多的订阅。

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>我如何访问/查看组织的所有订阅？

如今必须通过策略完成此操作；也就是说，你需要要求对于创建的每个订阅，都使用基于角色的访问将帐户添加到某个订阅角色。

### <a name="where-do-i-go-to-create-a-subscription"></a>去何处创建订阅？

必须由 Azure EA 门户中 EA 合约的管理员将你的帐户添加为帐户所有者角色后，你才能创建 Enterprise Azure (EA) 套餐订阅。 然后，需要登录到 Azure EA 门户，以获得创建 EA 套餐类型的订阅的权利。 我们建议使用 EA 门户的订阅选项卡中的“+ 添加订阅”链接创建你的首个 EA 订阅。  但如果帐户获得了授权，就能使用 portal.azure.com 中页面左上角的订阅选项卡更轻松地创建订阅，你可以在其中一步完成订阅的创建和重命名。

### <a name="who-can-create-a-subscription"></a>谁可以创建订阅？

要创建 Enterprise Azure 套餐类型的订阅，你必须在 [EA 门户](https://ea.azure.com)中获得帐户所有者角色。

## <a name="next-steps"></a>后续步骤

- 了解[虚拟机预留项](ea-portal-vm-reservations.md)如何帮助节省资金。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。
