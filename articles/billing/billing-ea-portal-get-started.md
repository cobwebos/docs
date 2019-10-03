---
title: Azure EA 门户入门
description: 本文介绍 Azure EA 客户如何使用 Azure EA 门户。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900924"
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

![简单 Azure EA 层次结构示意图](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企业用户角色

若要在注册中管理 Azure 服务，可以使用四种不同的企业管理用户角色：

- 企业管理员
- 部门管理员
- 帐户所有者
- 服务管理员

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

每个帐户需要唯一的工作、学校或 Microsoft 帐户。 有关 Azure EA 门户管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。

### <a name="service-administrator"></a>服务管理员

服务管理员有权在 Azure 门户中管理服务，以及将用户分配到共同管理员角色。

## <a name="activate-your-enrollment"></a>激活注册

若要激活服务，最初的企业管理员需打开 Azure EA 门户 ([https://ea.azure.com](https://ea.azure.com))，并使用邀请电子邮件中的电子邮件地址登录。

如果你有多个注册，请选择要激活的注册。 默认只会显示活动的注册。 若要查看注册历史记录，请清除 Azure EA 门户右上角的“活动”选项。 

在“注册”下，状态将显示为“活动”。 

![显示活动注册的示例](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

只有现有的 Azure 企业管理员才能创建其他企业管理员。

### <a name="create-another-enterprise-admin"></a>创建另一个企业管理员

- 登录到 [Azure EA 门户](https://ea.azure.com)，导航到“管理” > “注册详细信息”，然后单击页面右上角的“+ 添加管理员”。   

确保已获取用户的电子邮件地址和首选的身份验证方法，例如，使用工作或学校帐户或者 Microsoft 帐户进行身份验证。 添加用户时需要此信息。

如果你不是 EA 管理员，请联系 EA 管理员，请求他们将你添加到注册。 被添加到注册后，你会收到一封激活电子邮件。

如果 EA 管理员无法为你提供帮助，请创建 [Azure EA 门户支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 提供以下信息：

- 注册编号
- 要添加的电子邮件地址和身份验证类型（工作、学校帐户或 Microsoft 帐户）
- 现有 EA 管理员做出的电子邮件批准
  - 如果无法联系到现有的 EA 管理员，请联系合作伙伴或软件顾问，请求他们通过 VLSC 工具更改联系人详细信息。

有关企业管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。

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

![显示“添加部门管理员”对话框的示例](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>设置只读访问权限

可向部门管理员授予只读访问权限。 创建新的部门管理员时：

- 将只读选项设置为“是”。 

若要编辑现有的部门管理员：

1. 选择部门，然后单击要编辑的“部门管理员”旁边的铅笔符号。 
2. 将只读选项设置为“是”。  然后单击“保存”。 

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

![显示帐户列表和“+ 添加帐户”选项的示例](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

可以单击“添加另一个帐户”来添加另一个帐户，或者单击左侧工具栏右下角的“添加”。  

若要确认帐户所有权：

1. 登录到 Azure EA 门户。
2. 通过查看状态来确认帐户所有权。 状态应从“挂起”更改为“开始/结束日期”。   开始/结束日期分别是用户首次登录的日期以及协议的结束日期。


## <a name="change-account-owner"></a>更改帐户所有者

企业管理员可以使用 Azure EA 门户来转移注册中的订阅帐户所有权。 该操作会将源用户帐户中的所有订阅转移到目标用户帐户。

有关转移用户帐户信息的要点：

- 支持从一个工作或学校帐户转移到另一个工作或学校帐户。
- 支持从 Microsoft 帐户转移到工作或学校帐户。
- 不支持从工作或学校帐户转移到 Microsoft 帐户。
- 支持从一个 Microsoft 帐户转移到另一个 Microsoft 帐户。 目标帐户必须是有效的 Azure 商务帐户才能成为有效的转移目标。 对于新帐户，当你登录到 Azure EA 门户时，系统会要求创建一个 Azure 商务帐户。 对于现有帐户，必须先创建新的 Azure 订阅，然后该帐户才符合条件。
- 完成订阅转移后，Microsoft 会更新帐户所有者。

RBAC 策略：

- 只有在同一租户中的两个组织 ID 之间进行的 Azure 订阅转移才会保留现有的 Azure 基于角色的访问控制 (RBAC) 策略、服务管理员角色分配和共同管理员角色分配。 其他订阅转移会导致 RBAC 策略以及服务管理员和共同管理员角色分配丢失。 策略和管理员角色不会在不同的目录之间转移。 服务管理员将更新为目标帐户的所有者。
- 在同一租户中的两个组织 ID 之间执行订阅转移时，会保留 RBAC 策略以及现有的服务管理员和共同管理员角色。

在更改帐户所有者之前：

1. 查看“帐户”选项卡并找到源帐户。  源帐户必须处于活动状态。
2. 找到目标帐户。 该帐户必须处于活动状态。

若要转移所有订阅的帐户所有权：

1. 在左侧导航区域中单击“管理”。 
2. 单击“帐户”选项卡，将鼠标悬停在某个帐户上。 
3. 单击右侧的“更改帐户所有者”符号。 这是一个人形符号。
4. 选择某个符合条件的帐户，然后单击“下一步”。 
5. 确认转移并单击“提交”。 

![显示“更改帐户所有者”符号的插图](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

若要转移单个订阅的帐户所有权：

1. 在左侧导航区域中单击“管理”。 
2. 单击“帐户”选项卡，将鼠标悬停在某个帐户上。 
3. 单击右侧的“转移订阅”符号。 该符号类似于一个页面。
4. 选择某个符合条件的订阅，然后单击“下一步”。 
5. 确认转移并单击“提交”。 

![显示“转移订阅”符号的插图](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

以下视频演示了 Azure EA 门户用户管理：

[Azure EA 门户用户管理视频](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>创建订阅

帐户所有者可以查看和管理订阅。 你可以使用订阅为组织中的团队授予对开发环境和项目的访问权限。 例如测试、生产、开发和过渡环境。 为每个应用程序环境创建不同的订阅可以帮助保护每个环境的安全。 还可以针对每个订阅分配不同的服务管理员帐户。 可将订阅关联到任意数量的服务。 帐户所有者可以创建订阅，并将服务管理员帐户分配到其帐户中的每个订阅。

### <a name="add-a-subscription"></a>添加订阅

使用以下信息添加订阅。

首次在帐户中添加订阅时，系统会要求你接受 MOSA 协议和费率计划。 尽管这些条款并不适用于企业协议客户，但创建订阅时需要接受它们。 Microsoft Azure 企业协议注册修正条款取代了上述条款，但你的契约关系并无变化。 出现提示时，请选中表示接受条款的框。

创建的所有新订阅使用默认订阅名称“Microsoft Azure Enterprise”。  可以更新订阅名称，使之与注册中的其他订阅相区分。 另外，请确保该名称在企业级别的报告中易于识别。

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

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>将即用即付订阅转移到 EA 订阅

若要将采用即用即付费率的单个订阅转移到 EA 订阅，必须在 Azure 门户中创建新的支持请求。 若要创建支持请求，请在“帮助和支持”区域单击“+ 新建支持请求”。 


## <a name="view-usage-summary-and-download-reports"></a>查看使用情况摘要和下载报告

企业管理员可以在 Azure EA 门户中查看其使用情况数据、货币承诺消耗量，以及与其他用途相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

查看特定帐户的详细使用情况

下载“使用情况详细信息”报告。 单击“报告”，然后单击“下载使用情况”选项卡。   在报告列表中，单击要获取的月份报告对应的“下载”。 

该报告不包含任何适用的税费。 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报告中。

查看使用情况摘要报告和图表：

1. 在 Azure EA 门户的左侧导航区域中，单击“报告”并查看“使用情况摘要”选项卡。    
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. 选择承诺条款。
3. 在页面右上角的“M”（每月）和“C”（自定义）之间切换可以根据自定义的开始日期和结束日期查看“使用情况摘要”。     
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. 在图表上选择某个时段或月份可以查看更多详细信息。
5. 图表显示了每月的使用情况，其中细分了使用量、服务额外费用、单独计收的费用和市场费用。
6. 对于选定的月份，可在图表下方按部门、帐户和订阅进行筛选。
7. 在“按服务列出的费用”与“按层次结构列出的费用”之间切换。  
8. 展开和折叠“Azure 服务”、“单独计收的费用”和“Azure 市场”可查看详细信息。   

以下视频演示了如何查看使用情况：

[Azure EA 门户使用情况视频](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以使用“月份报告下载”页将多份报告下载为 CSV 文件。 这些权限包括：

- 余额和费用
- 使用情况详细信息
- 市场费用
- 价目表

若要下载报告：


1. 在 Azure EA 门户中单击“报告”。 
2. 单击页面顶部的“下载使用情况”。 
3. 选择月份报告旁边的“下载”。 

从使用日期开始算起，最长可能需要延迟五天，其费用才会显示在报告中。

用户在使用 Safari 将 CSV 文件下载到 Excel 时可能会遇到格式错误。 若要避免错误，请使用文本编辑器打开文件。

![显示“下载使用情况”页的示例](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

以下视频演示了如何下载使用情况信息：

[Azure EA 门户使用情况视频](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>安排登记通话

若要与支持人员安排客户登记通话，请通过 [Azure EA 门户支持](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133)创建支持请求。 选择“登记”作为“问题类别”。  

## <a name="next-steps"></a>后续步骤
- Azure EA 门户管理员应阅读 [Azure EA 门户管理](billing-ea-portal-administration.md)来了解常见的管理任务。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](billing-ea-portal-troubleshoot.md)。
