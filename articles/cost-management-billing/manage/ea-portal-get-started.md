---
title: Azure EA 门户入门
description: 本文介绍 Azure EA 客户如何使用 Azure EA 门户。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: ace3c251d979a67666d2aaf01dca01e257bed66b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992226"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Azure EA 门户入门

本文提供以下方面的基本信息来帮助 Azure EA 直接和间接客户开始使用 [Azure EA 门户](https://ea.azure.com)：

- Azure EA 门户的结构。
- Azure EA 门户中使用的角色。
- 如何开始创建订阅。
- 在 Azure EA 门户和 Azure 门户中分析成本。

以下视频演示了完整的 Azure EA 门户登记会话：

[Azure EA 门户登记视频](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Azure EA 门户的层次结构

Azure EA 门户的层次结构包括：

**Microsoft Azure EA 门户** - Azure EA 门户是一个在线管理门户，可帮助你管理 Azure EA 服务的成本。 使用该门户可以创建 Azure EA 层次结构，包括部门、帐户和订阅。 使用它还可以核对所用服务的成本、下载使用情况报告，以及查看价目表。 另外，可以创建用于注册的 API 密钥。

**部门** - 创建部门有助于将成本细分为逻辑分组，然后在部门级别设置预算或配额。

**帐户** – 帐户是 Azure EA 门户中用于管理订阅的一个组织单位。 帐户也用于报告。

**订阅** – 订阅是 Azure EA 门户中的最小单位。 它们是服务管理员管理的 Azure 服务的容器。

下图演示了简单的 Azure EA 层次结构。

![简单 Azure EA 层次结构示意图](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企业用户角色

若要在注册中管理 Azure 服务，有五个不同的企业管理用户角色：

- 企业管理员
- 部门管理员
- 帐户所有者
- 服务管理员
- 通知联系人

角色用于在两个不同的 Microsoft Azure 门户中完成任务。 Azure EA 门户 (https://ea.azure.com) 用于帮助管理计费和成本。 Azure 门户 (https://portal.azure.com) 用于管理 Azure 服务。

用户角色与用户帐户相关联。 为了验证用户真实性，每个用户必须具有有效的工作、学校或 Microsoft 帐户。 请确保每个帐户与某个实际受监视的电子邮件地址相关联。 帐户通知将发送到该电子邮件地址。

设置用户时，可将多个工作、学校或 Microsoft 帐户分配到企业管理员角色。 不过，只能将一个工作、学校或 Microsoft 帐户分配到帐户所有者角色。 此外，可向单个工作、学校或 Microsoft 帐户同时应用企业管理员和帐户所有者角色。

### <a name="enterprise-administrator"></a>企业管理员

企业管理员角色拥有最高级别的访问权限。 具有该角色的用户可以：

- 管理帐户和帐户所有者
- 管理其他企业管理员
- 管理部门管理员
- 管理通知联系人
- 查看所有帐户的使用情况
- 查看所有帐户的未开单费用

可以在一个企业注册中分配多个企业管理员。 可以向企业管理员授予只读访问权限。 这些管理员全部继承部门管理员角色。

### <a name="department-administrator"></a>部门管理员

具有该角色的用户可以：

- 创建和管理部门
- 创建新的帐户所有者
- 查看他们所管理的部门的使用情况详细信息
- 查看成本（如果已获取所需权限）

可为每个企业注册分配多个部门管理员。

可向部门管理员授予只读访问权限。 若要授予只读访问权限，请编辑或新建部门管理员，并将只读选项设置为“是”。

### <a name="account-owner"></a>帐户所有者

具有该角色的用户可以：

- 创建和管理订阅
- 管理服务管理员
- 查看订阅的使用情况

每个帐户需要唯一的工作、学校或 Microsoft 帐户。 有关 Azure EA 门户管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

### <a name="service-administrator"></a>服务管理员

服务管理员有权在 Azure 门户中管理服务，以及将用户分配到共同管理员角色。

### <a name="notification-contact"></a>通知联系人

通知联系人接收与注册相关的使用通知。

## <a name="activate-your-enrollment"></a>激活注册

若要激活服务，最初的企业管理员需打开 Azure EA 门户 ([https://ea.azure.com](https://ea.azure.com))，并使用邀请电子邮件中的电子邮件地址登录。

如果已将设置为 EA 管理员，则无需收到用于登录到 Azure EA 门户的激活电子邮件。 你可以继续[https://ea.azure.com](https://ea.azure.com) ，并使用你的电子邮件地址（工作、学校或 live ID）和密码登录。

如果你有多个注册，请选择要激活的注册。 默认只会显示活动的注册。 若要查看注册历史记录，请清除 Azure EA 门户右上角的“活动”选项。

在“注册”下，状态将显示为“活动”。

![显示活动注册的示例](./media/ea-portal-get-started/ea-enrollment-status.png)

只有现有的 Azure 企业管理员才能创建其他企业管理员。

### <a name="create-another-enterprise-admin"></a>创建另一个企业管理员

- 登录到 [Azure EA 门户](https://ea.azure.com)，导航到“管理” > “注册详细信息”，然后单击页面右上角的“+ 添加管理员”。

确保已获取用户的电子邮件地址和首选的身份验证方法，例如，使用工作或学校帐户或者 Microsoft 帐户进行身份验证。 添加用户时需要此信息。

如果你不是 EA 管理员，请联系 EA 管理员，请求他们将你添加到注册。 被添加到注册后，你会收到一封激活电子邮件。

如果 EA 管理员无法为你提供帮助，请创建 [Azure EA 门户支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 提供以下信息：

- 注册号
- 要添加的电子邮件地址和身份验证类型（工作、学校帐户或 Microsoft 帐户）
- 现有 EA 管理员做出的电子邮件批准
  - 如果无法联系到现有的 EA 管理员，请联系合作伙伴或软件顾问，请求他们通过 VLSC 工具更改联系人详细信息。

有关企业管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

## <a name="create-an-azure-ea-department"></a>创建 Azure EA 部门

企业管理员及部门管理员可以使用部门来按部门和成本中心对企业 Azure 服务与使用情况进行组织和报告。 企业管理员可以：

- 添加或删除部门
- 将帐户关联到部门
- 创建部门管理员
- 允许部门管理员查看价格和成本

部门管理员可将新帐户添加到其部门。 他们可以从其部门中删除帐户，但不能从注册中删除帐户。

若要添加部门：

1. 在左侧导航区域中单击“管理”。
2. 单击“部门”选项卡，然后单击“+ 添加部门”并输入所需的信息。
3. 只有“部门名称”是必填字段。 该名称必须至少包含三个字符。
4. 完成后，单击“添加”。

## <a name="add-a-department-admin"></a>添加部门管理员

创建部门后，Azure 企业管理员可以添加部门管理员并将每个管理员关联到部门。 部门管理员可以：

- 创建其他部门管理员
- 查看和编辑部门属性，例如名称或成本中心
- 为其部门添加帐户
- 从其部门中删除帐户
- 下载其部门的使用情况详细信息
- 查看其部门的每月使用情况和费用（如果企业管理员已为其授予权限 <sup>1</sup>）

### <a name="to-add-a-department-admin"></a>添加部门管理员

以企业管理员的身份：

1. 在左侧导航区域中单击“管理”。
2. 单击“部门”选项卡，然后单击相应的部门。
3. 单击“+ 添加管理员”并添加所需的信息。
4. 若要授予只读访问权限，请将“只读”选项设置为“是”，然后单击“添加”。

![显示“添加部门管理员”对话框的示例](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>设置只读访问权限

可向部门管理员授予只读访问权限。 创建新的部门管理员时：

- 将只读选项设置为“是”。

若要编辑现有的部门管理员：

1. 选择部门，然后单击要编辑的“部门管理员”旁边的铅笔符号。
2. 将只读选项设置为“是”。 然后单击“保存”。

具有企业管理员角色的用户将自动获得部门管理员权限。

<sup>1</sup> 如果你有权查看部门每月使用情况和费用，但却看不到这些信息，请与合作伙伴联系。

## <a name="add-an-account"></a>添加帐户

帐户和订阅的结构会影响其管理方式及其在发票和报告中的显示方式。 典型的组织方式示例包括按业务部门、职能团队和地理位置进行构建。

若要添加帐户：

1. 在 Azure EA 门户的左侧导航区域中，单击“管理”。
2. 单击“帐户”选项卡，然后在“帐户”页上单击“+ 添加帐户”。
3. 选择部门或将其保留为未分配，然后选择所需的身份验证类型。
4. 键入一个易记名称用于标识报告中的帐户。
5. 键入要与新帐户关联的**帐户所有者电子邮件地址**。
6. 确认该电子邮件地址，然后单击“添加”。

![显示帐户列表和“+ 添加帐户”选项的示例](./media/ea-portal-get-started/create-ea-add-an-account.png)

可以单击“添加另一个帐户”来添加另一个帐户，或者单击左侧工具栏右下角的“添加”。

若要确认帐户所有权：

1. 登录到 Azure EA 门户。
1. 通过查看状态来确认帐户所有权。 状态应从“挂起”更改为“开始/结束日期”。 开始/结束日期分别是用户首次登录的日期以及协议的结束日期。
1. 首次登录 Azure EA 门户弹出“警告”消息时，帐户所有者需要单击“继续”以激活帐户。


## <a name="change-account-owner"></a>更改帐户所有者

企业管理员可以使用 Azure EA 门户来转移注册中的订阅帐户所有权。 该操作会将源用户帐户中的所有订阅转移到目标用户帐户。

有关转移用户帐户信息的要点：

- 支持从一个工作或学校帐户转移到另一个工作或学校帐户。
- 支持从 Microsoft 帐户转移到工作或学校帐户。
- 不支持从工作或学校帐户转移到 Microsoft 帐户。
- 支持从一个 Microsoft 帐户转移到另一个 Microsoft 帐户。 目标帐户必须是有效的 Azure 商务帐户才能成为有效的转移目标。 对于新帐户，当你登录到 Azure EA 门户时，系统会要求创建一个 Azure 商务帐户。 对于现有帐户，必须先创建新的 Azure 订阅，然后该帐户才符合条件。
- 完成订阅转移后，Microsoft 会更新帐户所有者。

基于角色的访问控制策略：

- 只有在同一租户中的两个组织 ID 之间进行的 Azure 订阅转移才会保留现有的 Azure 基于角色的访问控制 (RBAC) 策略、服务管理员角色分配和共同管理员角色分配。 其他订阅转移会导致 RBAC 策略以及服务管理员和共同管理员角色分配丢失。 策略和管理员角色不会在不同的目录之间转移。 服务管理员将更新为目标帐户的所有者。
- 在同一租户中的两个组织 ID 之间执行订阅转移时，会保留 RBAC 策略以及现有的服务管理员和共同管理员角色。

在更改帐户所有者之前：

1. 查看“帐户”选项卡并找到源帐户。 源帐户必须处于活动状态。
2. 找到目标帐户。 该帐户必须处于活动状态。

若要转移所有订阅的帐户所有权：

1. 在左侧导航区域中单击“管理”。
2. 单击“帐户”选项卡，将鼠标悬停在某个帐户上。
3. 单击右侧的“更改帐户所有者”符号。 这是一个人形符号。
4. 选择某个符合条件的帐户，然后单击“下一步”。
5. 确认转移并单击“提交”。

![显示“更改帐户所有者”符号的插图](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

若要转移单个订阅的帐户所有权：

1. 在左侧导航区域中单击“管理”。
2. 单击“帐户”选项卡，将鼠标悬停在某个帐户上。
3. 单击右侧的“转移订阅”符号。 该符号类似于一个页面。
4. 选择某个符合条件的订阅，然后单击“下一步”。
5. 确认转移并单击“提交”。

![显示“转移订阅”符号的插图](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

以下视频演示了 Azure EA 门户用户管理：

[Azure EA 门户用户管理视频](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>创建订阅

帐户所有者可以查看和管理订阅。 你可以使用订阅为组织中的团队授予对开发环境和项目的访问权限。 例如测试、生产、开发和过渡环境。 为每个应用程序环境创建不同的订阅可以帮助保护每个环境的安全。 还可以针对每个订阅分配不同的服务管理员帐户。 可将订阅关联到任意数量的服务。 帐户所有者可以创建订阅，并将服务管理员帐户分配到其帐户中的每个订阅。

### <a name="add-a-subscription"></a>添加订阅

使用以下信息添加订阅。

首次在帐户中添加订阅时，系统会要求你接受 MOSA 协议和费率计划。 尽管这些条款并不适用于企业协议客户，但创建订阅时需要接受它们。 Microsoft Azure 企业协议注册修正条款取代了上述条款，但你的契约关系并无变化。 出现提示时，请选中表示接受条款的框。

创建的所有新订阅使用默认订阅名称“Microsoft Azure Enterprise”。 可以更新订阅名称，使之与注册中的其他订阅相区分。 另外，请确保该名称在企业级别的报告中易于识别。

若要添加订阅：

1. 在 Azure EA 门户中登录到帐户。
2. 单击“管理”选项卡，然后单击页面顶部的“订阅”。
2. 确认你是否以帐户所有者的身份登录。
3. 依次单击“+添加订阅”、“购买”。
  首次将订阅添加到帐户时，必须提供联系人信息。 添加更多的订阅时，系统会自动添加联系人信息。
4. 单击“订阅”，选择创建的订阅，然后单击“编辑订阅详细信息”。
5. 更新“订阅名称”和“服务管理员”，然后选择勾选标记。
  订阅名称将显示在报告中，它也是与开发门户中的订阅关联的项目的名称。

新的订阅最长可能需要在 24 小时后才会显示在订阅列表中。 创建订阅后，可以：

- [编辑订阅详细信息](https://account.azure.com/Subscriptions)
- [管理订阅服务](https://portal.azure.com/#home)

## <a name="transfer-ea-subscription-to-pay-as-you-go-subscription"></a>将 EA 订阅转换为即用即付订阅

若要将 EA 订阅转换为采用即用即付费率的单个订阅，必须在 Azure EA 门户中创建新的支持请求。 若要创建支持请求，请在“帮助和支持”区域单击“+ 新建支持请求”。

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>关联现有帐户与即用即付订阅

如果在 Microsoft Azure 门户已拥有 Microsoft Azure 帐户，请输入关联的 Microsoft 帐户或者工作或学校帐户，以将其与企业协议合约相关联。

### <a name="associate-an-existing-account"></a>关联现有帐户

1. 在 Enterprise Portal 中单击“管理”。
1. 单击“帐户”选项卡。
1. 单击“+添加帐户”。
1. 输入与现有帐户关联的 Microsoft 帐户或者工作或学校帐户。
1. 确认与现有帐户关联的 Microsoft 帐户或者工作或学校帐户。
1. 提供用于在报表中标识此帐户的名称。
1. 单击“添加”。
1. 再次选择“+添加帐户”选项可添加其他帐户，选择“管理”按钮可返回主页。
1. 单击“帐户”页以查看该页时，新添加的帐户将显示为“挂起”状态。

### <a name="confirm-account-ownership"></a>确认帐户所有权

1. 登录到与你提供的 Microsoft 帐户或者工作或学校帐户关联的电子邮件帐户。
1. 打开标题为“邀请通过 Microsoft 批量许可激活 Microsoft Azure Service 帐户”的电子邮件通知。
1. 单击该邀请邮件中的“登录 Microsoft Azure Enterprise Portal”链接。
1. 单击**登录**。
1. 输入 Microsoft 帐户或者工作或学校帐户并输入密码，以登录并确认帐户所有权。

### <a name="azure-marketplace"></a>Azure 市场

大多数订阅均从即用即付环境转换到 Azure Enterprise Portal，但 Azure 市场服务不会。 为了能够在一个视图中查看所有订阅和费用，建议你将 Azure 市场服务添加到 Enterprise Portal：

1. 在左侧导航区域中单击“管理”。
1. 单击“合约选项卡”。
1. 查看“合约详细信息”部分。
1. 在“Azure 市场”字段的右侧，单击铅笔图标启用，并单击“保存”。

帐户所有者现可购买之前在即用即付环境中拥有的 Azure 市场订阅。

在合约中激活新的 Azure 市场订阅后，请取消在即用即付环境中创建的市场订阅。 此步骤非常重要，它的意义在于：即用即付这种付款方式失效后，市场订阅不会处于错误状态。

### <a name="msdn"></a>MSDN

MSDN 订阅自动转换为 MSDN 开发/测试套餐，EA 套餐所有现有的货币额度均失效。

### <a name="azure-in-open"></a>Azure 开放

将 Azure 开放许可订阅与 EA 相关联将会使任何未使用的 Azure 开放许可信用额度作废。 为避免任何可能发生的信用额度作废，推荐客户在将帐户添加到 EA 之前将所有信用额度用于 Azure 开放许可订阅。  

### <a name="accounts-with-support-subscriptions"></a>具有支持订阅的帐户

向有支持订阅（但尚无 EA 支持订阅）的 Enterprise Portal 添加现有的帐户时，请注意：MOSA 支持订阅不会自动转换，需要在 EA 中重新购买支持。 我们会提供一个宽限期（截至后一个月的月末），以便客户有时间重新订购支持。

## <a name="view-usage-summary-and-download-reports"></a>查看使用情况摘要和下载报告

企业管理员可以在 Azure EA 门户中查看其使用情况数据、货币承诺消耗量，以及与其他用途相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

查看特定帐户的详细使用情况：

下载“使用情况详细信息”报告。 单击 "**报表**"，然后单击 "**下载使用情况**" 选项卡。在报表列表中，单击要获取的月度报表的 "**下载**"。

该报告不包含任何适用的税费。 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报告中。

查看使用情况摘要报表和关系图：

1. 在 Azure EA 门户的左侧导航区域中，单击“报告”并查看“使用情况摘要”选项卡。  
  ![创建和查看使用情况摘要并下载报表](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports.png)
2. 选择承诺条款。
3. 在页面右上角的“M”（每月）和“C”（自定义）之间切换可以根据自定义的开始日期和结束日期查看“使用情况摘要”。  
  ![在自定义视图中创建和查看使用情况摘要并下载报表](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. 在图表上选择某个时段或月份可以查看更多详细信息。
5. 图表显示了每月的使用情况，其中细分了使用量、服务额外费用、单独计收的费用和市场费用。
6. 对于选定的月份，可在图表下方按部门、帐户和订阅进行筛选。
7. 在“按服务列出的费用”与“按层次结构列出的费用”之间切换。
8. 展开和折叠“Azure 服务”、“单独计收的费用”和“Azure 市场”可查看详细信息。

以下视频演示了如何查看使用情况：

[Azure EA 门户使用情况视频](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以使用“月份报告下载”页将多份报告下载为 CSV 文件。 其中包括：

- 余额和费用
- 使用情况详细信息
- 市场费用
- 价目表

若要下载报告：


1. 在 Azure EA 门户中单击“报告”。
2. 单击页面顶部的“下载使用情况”。
3. 选择月份报告旁边的“下载”。

从使用日期开始算起，最长可能需要延迟五天，其费用才会显示在报告中。

用户在使用 Safari 将 CSV 文件下载到 Excel 时可能会遇到格式错误。 为避免错误，请使用文本编辑器打开文件。

![显示“下载使用情况”页的示例](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

以下视频演示了如何下载使用情况信息：

[Azure EA 门户使用情况视频](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>高级报表下载

如需特定日期范围或帐户的报表，可以使用高级报表下载功能。 从 2016 年 8 月 30 日起，输出文件的格式从 .xlsx 变为 .csv，以便容纳较大的记录集。

1. 选择“高级报表下载”。
1. 选择适当的日期范围。
1. 选择适当的帐户。
1. 选择“请求使用情况数据”。
1. 选择“刷新”按钮，直到报表状态更新为“下载”。
1. 下载报表。

## <a name="ea-term-glossary"></a>EA 术语表

- **帐户**： Azure EA 门户上用于管理订阅并用于报表的组织单位。
- **帐户所有者**：标识用于管理 Microsoft Azure 上的订阅和服务管理员的人员。 他们可以查看此帐户及其关联订阅的使用情况数据。
- **修正订阅**：注册修正下的单年或同限期订阅。
- **承诺**：按照此预付款的使用量打折承诺费率，为 Microsoft Azure 服务承诺。
- **部门管理员**：标识为管理部门、创建新的帐户和帐户所有者、查看他们所管理的部门的使用情况详细信息以及在授予权限时查看成本的人员。
- **注册编号**：由 Microsoft 提供的唯一标识符，用于标识与企业协议关联的特定注册。
- **企业管理员**：标识为管理部门和部门所有者以及 Microsoft Azure 上的帐户和帐户所有者的个人。 他们能够管理企业管理员，查看使用情况数据、已计费数量以及与企业合约相关的所有帐户和订阅中的未计费费用。
- **企业协议**：一种 microsoft 许可协议，适用于使用集中购买的客户，希望在 microsoft 技术上标准化整个组织，并在 microsoft 软件的标准基础上维护信息技术基础结构。
- **企业协议注册**：企业协议计划中的注册，以折扣费率向批量提供 Microsoft 产品。
- **Microsoft 帐户**：一种基于 Web 的服务，该服务使参与站点能够使用一组凭据对用户进行身份验证。
- **Microsoft Azure 企业注册修正（注册修正）** ：由企业签署的修订，使其能够作为其企业注册的一部分访问 Microsoft Azure。
- **AZURE EA 门户**：企业客户用来管理其 Microsoft Azure 帐户及其相关订阅的门户。
- **消耗的资源数量**：一个月内使用的单个 Microsoft Azure 服务的数量。
- **服务管理员**：标识用于访问和管理 Azure EA 门户中的订阅和开发项目的人员。
- **订阅**：表示 Azure EA 门户订阅，它是由同一服务管理员管理 Microsoft Azure 服务的容器。
- **工作或学校帐户**：对于已设置 active directory 并与云联合的组织，并且所有帐户都在单个租户上。

### <a name="enrollment-statuses"></a>合约状态：

- **挂起**：注册管理员需要登录到 Azure EA 门户。 登录后，合约将切换为“可用”状态。
- **活动**：注册处于活动状态，可以在 Azure EA 门户中创建帐户和订阅。 在企业合约的结束日期之前，合约一直有效。
- **无限期扩展术语**：经过了企业协议结束日期后，就会发生无限长的时间。 它使选择延期的 EA 客户能够在企业协议结束后继续无限期地使用 Azure。 在 EA 合约到达企业协议结束日期前，合约管理员应决定是通过添加其他货币承诺的方式续约，切换为新合约，迁移到 Microsoft 在线订阅计划 (MOSP)，还是确定禁用与该合约关联的所有服务。
- 已**过期**： ea 客户选择超出了扩展条款，ea 注册已达到企业协议结束日期，注册将过期，并且所有关联的服务都将被禁用。
- 已**传输**：已将所有关联的帐户和服务转移到新注册的注册将以传输状态显示。 如果新合约编号是在续订期间生成的，则合约不会自动转移。 若要自动转移，必须将以前的合约编号包含在客户的续订文书中。

## <a name="get-started-on-azure-ea-faq"></a>Azure EA 入门 FAQ

本文档提供客户在加入过程中所提的典型问题的详细信息。  

### <a name="can-i-associate-my-existing-azure-account-to-enterprise-enrollment"></a>我可以将现有的 Azure 帐户关联到企业合约吗？

是的，你可以。 务必注意，你作为帐户所有者的所有 Azure 订阅都将被转换到企业协议。 这包括使用月额度的订阅（例如 Visual Studio、AzurePass、MPN 和 BizSpark 等），也就是说，这样做会导致失去月额度。

### <a name="i-accidentally-associated-my-existing-azure-account-with-enterprise-enrollment-as-a-result-i-lost-my-monthly-credit-is-it-possible-to-get-my-monthly-credit-back"></a>我不小心将现有的 Azure 帐户与企业注册关联起来了。 结果失去了月额度。 可以拿回月额度吗？

验证为 EA 帐户所有者的身份，使用与 Visual Studio 订阅相同的登录名登录 EA 后，若要恢复 Visual Studio 个人订阅的 Azure 权益，必须：
1. 在删除或移动帐户所有者拥有的所有订阅后，从 EA 门户删除此帐户所有者，并让其重新登录以获得个人的 Visual Studio Azure 权益。
 或者
1. 从 VLSC 中的管理站点订阅服务器删除 Visual Studio 订阅者并重新分配订阅，令他们使用不同的登录名，这样他们就可以重新注册个人的 Visual Studio Azure 权益。

### <a name="what-type-of-subscription-should-i-create"></a>我应该创建哪种类型的订阅？

EA 门户为企业客户提供两类订阅：

- Microsoft Azure Enterprise - 适合：
  - 所有生产使用情况
  - 基于所用基础设施的最优价格
  - 可在 https://azure.microsoft.com/pricing/enterprise-agreement/ 找到更多详细信息
- Enterprise 开发/测试 - 适合：
  - 所有团队开发/测试工作负载
  - 中到重量级单个开发/测试工作负载
  - 访问特殊的 MSDN 映像和优惠服务费率
  - 可在 https://azure.microsoft.com/offers/ms-azr-0148p/ 找到更多详细信息

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>能将订阅所有权转让给其他帐户吗？

能，可以将订阅所有权转让给其他帐户的。 例如，如果帐户 A 有三个订阅，企业管理员可以将其中一个订阅转让给帐户 B，一个转让给帐户 C，一个转让给帐户 D；也可以全都转让给帐户 E。

你可以转到 EA 并单击“管理”>“帐户”，将鼠标悬停在“帐户”（最右侧）上，就能看到“转让所有权”（大头照图标）和“转让订阅”（列表图标）选项。

此选项仅对有效帐户可见。

### <a name="i-see-subscription-name-defaults-to-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>订阅名默认为套餐名，我应该将订阅名更改为对自己的组织而言有意义的名称吗？

创建的任何订阅都默认采用你选择的套餐类型。 建议将订阅名更改为便于你跟踪订阅的名称。

**更改名称：**
1. 登录到 [https://account.windowsazure.com](https://account.windowsazure.com)。
1. 单击“订阅列表”。
1. 选择“订阅”。
1. 单击“管理订阅”图标。
1. 编辑订阅详细信息。

### <a name="how-can-i-track-cost-incurred-by-cost-center"></a>我如何跟踪成本中心所产生的费用？

为了跟踪成本中心产生的费用，你需要在以下任一级别定义成本中心：
- 部门
- 帐户
- 订阅

你可以根据需求使用同一个成本中心来跟踪与特定成本中心相关的使用情况和费用。

例如，为了跟踪涉及多个部分的某个特定项目的费用，你可能希望在订阅级别使用成本中心来跟踪使用情况和费用。

不能在服务级别定义成本中心，而如果你希望在服务级别跟踪使用情况，可以使用服务级别可用的“标记”功能。

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>如何跟踪组织中不同部门的使用情况和支出？

在 EA 合约下，你可以创建的部门数量取决于自己的需求。 为了正确地跟踪使用情况，你需要确保未跨部门共享订阅。

完成部门和订阅的创建后，可以在使用情况报表中看到有助于在部门级别跟踪使用情况和管理成本/支出的信息。

还可以通过 API 访问使用情况，详细信息和示例代码位于 [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI)。

### <a name="can-i-set-the-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>快要达限制时，我可以设置支出配额并获得警报吗？

可以在部门级别设置支出配额，系统会在支出限制达到你定义的配额的 50%、75%、90% 和 100% 时自动通知你。

要定义支出配额，请单击要添加支出限制的部门，然后单击编辑图标。 单击“保存”保存详细信息。

### <a name="i-used-resource-groups-rgs-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>我使用了资源组 (RG) 来实现 RBAC 和跟踪使用情况，如何查看相关的使用情况详细信息？

在服务级别跟踪“资源组”和“标记”等信息（如果使用了），这些信息位于详细使用情况下载 (CSV) 文件中，可以从 Azure EA 门户 ([https://ea.azure.com/report/downloadusage](https://ea.azure.com/report/downloadusage)) 下载该文件。

还可以通过 API 访问使用情况，详细信息和示例代码位于 [https://ea.azure.com/helpdocs/reportingAPI](https://ea.azure.com/helpdocs/reportingAPI)。

请注意，只能将标记应用于支持资源管理器操作的资源。 如果是通过经典部署模型（如通过经典门户）创建的虚拟机、虚拟网络或存储，则无法向该资源应用标记。 必须通过资源管理器重新部署这些资源才支持标记。 所有其他资源均支持标记。

### <a name="can-i-perform-analyses-using-power-bi"></a>能否使用 Power BI 执行分析？

可以。 借助适用于 Power BI 的 Microsoft Azure Enterprise 内容包，可以快速导入和分析企业合约中的 Azure 使用情况，了解哪个部门、帐户或订阅所占使用量最多，组织使用哪项服务最频繁，或跟踪支出和使用情况趋势。

**导航到 Power BI 网站：**

 1. 使用有效的工作或学校帐户登录。
    - 工作或学校帐户可以与用于通过 Azure EA 门户访问合约的帐户相同或不同。
 1. 在服务的仪表板中，选择：
    - Microsoft Azure Enterprise 磁贴。
    - 单击“连接”。
 1. 在“连接到 Azure Enterprise”屏幕上，选择：
    - Azure 环境 URL：[https://ea.azure.com](https://ea.azure.com)。
    - 月数：在 1 到 36 之间选择一个。
    - 合约编号：输入合约编号。
    - 单击“下一步”。
 1. 在“身份验证密钥”框中输入 API 密钥。 可以在 Azure EA 门户中获取 API 密钥，方式是在“下载使用情况”选项卡中单击“API 访问密钥”。
    - 将该密钥复制并粘贴到“帐户密钥”框中。
    - 在 Power BI 中加载数据需要大约 5-30 分钟，具体取决于数据集的大小。

Power BI 报表适用于 EA 直接合作伙伴以及能够查看账单信息的间接客户。

## <a name="next-steps"></a>后续步骤

- Azure EA 门户管理员应阅读 [Azure EA portal administration](ea-portal-administration.md)（Azure EA 门户管理）来了解常见的管理任务。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。
- 如需加入 Azure EA 方面的指导，请参阅 [Azure EA 加入指南](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)。
