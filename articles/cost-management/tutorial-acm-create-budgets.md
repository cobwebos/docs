---
title: 教程 - 创建并管理 Azure 预算 | Microsoft Docs
description: 本教程介绍如何对所使用的 Azure 服务进行成本计划和核算。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: edb46bc361c515439a93d9c3d0b9987bebe4b1b1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229876"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教程：创建并管理 Azure 预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，只会触发通知， 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。

Cost and usage data is typically available within 12-16 hours and budgets are evaluated against these costs every four hours. Email notifications are normally received within 12-16 hours.

在未来选择过期日期时，预算会在某个期间末（月末、季末或年末）自动重置为相同的预算金额。 由于预算是使用相同的预算金额重置的，因此如果未来时段的预算货币金额不同于现在，则需创建单独的预算。

本教程中的示例演示了如何针对 Azure 企业协议 (EA) 订阅创建和编辑预算。

Watch the [How to create a budget to monitor your spending with Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video to see how you can create budgets in Azure to monitor spending.


本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

## <a name="prerequisites"></a>必备组件

各种 Azure 帐户类型都支持预算。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 若要查看预算，你至少需要对 Azure 帐户具有读取访问权限。

 对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。 若要创建和管理预算，必须具有参与者权限。 可以为 EA 订阅和资源组创建单独的预算。 但是，不能为 EA 计费帐户创建预算。

The following Azure permissions, or scopes, are supported per subscription for budgets by user and group. 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 门户中创建预算

可以按月、按季或按年创建 Azure 订阅预算。 Your navigational content in the Azure portal determines whether you create a budget for a subscription or for a management group.

To create or view a budget, open the desired scope in the Azure portal and select **Budgets** in the menu. For example, navigate to **Subscriptions**, select a subscription from the list, and then select **Budgets** in the menu. Use the **Scope** pill to switch to a different scope, like a management group, in Budgets. 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

创建预算后，会显示一个简单的视图，其中说明了当前支出与预算的对比情况。

单击“添加”。

![Example showing a list of budgets already created](./media/tutorial-acm-create-budgets/budgets01.png)

In the **Create budget** window, make sure that the scope shown is correct. Choose any filters that you want to add. Filters allow you to create budgets on specific costs, such as resource groups in a subscription or a service like virtual machines. Any filter you can use in cost analysis can also be applied to a budget.

After you've identified your scope and filters, type a budget name. Then, choose a monthly, quarterly or annual budget reset period. This reset period determines the time window that's analyzed by the budget. The cost evaluated by the budget starts at zero at the beginning of each new period. 创建季度预算时，其操作方式与每月预算相同。 不同之处在于，季度预算的预算金额是在该季度的三个月中平均分配的。 An annual budget amount is evenly divided among all 12 months of the calendar year.

如果有即用即付、MSDN 或 Visual Studio 订阅，则发票计费周期可能与日历月不一致。 For those subscription types and resource groups, you can create a budget that's aligned to your invoice period or to calendar months. To create a budget aligned to your invoice period, select a reset period of **Billing month**, **Billing quarter**, or **Billing year**. To create a budget aligned to the calendar month, select a reset period of **Monthly**, **Quarterly**, or **Annually**.

Next, identify the expiration date when the budget becomes invalid and stops evaluating your costs.

Based on the fields chosen in the budget so far, a graph is shown to help you select a threshold to use for your budget. The suggested budget is based on the highest forecasted cost that you might incur in future periods. You can change the budget amount.

![Example showing budget creation with monthly cost data ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

After you configure the budget amount, click **Next** to configure budget alerts. 预算需要至少一个成本阈值（预算百分比）和相应的电子邮件地址。 可以选择在单个预算中包括多达五个阈值和五个电子邮件地址。 When a budget threshold is met, email notifications are normally received in less than 20 hours. 有关通知的详细信息，请参阅[使用成本警报](cost-mgt-alerts-monitor-usage-spending.md)。 In the example below, an email alert gets generated when 90% of the budget is reached. If you create a budget with the Budgets API, you can also assign roles to people to receive alerts. Assigning roles to people isn't supported in the Azure portal. For more about the Azure budgets API, see [Budgets API](/rest/api/consumption/budgets).

![Example showing alert conditions](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

创建预算后，预算会显示在成本分析中。 开始[分析成本和支出](quick-acm-cost-analysis.md)时，首先要执行的步骤之一是查看预算与支出趋势的关系。

![成本分析中显示的预算和支出示例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上面的示例中，你为订阅创建了一个预算。 不过，也可为资源组创建预算。 如需为资源组创建预算，请导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“资源组”，接着选择一个资源组，单击“预算”，然后单击“添加”以添加预算。

## <a name="trigger-an-action-group"></a>Trigger an action group

When you create or edit a budget for a subscription or resource group scope, you can configure it to call an action group. The action group can perform a variety of different actions when your budget threshold is met. Action Groups are currently only supported for subscription and resource group scopes. For more information about Action Groups, see [Create and manage action groups in the Azure portal](../azure-monitor/platform/action-groups.md). For more information about using budget-based automation with action groups, see [Manage costs with Azure budgets](../billing/billing-cost-management-budget-scenario.md).



To create or update action groups, click **Manage action groups** while you're creating or editing a budget.

![Example of creating a budget to show Manage action groups](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Next, click **Add action group** and create the action group.


![Image of the Add action group box](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

After the action group is created, close the box to return to your budget.

Configure your budget to use your action group when an individual threshold is met. Up to five different thresholds are supported.

![Example showing action group selection for an alert condition](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

The following example shows budget thresholds set to 50%, 75% and 100%. Each is configured to trigger the specified actions within the designated action group.

![Example showing alert conditions configured with various action groups and type of actions](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budget integration with action groups only works for action groups that have the common alert schema disabled. For more information about disabling the schema, see [How do I enable the common alert schema?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

请继续学习下一教程，了解如何针对成本管理数据创建定期导出。

> [!div class="nextstepaction"]
> [创建和管理导出的数据](tutorial-export-acm-data.md)
