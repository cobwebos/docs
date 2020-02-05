---
title: 用备份资源管理器监视备份
description: 介绍如何使用备份资源管理器对跨保管库、订阅、区域和租户执行备份进行实时监视的文章
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992178"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>用备份资源管理器监视备份

当组织将更多的计算机备份到云时，有一个中心位置来查看有关跨大场地的备份的操作信息是非常重要的，以便有效监视备份。

备份资源管理器工作簿是一种内置 Azure Monitor 工作簿，使备份客户能够在 Azure 上执行与备份相关的操作监视活动（跨租户、位置、订阅、资源组和保管库）都从一个中心位置进行。 它广泛地提供了以下功能：

* **大规模透视**-备份项、作业、警报、策略和未配置为在整个备份空间进行备份的资源的聚合视图。 
* **向下钻取分析**–可以选择从单个位置获取每个实体（作业、警报、策略和备份项）的详细信息。
* 可**操作接口**–确定问题后，可以通过无缝导航到备份项或 Azure 资源来选择执行操作。

使用 Azure 资源关系图和 Azure Monitor 工作簿的本机集成提供了上述功能。

> [!NOTE]
> * 备份资源管理器当前仅对 Azure VM 数据可用。
> * 备份资源管理器是一个操作面板，用于查看过去7天（最大值）的有关备份的信息。
> * 当前不支持自定义备份资源管理器模板。 此外，我们目前不建议在 Azure 资源关系图数据上编写自定义自动化。

## <a name="getting-started"></a>入门

可以通过导航到任何恢复服务保管库并单击 "**概述**" 页中的 "**备份资源管理器**" 链接来访问备份资源管理器。

![保管库快速链接](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

单击此链接将打开备份资源管理器，其中提供了你有权访问的所有保管库和订阅的聚合视图。 如果你使用的是 Azure Lighthouse 帐户，则可以查看你有权访问的所有租户中的数据（有关详细信息，请参阅以下部分中的跨租户视图）。

![资源管理器登录页](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>备份资源管理器用例

备份资源管理器由多个选项卡组成，每个选项卡提供有关特定类型的备份项目的详细信息，例如备份项、作业、策略等。本部分提供每个选项卡的简要概述。 视频提供每个选项卡的示例用例，以及对用户可用的各种控件的说明。

### <a name="summary"></a>摘要

使用 "摘要" 选项卡，可以快速浏览备份空间的整体状况。 你可以查看信息，如受保护的项目数、尚未启用保护的项数、最近24小时内成功的作业数，等等。 

其他每个选项卡都代表一个不同的实体，例如：备份项、备份作业、备份警报和备份策略。 你还可以查看有关未配置备份的计算机的信息。 单击其中任一选项卡将显示特定于该实体的信息。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>备份项

可以查看按订阅、保管库和其他参数筛选的每个备份项。 单击备份项的名称可打开该备份项的 Azure 边栏选项卡。 例如，在表中，您可能会发现项 "X" 的上次备份失败。 单击 "X" 将打开此项目的 "备份" 边栏选项卡，你可以在其中触发按需备份操作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>工作

通过导航到 "作业" 选项卡，可以查看过去7天触发的所有作业的详细信息。在这里，你可以按作业操作、作业状态和错误代码（如果作业失败）进行筛选。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>警报

通过单击 "警报" 选项卡，可以查看在过去7天内在保管库上触发的所有警报的详细信息。在此处，你可以按警报类型筛选记录（例如，备份失败、还原失败）、警报的当前状态（例如 "活动" 或 "已解决"）和警报严重性（例如，"严重"、"警告"、"信息"）。 还可以通过单击 VM 的链接导航到 Azure VM，并执行必要的操作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>策略

单击 "策略" 选项卡可以查看在备份空间中创建的所有备份策略的关键信息。 你可以查看与每个策略关联的项目数，以及每个策略指定的保持期和备份频率。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>未启用备份

组织的备份管理员必须快速确定组织中的哪些计算机尚未启用备份，以便为需要保护的所有计算机启用备份，这一点非常重要。 单击 "**未启用备份**" 选项卡可帮助你在此任务中进行。

在此选项卡中，你将看到一个表，其中包含不受保护的项的列表。 如果你的组织遵循将不同标记分配给生产计算机和测试计算机的做法，或为不同的功能提供服务的计算机，则每个操作可能都需要单独的备份策略，并按标记进行筛选有助于查看特定于特定的计算机类别。 单击任何项的名称会将你重定向到该项的 "**配置备份**" 边栏选项卡，你可以在其中选择使用适当的备份策略来备份该项。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>导出到 Excel

单击任意小组件右上角的向下箭头按钮（表/图表）会将该小组件的内容导出为 Excel 工作表，就像现有筛选器一样。 若要将表的更多行导出到 Excel 中，可以使用每个选项卡顶部的 "**每页行**数" 下拉列表来增加页面上显示的行数。

## <a name="pinning-to-dashboard"></a>固定到仪表板

可以单击每个小组件顶部的固定图标，将该小组件固定到 Azure 门户仪表板。 这可以帮助你创建自定义的仪表板以显示你需要的最重要的信息。

## <a name="cross-tenant-views"></a>跨租户视图

如果你是拥有跨多个租户环境的订阅的委托访问权限的 Azure Lighthouse 用户，则可以使用默认订阅筛选器（通过单击 Azure 门户的右上方的 "筛选器" 图标），选择你想要的所有订阅查看的数据。 这样做将使备份资源管理器可以在这些订阅上聚合所有保管库上的信息。 [在此处](https://docs.microsoft.com/azure/lighthouse/overview)了解有关 Azure Lighthouse 的详细信息。

## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure Monitor 获取有关备份数据的见解](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)