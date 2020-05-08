---
title: Azure Enterprise 门户入门
description: 本文介绍 Azure 企业协议 (Azure EA) 客户如何使用 Azure Enterprise 门户。
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 647802ada8c8ca701c552749730b54bfce1e1651
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691561"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Azure Enterprise 门户入门

本文帮助直接和间接 Azure 企业协议 (Azure EA) 客户开始使用 [Azure Enterprise 门户](https://ea.azure.com)。 获取以下方面的基本信息：

- Azure Enterprise 门户的结构。
- Azure Enterprise 门户中使用的角色。
- 订阅的创建。
- Azure Enterprise 门户和 Azure 门户中的成本分析。

观看以下讲座视频，全面了解如何载入 Azure Enterprise 门户：

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-enterprise-portal-hierarchy"></a>Azure Enterprise 门户层次结构

Azure Enterprise 门户的层次结构包括：

- **Azure Enterprise 门户** - 一个在线管理门户，可帮助你管理 Azure EA 服务的成本。 可以：

  - 创建包含部门、帐户和订阅的 Azure EA 层次结构。
  - 核对所用服务的成本、下载使用情况报表，以及查看价目表。
  - 创建用于注册的 API 密钥。

- “部门”可帮助你将成本细分为逻辑分组。  使用部门可在部门级别设置预算或配额。

- “帐户”是 Azure Enterprise 门户中的一个组织单位。  可以使用帐户来管理订阅和访问报表。

- “订阅”是 Azure Enterprise 门户中的最小单位。  它们是服务管理员管理的 Azure 服务的容器。

下图演示了简单的 Azure EA 层次结构。

![简单 Azure EA 层次结构示意图](./media/ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企业用户角色

以下管理用户角色是企业注册的一部分：

- 企业管理员
- 部门管理员
- 帐户所有者
- 服务管理员
- 通知联系人

角色用于在两个不同的门户中完成任务。 可以使用 [Azure Enterprise 门户](https://ea.azure.com)来管理计费和成本，使用 [Azure 门户](https://portal.azure.com)来管理 Azure 服务。

用户角色与用户帐户相关联。 为了验证用户真实性，每个用户必须具有有效的工作、学校或 Microsoft 帐户。 请确保每个帐户与某个实际受监视的电子邮件地址相关联。 帐户通知将发送到该电子邮件地址。

设置用户时，可将多个帐户分配到企业管理员角色。 但是，只能将一个帐户分配到帐户所有者角色。 此外，可向单个帐户同时分配企业管理员和帐户所有者角色。

### <a name="enterprise-administrator"></a>企业管理员

具有此角色的用户拥有最高级别的访问权限。 他们可以：

- 管理帐户和帐户所有者。
- 管理其他企业管理员。
- 管理部门管理员。
- 管理通知联系人。
- 查看所有帐户的使用情况。
- 查看所有帐户的未开单费用。

可以在一个企业注册中分配多个企业管理员。 可以向企业管理员授予只读访问权限。 这些管理员全部继承部门管理员角色。

### <a name="department-administrator"></a>部门管理员

具有此角色的用户可以：

- 创建和管理部门。
- 创建新的帐户所有者。
- 查看他们所管理的部门的使用情况详细信息。
- 查看成本（如果拥有所需的权限）。

可为每个企业注册分配多个部门管理员。

编辑或新建部门管理员时，可以向部门管理员授予只读访问权限。 将只读选项设置为“是”。 

### <a name="account-owner"></a>帐户所有者

具有此角色的用户可以：

- 创建和管理订阅。
- 管理服务管理员。
- 查看订阅的使用情况。

每个帐户需要唯一的工作、学校或 Microsoft 帐户。 有关 Azure Enterprise 门户管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

### <a name="service-administrator"></a>服务管理员

服务管理员角色有权在 Azure 门户中管理服务，以及将用户分配到共同管理员角色。

### <a name="notification-contact"></a>通知联系人

通知联系人接收注册相关的使用情况通知。

## <a name="activate-your-enrollment"></a>激活注册

若要激活服务，最初的企业管理员需打开 [Azure Enterprise 门户](https://ea.azure.com)，并使用邀请电子邮件中的电子邮件地址登录。

如果你已被设置为企业管理员，则无需接收激活电子邮件。 转到 [Azure Enterprise 门户](https://ea.azure.com)，并使用工作、学校帐户或 Microsoft 帐户电子邮件地址和密码登录。

如果你有多个注册，请选择要激活的注册。 默认只会显示活动的注册。 若要查看注册历史记录，请清除 Azure Enterprise 门户右上角的“活动”选项。 

在“注册”下，状态将显示为“活动”。  

![显示活动注册的示例](./media/ea-portal-get-started/ea-enrollment-status.png)

只有现有的 Azure 企业管理员才能创建其他企业管理员。

### <a name="create-another-enterprise-administrator"></a>创建另一个企业管理员

若要添加另一个企业管理员：

1. 登录到 [Azure Enterprise 门户](https://ea.azure.com)。
1. 转到“管理” > “注册详细信息”。  
1. 选择右上角的“+ 添加管理员”。 

确保已获取用户的电子邮件地址和首选的身份验证方法，例如工作、学校帐户或 Microsoft 帐户。

如果你不是企业管理员，请联系企业管理员，让他们将你添加到注册。 被添加到注册后，你会收到一封激活电子邮件。

如果企业管理员无法为你提供帮助，请创建 [Azure Enterprise 门户支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 提供以下信息：

- 注册编号
- 要添加的电子邮件地址和身份验证类型（工作、学校帐户或 Microsoft 帐户）
- 现有企业管理员做出的电子邮件批准
  - 如果无法联系到现有的企业管理员，请联系合作伙伴或软件顾问，让他们通过批量许可服务中心 (VLSC) 工具更改联系人详细信息。

有关企业管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

## <a name="create-an-azure-enterprise-department"></a>创建 Azure Enterprise 部门

企业管理员及部门管理员可以使用部门来按部门和成本中心对企业 Azure 服务与使用情况进行组织和报告。 企业管理员可以：

- 添加或删除部门。
- 将帐户关联到部门。
- 创建部门管理员。
- 允许部门管理员查看价格和成本。

部门管理员可将新帐户添加到其部门。 他们可以从其部门中删除帐户，但不能从注册中删除帐户。

若要添加部门：

1. 登录到 Azure Enterprise 门户。
1. 在左侧窗格中选择“管理”。 
1. 选择“部门”选项卡，然后选择“+ 添加部门”。  
1. 输入信息。
   只有部门名称是必填字段。 该名称必须至少包含三个字符。
1. 完成后，选择“添加”。 

## <a name="add-a-department-administrator"></a>添加部门管理员

创建部门后，企业管理员可以添加部门管理员并将每个管理员关联到部门。 部门管理员可针对其部门执行以下操作：

- 创建其他部门管理员
- 查看和编辑部门属性，例如名称或成本中心
- 添加帐户
- 删除帐户
- 下载使用情况详细信息
- 查看每月使用情况和费用 <sup>1</sup>

> <sup>1</sup> 企业管理员必须授予这些权限。 如果你有权查看部门每月使用情况和费用，但却看不到这些信息，请与合作伙伴联系。

### <a name="to-add-a-department-administrator"></a>添加部门管理员

以企业管理员的身份：

1. 登录到 Azure Enterprise 门户。
1. 在左侧窗格中选择“管理”。 
1. 选择“部门”选项卡，然后选择部门。 
1. 选择“+ 添加管理员”并添加所需的信息。 
1. 若要授予只读访问权限，请将“只读”选项设置为“是”，然后选择“添加”。   

![显示“添加部门管理员”对话框的示例](./media/ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>设置只读访问权限

可向部门管理员授予只读访问权限。

- 创建新的部门管理员时，请将只读选项设置为“是”。 

- 若要编辑现有的部门管理员：
   1. 选择部门，然后选择要编辑的“部门管理员”旁边的铅笔符号。 
   1. 将只读选项设置为“是”，然后选择“保存”。  

企业管理员将自动获得部门管理员权限。

## <a name="add-an-account"></a>添加帐户

帐户和订阅的结构会影响其管理方式及其在发票和报表中的显示方式。 典型的组织结构示例包括按业务部门、职能团队和地理位置。

若要添加帐户：

1. 在 Azure Enterprise 门户的左侧导航区域中，选择“管理”。 
1. 选择“帐户”选项卡。  在“帐户”页上，选择“+添加帐户”。  
1. 选择部门或将其保留为未分配，然后选择所需的身份验证类型。
1. 输入一个易记名称用于标识报表中的帐户。
1. 输入要与新帐户关联的**帐户所有者电子邮件地址**。
1. 确认该电子邮件地址，然后选择“添加”。 

![显示帐户列表和“+ 添加帐户”选项的示例](./media/ea-portal-get-started/create-ea-add-an-account.png)

若要添加另一个帐户，请选择“添加另一个帐户”，或者选择左侧工具栏右下角的“添加”。  

若要确认帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 查看状态。

   状态应从“挂起”更改为“开始/结束日期”。   开始/结束日期分别是用户首次登录的日期以及协议的结束日期。
1. 首次登录 Azure Enterprise 门户时，如果弹出“警告”消息，帐户所有者需要选择“继续”以激活帐户。  

## <a name="change-account-owner"></a>更改帐户所有者

企业管理员可以使用 Azure Enterprise 门户来转移注册中的订阅帐户所有权。 该操作会将源用户帐户中的所有订阅转移到目标用户帐户。

转移帐户时，请注意以下重要信息：

- 可按如下所述进行转移：
  - 从一个工作或学校帐户转移到另一个工作或学校帐户。
  - 从 Microsoft 帐户转移到工作或学校帐户。
  - 从一个 Microsoft 帐户转移到另一个 Microsoft 帐户。

    目标帐户必须是有效的 Azure 商务帐户才能成为有效的转移目标。 对于新帐户，当你登录到 Azure Enterprise 门户时，系统会要求创建一个 Azure 商务帐户。 对于现有帐户，必须先创建新的 Azure 订阅，然后该帐户才符合条件。

- 无法从工作或学校帐户转移到 Microsoft 帐户。

- 完成订阅转移后，Microsoft 会更新帐户所有者。

了解以下基于角色的访问控制 (RBAC) 策略：

- 在同一租户中的两个组织 ID 之间执行订阅转移时，会保留 RBAC 策略以及现有的服务管理员和共同管理员角色。
- 其他订阅转移会导致 RBAC 策略和角色分配丢失。
- 策略和管理员角色不会在不同的目录之间转移。 服务管理员将更新为目标帐户的所有者。

在更改帐户所有者之前：

1. 在 Azure Enterprise 门户中，查看“帐户”选项卡并找到源帐户。  源帐户必须处于活动状态。
1. 找到目标帐户，并确保它处于活动状态。

若要转移所有订阅的帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中，选择“管理”。 
1. 选择“帐户”选项卡，将鼠标悬停在某个帐户上。 
1. 选择右侧的“更改帐户所有者”图标。 这是一个人形图标。
1. 选择某个符合条件的帐户，然后选择“下一步”。 
1. 确认转移并选择“提交”。 

![显示“更改帐户所有者”符号的插图](./media/ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

若要转移单个订阅的帐户所有权：

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中，选择“管理”。 
1. 选择“帐户”选项卡，将鼠标悬停在某个帐户上。 
1. 选择右侧的“转移订阅”图标。 该图标类似于一个页面。
1. 选择某个符合条件的订阅，然后选择“下一步”。 
1. 确认转移并选择“提交”。 

![显示“转移订阅”符号的插图](./media/ea-portal-get-started/ea-transfer-subscriptions.png)

观看以下视频来了解 Azure Enterprise 门户用户管理：

> [!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>创建订阅

帐户所有者可以查看和管理订阅。 你可以使用订阅为组织中的团队授予对开发环境和项目的访问权限。 例如测试、生产、开发和过渡环境。

为每个应用程序环境创建不同的订阅可以帮助保护每个环境的安全。

- 还可以针对每个订阅分配不同的服务管理员帐户。
- 可将订阅关联到任意数量的服务。
- 帐户所有者可以创建订阅，并将服务管理员帐户分配到其帐户中的每个订阅。

### <a name="add-a-subscription"></a>添加订阅

使用以下信息添加订阅。

首次在帐户中添加订阅时，系统会要求你接受 Microsoft 在线订阅协议 (MOSA) 和费率计划。 尽管这些条款并不适用于企业协议客户，但必须接受 MOSA 和费率计划才能创建订阅。 Microsoft Azure 企业协议注册修正条款取代了上述条款，但你的契约关系并无变化。 出现提示时，请选中表示接受条款的框。

创建的订阅使用默认订阅名称 _Microsoft Azure Enterprise_。 可以更改该名称，使之与注册中的其他订阅相区分，并确保该名称在企业级别的报表中易于识别。

若要添加订阅：

1. 在 Azure Enterprise 门户中登录到帐户。
1. 选择“管理”选项卡，然后选择页面顶部的“订阅”。  
1. 确认你是否以帐户所有者的身份登录。
1. 依次选择“+添加订阅”、“购买”。  

   首次将订阅添加到帐户时，必须提供联系人信息。 添加更多的订阅时，系统会自动添加联系人信息。

1. 选择“订阅”，然后选择创建的订阅。 
1. 选择“编辑订阅详细信息”。 
1. 编辑“订阅名称”和“服务管理员”，然后选择勾选标记。  

   订阅名称将显示在报表中。 它是与开发门户中的订阅关联的项目的名称。

新的订阅最长可能需要在 24 小时后才会显示在订阅列表中。 创建订阅后，可以：

- [编辑订阅详细信息](https://account.azure.com/Subscriptions)
- [管理订阅服务](https://portal.azure.com/#home)

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>将 Enterprise 订阅转换为即用即付订阅

若要将 Enterprise 订阅转换为采用即用即付费率的单个订阅，必须在 Azure Enterprise 门户中创建新的支持请求。 若要创建支持请求，请在“帮助和支持”区域中选择“+ 新建支持请求”。  

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>将现有帐户关联到即用即付订阅

如果你在 Azure 门户中已有一个 Microsoft Azure 帐户，请输入关联的工作、学校帐户或 Microsoft 帐户，以将其关联到企业协议注册。

### <a name="associate-an-existing-account"></a>关联现有帐户

1. 在 Azure Enterprise 门户中选择“管理”。 
1. 选择“帐户”选项卡。 
1. 选择“+添加帐户”。 
1. 输入与现有 Azure 帐户关联的工作、学校帐户或 Microsoft 帐户。
1. 确认该帐户已与现有 Azure 帐户相关联。
1. 提供用于在报表中标识此帐户的名称。
1. 选择 **添加** 。
1. 若要添加更多的帐户，可以再次选择“+添加帐户”选项，或选择“管理”按钮返回主页。  
1. 查看“帐户”页时，新添加的帐户将显示为“挂起”状态。  

### <a name="confirm-account-ownership"></a>确认帐户所有权

1. 登录到与提供的工作、学校帐户或 Microsoft 帐户关联的电子邮件帐户。
1. 打开标题为“邀请通过 Microsoft 批量许可激活 Microsoft Azure Service 帐户”的电子邮件通知  。
1. 选择该邀请邮件中的“登录到 Microsoft Azure Enterprise 门户”链接。 
1. 选择“登录”  。
1. 输入工作、学校帐户或 Microsoft 帐户和密码，以登录并确认帐户所有权。

### <a name="azure-marketplace"></a>Azure 市场

尽管大部分订阅都可以从即用即付环境转换为 Azure 企业协议，但 Azure 市场服务不支持这样做。 为了能够在一个视图中查看所有订阅和费用，我们建议将 Azure 市场服务添加到 Azure Enterprise 门户。

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中选择“管理”。 
1. 选择“注册”选项卡。 
1. 查看“注册详细信息”部分。 
1. 在“Azure 市场”字段的右侧，选择铅笔图标来启用此功能。 选择“保存”。 

现在，帐户所有者可以购买以前在即用即付订阅中拥有的任何 Azure 市场服务。

在 Azure EA 注册中激活新的 Azure 市场订阅后，请取消在即用即付环境中创建的 Azure 市场服务。 此步骤非常重要，它的意义在于：即用即付这种付款方式失效后，Azure 市场订阅不会处于错误状态。

### <a name="msdn"></a>MSDN

MSDN 订阅自动转换为 MSDN 开发/测试套餐，Azure EA 套餐所有现有的货币额度均失效。

### <a name="azure-in-open"></a>Azure 开放许可

如果将 Azure 开放许可订阅关联到企业协议，任何未使用的 Azure 开放许可额度将会作废。 因此，在将帐户添加到企业协议之前，我们建议用掉 Azure 开放许可订阅的所有额度。  

### <a name="accounts-with-support-subscriptions"></a>具有支持订阅的帐户

如果企业协议没有支持订阅，而你将具有支持订阅的现有帐户添加到 Azure Enterprise 门户，则 MOSA 支持订阅不会自动转换。 需要在宽限期（截至后一个月的月末）内在 Azure EA 中重新购买支持订阅。

## <a name="view-usage-summary-and-download-reports"></a>查看使用情况摘要和下载报告

企业管理员可以查看对其使用情况数据、使用的货币承诺以及与 Azure Enterprise 门户中其他使用量相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

若要查看特定帐户的详细使用情况，请下载使用情况详细信息报表：

1. 登录到 Azure Enterprise 门户。
1. 选择“报告”。 
1. 选择“下载使用情况”选项卡。 
1. 在报表列表中，选择要获取的月份报表对应的“下载”。 

   > [!NOTE]
   > 使用情况详细信息报表不包含任何适用的税费。
   >
   > 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报表中。

查看使用情况摘要报表和关系图：

1. 登录到 Azure Enterprise 门户。

1. 选择承诺条款。

   若要更改“使用情况摘要”的日期范围，可以在页面右上角从“M”（每月）切换为“C”（自定义），然后输入自定义的开始日期和结束日期。   

   ![在自定义视图中创建和查看使用情况摘要并下载报表](./media/ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. 若要查看更多详细信息，可以在图表上选择某个时段或月份。

   - 该图表显示了每月的使用情况，其中细分了使用量、服务额外费用、单独计收的费用和 Azure 市场费用。
   - 对于选定的月份，可以使用图表下面的字段按部门、帐户和订阅进行筛选。
   - 可以在“按服务列出的费用”与“按层次结构列出的费用”之间切换。  
   - 展开相关的部分可以查看“Azure 服务”、“单独计收的费用”和“Azure 市场”中的详细信息。   

观看以下视频来了解如何查看使用情况：

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以使用“月份报表下载”页以 CSV 文件形式下载以下报表：

- 余额和费用
- 使用情况详细信息
- Azure 市场费用
- 价目表

若要下载报告：

1. 在 Azure Enterprise 门户中选择“报表”。 
2. 选择页面顶部的“下载使用情况”。 
3. 选择月份报告旁边的“下载”。 

   > [!NOTE]
   > 从使用日期开始算起，最长可能需要延迟五天，其费用才会显示在报告中。
   >
   > 用户在使用 Safari 将 CSV 文件下载到 Excel 时可能会遇到格式错误。 为避免错误，请使用文本编辑器打开文件。

![显示“下载使用情况”页的示例](./media/ea-portal-get-started/create-ea-download-csv-reports.png)

观看以下视频来了解如何下载使用情况信息：

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>高级报表下载

可以使用高级报表下载来获取涵盖特定日期范围或帐户的报表。 输出文件采用 CSV 格式，以容纳较大的记录集。

1. 在 Azure Enterprise 门户中选择“高级报表下载”。 
1. 选择适当的日期范围和帐户。
1. 选择“请求使用情况数据”  。
1. 选择“刷新”按钮，直到报表状态更新为“下载”。  
1. 下载报表。

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>下载先前注册的使用情况报表和计费信息

发生注册转移后，可以下载先前某个注册的使用情况报表和计费信息。 Azure Enterprise 门户和成本管理中都会提供历史报表。

Azure Enterprise 门户会从视图中筛选出非活动的注册。 需要取消选中“活动”框才能查看非活动的已转移注册。   

![取消选中“活动”框可让用户查看非活动的注册](./media/ea-portal-get-started/unchecked-active-box.png)

## <a name="azure-ea-term-glossary"></a>Azure EA 术语表

- **帐户**：Azure Enterprise 门户中的一个组织单位。 它用于管理订阅和报表。
- **帐户所有者**：管理 Azure 上的订阅和服务管理员的人员。 他们可以查看此帐户及其关联订阅的使用情况数据。
- **修订订阅**：注册修正条款下的一年期订阅或连年订阅。
- **承诺**：在此预付模式下，Azure 服务的年货币额承诺按折扣承诺费率为使用量付款。
- **部门管理员**：管理部门、新建帐户和帐户所有者、查看所管理部门的使用情况详细信息并可在获得授权时查看费用的用户。
- **合约编号**：Microsoft 提供的唯一标识符，用于标识与企业协议关联的特定注册。
- **企业管理员**：管理 Azure 上的部门、部门所有者、帐户和帐户所有者的用户。 他们能够管理企业管理员，查看使用情况数据、已计费数量以及与企业合约相关的所有帐户和订阅中的未计费费用。
- **企业协议**：一种 Microsoft 许可协议，适用于采用集中购买模式、希望通过 Microsoft 技术实现整个组织的规范化并基于 Microsoft 软件标准维护信息技术基础结构的客户。
- **企业协议合约**：企业协议计划中的注册，以折扣费率批量提供 Microsoft 产品。
- **Microsoft 帐户**：一个基于 Web 的服务，使参与的站点能够使用一组凭据对用户进行身份验证。
- **Microsoft Azure Enterprise 注册修正条款（注册修正条款）** ：企业签署的修正条款，是企业注册的一部分，为其提供对 Azure 的访问权限。
- **Azure Enterprise 门户**：供企业客户用来管理 Azure 帐户及其相关订阅的门户。
- **已使用的资源数量**：一个月内使用的单个 Azure 服务的数量。
- **服务管理员**：可以在 Azure Enterprise 门户中访问和管理订阅与开发项目的人员。
- **订阅**：表示 Azure Enterprise 门户订阅，是同一个服务管理员所管理的 Azure 服务的容器。
- **工作或学校帐户**：适用于已设置与云联合的 Active Directory 且所有帐户都在单个租户上的组织。

### <a name="enrollment-statuses"></a>注册状态

- **Pending**：注册管理员需要登录到 Azure Enterprise 门户。 登录后，注册将切换为“活动”状态。
- **活动**：注册处于“活动”状态，可以在 Azure Enterprise 门户中创建帐户和订阅。 在企业协议结束日期之前，该注册将保持活动状态。
- **无限延期**：在企业协议结束日期后，会出现无限延期状态。 它使选择延期的 Azure EA 客户能够在企业协议结束后继续无限期地使用 Azure 服务。

   在 Azure EA 注册达到企业协议结束日期之前，注册管理员应决定是否要采取以下做法：

  - 通过添加更多的货币承诺来续订注册。
  - 转移到新注册。
  - 迁移到 Microsoft 在线订阅计划 (MOSP)。
  - 确认禁用与注册关联的所有服务。
- **已过期**：Azure EA 客户选择不延期，且 Azure EA 注册已达到企业协议结束日期。 注册将会过期，并且所有关联的服务将被禁用。
- **已转移**：所有关联的帐户和服务均已转移到新注册的注册显示为已转移状态。
  >[!NOTE]
  > 如果新注册编号是在续订时生成的，则注册不会自动转移。 若要方便自动转移，必须将以前的注册编号包含在续订文书中。

## <a name="get-started-on-azure-ea---faq"></a>Azure EA 入门 - 常见问题解答

本部分提供客户在载入过程中所提出的典型问题的详细信息。  

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>是否可以将现有的 Azure 帐户关联到 Azure EA 注册？

是的。 你是其帐户所有者的所有 Azure 订阅都将转换为企业协议。 这包括使用每月额度的订阅，例如 Visual Studio、AzurePass、MPN 和 BizSpark 等。 转换此类订阅时，会失去每月额度。

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>我不小心将现有的 Azure 帐户与 Azure EA 注册关联起来了。 结果失去了月额度。 可以重新取回每月额度吗？

如果你已经以 Azure EA 帐户所有者的身份使用与 Visual Studio 订阅相同的凭据登录，则可以通过执行以下操作之一来恢复 Visual Studio 个人订阅的 Azure 权益：

- 在删除或移动帐户所有者拥有的所有订阅后，从 Azure Enterprise 门户中删除你的帐户所有者。 然后，重新注册个人 Visual Studio Azure 权益。
- 从 VLSC 中的管理站点删除 Visual Studio 订阅者，并将订阅重新分配到某个帐户（这次请使用不同的凭据）。 然后，重新注册个人 Visual Studio Azure 权益。

### <a name="what-type-of-subscription-should-i-create"></a>我应该创建哪种类型的订阅？

Azure Enterprise 门户为企业客户提供两种类型的订阅：

- Microsoft Azure Enterprise，非常适合用于：
  - 所有生产使用情况
  - 基于所用基础设施的最优价格

  有关详细信息，请[联系 Azure 销售人员](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- Enterprise 开发/测试，非常适合用于：
  - 所有团队开发/测试工作负载
  - 中到重量级单个开发/测试工作负载
  - 访问特殊的 MSDN 映像和优惠服务费率

  有关详细信息，请参阅 [Enterprise 开发/测试套餐](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>能将订阅所有权转让给其他帐户吗？

可以将订阅所有权转移到其他帐户。 例如，如果帐户 A 有三个订阅，企业管理员可以将其中一个订阅转移到帐户 B，一个转移到帐户 C，一个转移到帐户 D。或者，将所有订阅转移到帐户 E。

若要转移订阅：

1. 在 Azure Enterprise 门户中选择“管理” > “帐户”。  
1. 将鼠标悬停在最右侧的“帐户”，就能看到“转移所有权”（人形图标）和“转移订阅”（列表图标）选项。    这些选项仅对活动的帐户可见。

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>我的订阅名称与套餐名称相同。 我应该将订阅名称更改为对我的组织而言有意义的名称吗？

创建订阅时，其名称默认为所选的套餐类型。 建议将订阅名更改为便于你跟踪订阅的名称。

若要更改名称：

1. 登录到 [https://account.windowsazure.com](https://account.windowsazure.com)。
1. 选择订阅列表。
1. 选择要编辑的订阅。
1. 选择“管理订阅”图标。 
1. 编辑订阅详细信息。

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>如何跟踪成本中心产生的费用？

若要跟踪成本中心产生的费用，需要在以下级别之一定义成本中心：

- 部门
- 帐户
- 订阅

可以根据需求使用同一个成本中心来跟踪与特定成本中心相关的使用情况和费用。

例如，为了跟踪涉及多个部分的某个特定项目的费用，你可能希望在订阅级别定义成本中心来跟踪使用情况和费用。

不能在服务级别定义成本中心。 若要在服务级别跟踪使用情况，可以使用在服务级别可用的“标记”功能。 

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>如何跟踪组织中不同部门的使用情况和支出？

在 Azure EA 注册下，可根据需要创建任意数量的部门。 若要正确跟踪使用情况，请确保不要在部门之间共享订阅。

创建部门和订阅后，可以在使用情况报表中看到数据。 这些信息有助于在部门级别跟踪使用情况和管理成本与支出。

还可以通过报告 API 访问使用情况数据。 有关详细信息和示例代码，请参阅 [Azure Enterprise REST API](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis)。

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>是否可以设置支出配额，并在即将达到限制时收到警报？

可以在部门级别设置支出配额，系统会在支出限制达到定义的配额的 50%、75%、90% 和 100% 时自动通知你。

若要定义支出配额，请选择部门，然后选择编辑图标。 编辑支出限制详细信息后，选择“保存”。 

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>我使用了资源组来实现 RBAC 和跟踪使用情况。 如何查看相关的使用情况详细信息？

如果你使用了资源组和标记，则会在服务级别跟踪此信息，可以在下载的详细使用情况 (CSV) 文件中访问此信息。   请参阅 Azure Enterprise 门户中的[下载使用情况报表](https://ea.azure.com/report/downloadusage)。

也可以通过 API 访问使用情况。 有关详细信息和示例代码，请参阅 [Azure Enterprise REST API](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-rest-apis)。

> [!NOTE]
> 只能将标记应用到支持 Azure 资源管理器操作的资源。 如果是通过经典部署模型（如通过经典门户）创建的虚拟机、虚拟网络或存储，则无法向该资源应用标记。 必须通过资源管理器重新部署这些资源才支持标记。 所有其他资源均支持标记。

### <a name="can-i-perform-analyses-using-power-bi"></a>能否使用 Power BI 执行分析？

是的。 借助适用于 Power BI 的 Microsoft Azure Enterprise 内容包，可以：

- 快速导入和分析企业注册中的 Azure 使用情况。
- 了解哪个部门、帐户或订阅所占的使用量最大。
- 了解组织使用哪项服务最频繁。
- 跟踪支出和使用趋势。

若要使用 Power BI：

1. 转到 Power BI 网站。
1. 使用有效的工作或学校帐户登录。

   工作或学校帐户可以与用于通过 Azure Enterprise 门户访问注册的帐户相同或不同。
1. 在服务仪表板上选择“Microsoft Azure Enterprise”磁贴，然后选择“连接”。 
1. 在“连接到 Azure Enterprise”屏幕上，输入： 
    - Azure 环境 URL：[https://ea.azure.com](https://ea.azure.com)
    - 月数：1 到 36
    - 注册编号：你的注册编号
1. 选择“**下一页**”。
1. 在“帐户密钥”框中输入 API 密钥。 

   可以在 Azure Enterprise 门户中找到 API 密钥。 在“下载使用情况”选项卡下，选择“API 访问密钥”。   复制该密钥，然后将其粘贴到 Power BI 的“帐户密钥”框中。 

在 Power BI 中加载数据需要 5 到 30 分钟，具体取决于数据集的大小。

Power BI 报告适用于 Azure EA 直接合作伙伴以及能够查看帐单信息的间接客户。

## <a name="next-steps"></a>后续步骤

- Azure Enterprise 门户管理员应阅读 [Azure Enterprise 门户管理](ea-portal-administration.md)来了解常见的管理任务。
- 在排查 Azure Enterprise 门户问题时如需帮助，请参阅[排查 Azure Enterprise 门户访问问题](ea-portal-troubleshoot.md)。
- 如需载入 Azure EA 方面的指导，请参阅 [Azure EA 载入指南 (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)。
