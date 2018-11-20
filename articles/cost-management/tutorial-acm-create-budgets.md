---
title: 教程 - 创建并管理 Azure 预算 | Microsoft Docs
description: 本教程介绍如何对所使用的 Azure 服务进行成本计划和核算。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 49341b320df98bb08ee4f5c4ee061a51bec29ff2
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686154"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教程：创建并管理 Azure 预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，只会触发通知， 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。

在未来选择过期日期时，预算会在某个期间末（月末、季末或年末）自动重置为相同的预算金额。 由于预算是使用相同的预算金额重置的，因此如果未来时段的预算货币金额不同于现在，则需创建单独的预算。

本教程中的示例演示了如何针对 Azure 企业协议 (EA) 订阅创建和编辑预算。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

## <a name="prerequisites"></a>先决条件

预算适用于所有 Azure EA 客户。 必须有 Azure EA 订阅的读取访问权限才能创建并管理预算。 可以为 EA 订阅和资源组创建单独的预算。 但是，不能为 EA 计费帐户创建预算。

每个订阅支持以下 Azure 权限，以便按用户和组进行预算：

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者 - 可以根据权限查看预算。

## <a name="sign-in-to-azure"></a>登录 Azure

- 通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 门户中创建预算

可以按月、按季或按年创建 Azure 订阅预算。 Azure 门户中的导航内容决定了你是为订阅还是为资源组创建预算。

在 Azure 门户中，导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“预算”。 在此示例中，创建的预算是针对所选订阅的。

创建预算后，会显示一个简单的视图，其中说明了当前支出与预算的对比情况。

单击“添加”。

![成本管理预算](./media/tutorial-acm-create-budgets/budgets01.png)

在“创建预算”窗口中，输入预算名称和预算金额。 然后，选择月、季或年作为预算期限。 接下来，选择结束日期。 预算需要至少一个成本阈值（预算百分比）和相应的电子邮件地址。 可以选择在单个预算中包括多达五个阈值和五个电子邮件地址。 当满足预算阈值时，通常将在八小时内接收到电子邮件通知。

下面是一个示例，说明了如何创建金额为 4,500 美元的每月预算。 达到预算的 90% 时，会生成电子邮件警报。

![每月预算示例](./media/tutorial-acm-create-budgets/monthly-budget01.png)

创建季度预算时，其操作方式与每月预算相同。 不同之处在于，季度预算的预算金额是在该季度的三个月中平均分配的。 如你所料，年度预算金额是在日历年的所有 12 个月中平均分配的。

只要成本管理收到更新的计费数据，就会更新与预算相对应的当前支出。 通常会每日更新一次。

![当前支出与预算的对比](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

创建预算后，预算会显示在成本分析中。 开始[分析成本和支出](quick-acm-cost-analysis.md)时，首先要执行的步骤之一是查看预算与支出趋势的关系。

![在成本分析中显示的预算](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上面的示例中，你为订阅创建了一个预算。 不过，也可为资源组创建预算。 如需为资源组创建预算，请导航到“成本管理 + 计费”&gt;“订阅”，选择一个订阅，然后选择“资源组”，接着选择一个资源组，单击“预算”，然后单击“添加”以添加预算。

## <a name="edit-a-budget"></a>编辑预算

可以通过编辑预算来更改其属性，具体取决于你所具有的访问权限的级别。 在以下示例中，某些属性为只读属性，因为用户只有订阅的“参与者”权限。 目前，“过期日期”处于禁用状态，一旦设置即无法修改。

![编辑预算 - 参与者权限](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 门户中创建预算
> * 编辑预算

请继续学习下一教程，了解如何针对成本管理数据创建定期导出。

> [!div class="nextstepaction"]
> [创建和管理导出的数据](tutorial-export-acm-data.md)
