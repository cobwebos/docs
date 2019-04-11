---
title: 教程 - 创建并管理 Azure 预算 | Microsoft Docs
description: 本教程介绍如何对所使用的 Azure 服务进行成本计划和核算。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469482"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教程：创建和管理 Azure 预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，只会触发通知， 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。

每月预算将根据每四个小时的支出进行评估。 但是，消耗资源的数据和通知可在八小时内获得。  

在未来选择过期日期时，预算会在某个期间末（月末、季末或年末）自动重置为相同的预算金额。 由于预算是使用相同的预算金额重置的，因此如果未来时段的预算货币金额不同于现在，则需创建单独的预算。

本教程中的示例演示了如何针对 Azure 企业协议 (EA) 订阅创建和编辑预算。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

## <a name="prerequisites"></a>必备组件

各种 Azure 帐户类型都支持预算。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 若要查看预算，你至少需要对 Azure 帐户具有读取访问权限。

 对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。 若要创建和管理预算，必须具有参与者权限。 可以为 EA 订阅和资源组创建单独的预算。 但是，不能为 EA 计费帐户创建预算。

以下 Azure 权限作用域，支持或预算对于每个订阅按用户和组。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 门户中创建预算

可以按月、按季或按年创建 Azure 订阅预算。 在 Azure 门户中导航的内容确定是否创建订阅或管理组的预算。

若要创建或查看在预算，打开在 Azure 门户并选择所需的范围**预算**菜单中。 例如，导航到**订阅**，从列表中，选择一个订阅，然后选择**预算**菜单中。 使用**作用域**药丸切换到不同的作用域，如预算中的管理组。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

创建预算后，会显示一个简单的视图，其中说明了当前支出与预算的对比情况。

单击“添加”。

![Azure 门户中显示的成本管理预算](./media/tutorial-acm-create-budgets/budgets01.png)

在“创建预算”窗口中，输入预算名称和预算金额。 然后，选择月、季或年作为预算期限。 接下来，选择结束日期。 预算需要至少一个成本阈值（预算百分比）和相应的电子邮件地址。 可以选择在单个预算中包括多达五个阈值和五个电子邮件地址。 当满足预算阈值时，通常将在八小时内接收到电子邮件通知。 有关通知的详细信息，请参阅[使用成本警报](cost-mgt-alerts-monitor-usage-spending.md)。

如果你有一个即用即付、 MSDN 或 Visual Studio 订阅，发票计费周期可能不会与日历月份一致。 对于这些类型的订阅和资源组，可以创建对齐的预算，到你的发票期或日历月。 若要创建与你的发票期对齐的预算，选择重置期间的计费月、 计费季度或计费年。 若要创建与日历月份一致的预算，请选择重置期间的每月、 每季度或每年一次。

下面是一个示例，说明了如何创建金额为 4,500 美元的每月预算。 达到预算的 90% 时，会生成电子邮件警报。

![“创建预算”框中显示的示例信息](./media/tutorial-acm-create-budgets/monthly-budget01.png)

创建季度预算时，其操作方式与每月预算相同。 不同之处在于，季度预算的预算金额是在该季度的三个月中平均分配的。 如你所料，年度预算金额是在日历年的所有 12 个月中平均分配的。

只要成本管理收到更新的计费数据，就会更新与预算相对应的当前支出。 通常会每日更新一次。

![显示当前预算支出的示例信息](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

创建预算后，预算会显示在成本分析中。 开始[分析成本和支出](quick-acm-cost-analysis.md)时，首先要执行的步骤之一是查看预算与支出趋势的关系。

![成本分析中显示的预算和支出示例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上面的示例中，你为订阅创建了一个预算。 不过，也可为资源组创建预算。 如需为资源组创建预算，请导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“资源组”，接着选择一个资源组，单击“预算”，然后单击“添加”以添加预算。

## <a name="edit-a-budget"></a>编辑预算

可以通过编辑预算来更改其属性，具体取决于你所具有的访问权限的级别。 在以下示例中，某些属性为只读属性，因为用户只有订阅的“参与者”权限。 目前，“过期日期”处于禁用状态，一旦设置即无法修改。

![编辑预算以更改各种属性的示例](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>触发器的操作组

当创建或编辑订阅或资源组范围内的预算时，您可以将其配置为调用一个操作组。 当满足你预算的阈值时，操作组可以执行各种不同的操作。 有关操作组详细信息，请参阅[创建和管理在 Azure 门户中的操作组](../azure-monitor/platform/action-groups.md)。 有关操作组包含使用基于预算的自动化的详细信息，请参阅[使用 Azure 预算管理成本](../billing/billing-cost-management-budget-scenario.md)。

若要创建或更新操作组，请单击**管理操作组**时你正在创建或编辑在预算。

![创建预算以显示管理操作组的示例](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

接下来，单击**添加操作组**和创建操作组。


![添加操作组中的图像](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

组创建操作后，关闭该对话框，以返回到你的预算。

配置你的预算以满足各个阈值时使用操作组。 支持多达五个不同的阈值。

![显示警报条件的操作组选择示例](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

下面的示例列出了预算阈值设置为 50%、 75%和 100%。 每个配置为触发指定的操作组中指定的操作。

![显示配置了各种操作组和操作的类型的警报条件示例](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

请继续学习下一教程，了解如何针对成本管理数据创建定期导出。

> [!div class="nextstepaction"]
> [创建和管理导出的数据](tutorial-export-acm-data.md)
