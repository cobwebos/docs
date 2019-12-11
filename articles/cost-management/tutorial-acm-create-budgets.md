---
title: 教程 - 创建并管理 Azure 预算 | Microsoft Docs
description: 本教程介绍如何对所使用的 Azure 服务进行成本计划和核算。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 4b0e5be0e5f71fa61e3a6c6fa39e720c4c298898
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967928"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教程：创建并管理 Azure 预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，只会触发通知， 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。

成本和使用情况数据通常在12-16 小时内可用，每四个小时针对这些成本评估预算。 电子邮件通知通常在12-16 小时内收到。

在未来选择过期日期时，预算会在某个期间末（月末、季末或年末）自动重置为相同的预算金额。 由于预算是使用相同的预算金额重置的，因此如果未来时段的预算货币金额不同于现在，则需创建单独的预算。

本教程中的示例演示了如何针对 Azure 企业协议 (EA) 订阅创建和编辑预算。

若要了解如何在 Azure 中创建预算来监视支出，请观看[使用 Azure 门户视频将预算应用到订阅](https://www.youtube.com/watch?v=UrkHiUx19Po)。


本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

## <a name="prerequisites"></a>必备组件

各种 Azure 帐户类型都支持预算。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 若要查看预算，你至少需要对 Azure 帐户具有读取访问权限。

 对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。 若要创建和管理预算，必须具有参与者权限。 可以为 EA 订阅和资源组创建单独的预算。 但是，不能为 EA 计费帐户创建预算。

按用户和组对预算的每个订阅支持以下 Azure 权限或范围。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 门户中创建预算

可以按月、按季或按年创建 Azure 订阅预算。 Azure 门户中的导航内容决定了你是为订阅创建预算还是为管理组创建预算。

若要创建或查看预算，请在 Azure 门户中打开所需范围，并在菜单中选择“预算”。 例如，导航到 "**订阅**"，从列表中选择一个订阅，然后在菜单中选择 "**预算**"。 使用**范围**欣然可以在预算内切换到不同的作用域，如管理组。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

创建预算后，会显示一个简单的视图，其中说明了当前支出与预算的对比情况。

选择 **添加** 。

![显示已创建的预算列表的示例](./media/tutorial-acm-create-budgets/budgets01.png)

在“创建预算”窗口中，确保显示的范围正确无误。 选择任何要添加的筛选器。 使用筛选器可以创建针对特定成本的预算，如订阅中的资源组或虚拟机等服务。 任何可以在成本分析中使用的筛选器都可以应用于预算。

确定范围和筛选器后，键入预算名称。 然后，选择每月、每季度或每年预算重置时间段。 此重置周期确定由预算分析的时间范围。 预算评估的成本在每个新周期开始时为零。 创建季度预算时，其操作方式与每月预算相同。 不同之处在于，季度预算的预算金额是在该季度的三个月中平均分配的。 年度预算金额在日历年的所有 12 个月中平均分配。

如果有即用即付、MSDN 或 Visual Studio 订阅，则发票计费周期可能与日历月不一致。 对于这些订阅类型和资源组，可以创建一个与发票周期或日历月一致的预算。 若要创建与发票周期对齐的预算，请选择**计费月**、**帐单季度**或**帐单年**的重置时段。 若要创建与日历月对齐的预算，请选择**每月**、每**季度**或**每年**的重置期间。

下一步，确定预算失效并停止评估成本的到期日期。

基于目前在预算中选择的字段，系统会显示一张图表，帮助你选择用于预算的阈值。 建议的预算基于你未来可能会产生的最高预测成本。 你可以更改预算金额。

![使用每月成本数据创建预算的示例 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

配置预算金额后，选择“下一步”来配置预算警报。 预算需要至少一个成本阈值（预算百分比）和相应的电子邮件地址。 可以选择在单个预算中包括多达五个阈值和五个电子邮件地址。 当达到预算阈值时，通常会在20小时内收到电子邮件通知。 有关通知的详细信息，请参阅[使用成本警报](cost-mgt-alerts-monitor-usage-spending.md)。 在下面的示例中，达到预算的90% 时将生成电子邮件警报。 如果使用预算 API 创建预算，还可以将角色分配给人员来接收警报。 Azure 门户中不支持向人员分配角色。 有关 Azure 预算 API 的详细信息，请参阅[预算 api](/rest/api/consumption/budgets)。

![显示警报条件的示例](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

创建预算后，预算会显示在成本分析中。 开始[分析成本和支出](quick-acm-cost-analysis.md)时，首先要执行的步骤之一是查看预算与支出趋势的关系。

![成本分析中显示的预算和支出示例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上面的示例中，你为订阅创建了一个预算。 不过，也可为资源组创建预算。 如需为资源组创建预算，请导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“资源组”，接着选择一个资源组，单击“预算”，然后单击“添加”以添加预算。

## <a name="trigger-an-action-group"></a>触发操作组

在为订阅或资源组范围创建或编辑预算时，可以将其配置为调用操作组。 达到预算限额时，操作组可以执行各种不同的操作。 当前只有订阅和资源组作用域才支持操作组。 有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](../azure-monitor/platform/action-groups.md)。 有关将基于预算的自动化与操作组结合使用的详细信息，请参阅[使用 Azure 预算管理成本](../billing/billing-cost-management-budget-scenario.md)。



若要创建或更新操作组，请选择在创建或编辑预算时**管理操作组**。

![创建显示管理操作组的预算的示例](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


接下来，选择 "**添加操作组**" 并创建操作组。


!["添加操作组" 框的图像](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

创建操作组之后，请关闭该框以返回到预算。

将预算配置为在满足单个阈值时使用操作组。 支持最多五个不同的阈值。

![显示警报条件的操作组选择的示例](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

以下示例显示将预算阈值设置为50%、75% 和100%。 每个都配置为在指定的操作组中触发指定的操作。

![显示配置有各种操作组和操作类型的警报条件的示例](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

与操作组的预算集成仅适用于禁用常见警报架构的操作组。 有关禁用该架构的详细信息，请参阅[如何实现启用常见的警报架构？](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="edit-an-existing-budget"></a>编辑现有预算
如果要对现有预算进行更改，请导航到 "主预算" 窗口，并选择要编辑的预算。 在 "预算详细信息" 窗口中，选择 "**编辑预算**并做出必要的更改"，然后选择 "**保存**"。

![显示编辑预算的示例](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

请继续学习下一教程，了解如何针对成本管理数据创建定期导出。

> [!div class="nextstepaction"]
> [创建和管理导出的数据](tutorial-export-acm-data.md)
