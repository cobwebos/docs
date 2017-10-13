---
title: "在 Azure 成本管理中查看使用情况和成本 | Microsoft 文档"
description: "查看使用情况和成本以跟踪趋势、检测低效情况并创建警报。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/19/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 4440ca81545978ab7438f1684ce71ded24c326e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="review-usage-and-costs"></a>查看使用情况和成本

通过 Cloudyn 提供的 Azure 成本管理将显示使用情况和成本，以便可以跟踪趋势、检测低效情况并创建警报。 所有使用情况和成本数据都将显示在 Cloudyn 仪表板和报表中。 本教程中将举例说明如何使用仪表板和报表查看使用情况和成本。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 跟踪使用情况和成本趋势
> * 检测低效使用情况
> * 针对异常支出或超支情况创建警报



## <a name="open-the-cloudyn-portal"></a>打开 Cloudyn 门户

可以在 Cloudyn 门户中查看所有使用情况和成本。 可以从 Azure 门户中打开 Cloudyn 门户，也可以导航到 https://app.cloudyn.com 并登录打开。

## <a name="track-usage-and-cost-trends"></a>跟踪使用情况和成本趋势

通过“随着时间推移”报表跟踪使用情况和成本的实际开支来确定趋势。 若要开始查看趋势，请使用“实际成本随着时间推移”报表。 在门户顶部的报表菜单中，单击“成本” > “成本分析” > “实际成本随着时间推移”。 首次打开报表时，不会对其应用组或筛选器。

下面是一个示例报表：

![示例报表](./media/tutorial-review-usage/actual-cost01.png)

此报表显示过去 30 天的所有支出。 若要仅查看 Azure 服务的支出，请应用“服务”组，然后针对所有 Azure 服务进行筛选。 下图显示了筛选出的服务。

![筛选出的服务](./media/tutorial-review-usage/actual-cost02.png)

在上面的示例中，从 2017 年 8 月 31 日开始，支出较比之前的要少。 该成本趋势针对各种服务持续了大约九天。 然后，其他支出像以前那样继续。 但是，列数过多可能会使趋势不明显。 可以将报表视图更改为折线图或面积图，以查看在其他视图中显示的数据。 下图更清楚地显示了这一趋势。

![报表中的趋势](./media/tutorial-review-usage/actual-cost03.png)

在该示例中，可以清楚地看到 Azure 存储成本从 2017 年 8 月 31 日起开始下降，而其他 Azure 服务的支出持平。 那么，是什么导致了支出缩减呢？ 在此示例中，某些员工正在休假，因而没有使用存储服务。

## <a name="detect-usage-inefficiencies"></a>检测低效使用情况

优化器报表可提高效率、优化使用情况以及找到在云资源上节省开支的方式。 对于旨在帮助减少空闲或昂贵 VM 方面，它们特别有助于给出经济高效的大小调整建议。

当组织最初将资源移动到云中时，影响组织的常见问题是其虚拟化策略。 他们通常使用类似于用于本地虚拟化环境创建虚拟机的方法。 而且，他们认为通过将本地 VM 移动到云就可降低成本。 但是，这种方法不可能降低成本。

问题在于他们已为现有的基础结构支付了费用。 如果用户愿意，他们可以创建非常大的 VM，并保持它们空运行或不运行而得不到有效利用。 将非常大的或空闲的 VM 移到云中可能会增加成本。 与云服务提供商达成协议时，各资源的成本分配非常重要。 无论你充分使用资源还是不使用，都必须为你获得的资源支付费用。

“经济高效的大小调整建议”报表通过将 VM 实例类型容量与其 CPU 和内存使用情况的历史数据相比较，确定出每年可能节省的费用。  

在门户顶部的报表菜单中，单击“优化器” > “定价优化” > “经济高效的大小调整建议”。 将提供商筛选为 Azure 以便仅查看 Azure VM。 下面是一个示例图像：

![Azure VM](./media/tutorial-review-usage/sizing01.png)

在此示例中，按照建议更改 VM 实例类型可以节省 3,114 美元。 单击“详细信息”下的加号 (+) 获取第一个建议。 以下是有关第一个建议的详细信息。

![建议详细信息](./media/tutorial-review-usage/sizing02.png)

单击“候选项列表”旁的加号以查看 VM 实例 ID。

![候选项列表](./media/tutorial-review-usage/sizing03.png)

## <a name="create-alerts-for-unusual-spending"></a>针对异常支出创建警报

你可以针对支出异常和超支风险自动向利益干系人发出警报。 你可以使用支持基于预算和成本阈值的警报报表快速轻松地创建警报。

可以使用任何成本报表针对任何支出创建警报。 在此示例中，当 Azure VM 支出接近总预算时，使用“实际成本随着时间推移”报表通知你。 在门户顶部的报表菜单中，单击“成本” > “成本分析” > “实际成本随着时间推移”。 将“组”设置为“服务”，并将“按服务筛选”设置为“Azure/VM”。 在报表的右上角，单击“操作”，然后选择“计划报表”。

使用“计划”选项卡以所需频率向自己发送报表电子邮件。 你所用的任何标记、分组和筛选均包含在通过电子邮件发送的报表中。 单击“阈值”选项卡，然后选择“实际成本与阈值”。 如果你的总预算为 500,000 美元，并且希望在成本接近大约一半时获得通知，则在 250,000 美元创建“红色警报”，并在 240,000 美元创建“黄色警报”。 然后，选择连续警报的数目。 当你收到的警报总数达到指定数目时，就不会再发送任何其他警报。 保存计划的报表。

![示例报表](./media/tutorial-review-usage/schedule-alert01.png)

还可以选择“成本百分比与预算”阈值指标来创建警报。 通过使用该指标，可以使用预算百分比，而不是货币值。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 跟踪使用情况和成本趋势
> * 检测低效使用情况
> * 针对异常支出或超支情况创建警报


请转到下一教程，了解对数据访问的控制。

> [!div class="nextstepaction"]
> [控制对数据的访问](tutorial-user-access.md)
