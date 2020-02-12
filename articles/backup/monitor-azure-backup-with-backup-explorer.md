---
title: 用备份资源管理器监视备份
description: 本文介绍如何使用备份资源管理器对跨保管库、订阅、区域和租户的备份进行实时监视。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b65f68e33b53dff341ee72f6b9e9f42e344c49b1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149569"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>用备份资源管理器监视备份

当组织将更多的计算机备份到云时，有效监视这些备份将变得越来越重要。 要开始的最佳方式是使用一个中央位置查看跨大房产的操作信息。

备份资源管理器是一种内置 Azure Monitor 工作簿，该工作簿向 Azure 备份客户提供此单个中央位置。 备份资源管理器可帮助你监视 Azure 上整个备份空间内的运营活动，跨越租户、位置、订阅、资源组和保管库。 备份资源管理器广泛地提供以下功能：

* **大规模透视**：获取尚未在整个房地产中进行备份配置的备份项、作业、警报、策略和资源的汇总视图。 
* **向下钻取分析**：在一个位置显示有关每个作业、警报、策略和备份项的详细信息。
* 可**操作接口**：确定问题后，可通过无缝访问相关的备份项或 Azure 资源来解决该问题。

与 Azure 资源关系图和 Azure Monitor 工作簿的本机集成提供了这些功能。

> [!NOTE]
> * 备份资源管理器当前仅可用于 Azure 虚拟机（Vm）数据。
> * 备份资源管理器是一个操作面板，用于查看过去7天（最大值）的有关备份的信息。
> * 当前不支持自定义备份资源管理器模板。 
> * 建议不要在 Azure 资源关系图数据上编写自定义自动化。

## <a name="get-started"></a>入门

你可以访问备份资源管理器，方法是转到任何恢复服务保管库，然后在 "**概述**" 窗格中选择 "**备份资源管理器**" 链接。

![保管库快速链接](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

选择此链接将打开备份资源管理器，该资源管理器将提供你有权访问的所有保管库和订阅的聚合视图。 如果你使用的是 Azure Lighthouse 帐户，则可以查看你有权访问的所有租户中的数据。 有关详细信息，请参阅本文末尾的 "跨租户视图" 一节。

![备份资源管理器登录页](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>备份资源管理器用例

备份资源管理器将显示多个选项卡，每个选项卡提供有关特定备份项目（例如，备份项目、作业或策略）的详细信息。 本部分提供每个选项卡的简要概述。 视频提供每个备份项目的示例用例，以及可用控件的说明。

### <a name="the-summary-tab"></a>"摘要" 选项卡

"**摘要**" 选项卡可让你快速浏览备份空间的整体状况。 例如，你可以查看受保护的项目数、尚未启用保护的项目数或最近24小时内成功的作业数。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>"备份项" 选项卡

你可以按订阅、保管库和其他特性筛选和查看每个备份项。 通过选择备份项的名称，你可以打开该项目的 Azure 窗格。 例如，在表中，您可能会发现项*X*的上次备份失败。通过选择 " *X*"，您可以打开项的 "**备份**" 窗格，您可以在其中触发按需备份操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>"作业" 选项卡

选择 "**作业**" 选项卡以查看在过去7天内触发的所有作业的详细信息。 在此处，你可以按*作业操作*、*作业状态*和*错误代码*（对于失败的作业）进行筛选。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>"警报" 选项卡

选择 "**警报**" 选项卡，以查看在过去7天内在保管库中生成的所有警报的详细信息。 你可以按类型（*备份失败*或*还原失败*）筛选警报、当前状态（"*活动*" 或 "*已解决*"）和 "严重性" （*关键*、*警告*或*信息*）。 你还可以选择链接以转到 Azure VM，并执行任何必要的操作。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>"策略" 选项卡

你可以选择 "**策略**" 选项卡，以查看有关在备份空间中创建的所有备份策略的关键信息。 你可以查看与每个策略关联的项目数，以及策略指定的保持期和备份频率。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>"未启用备份" 选项卡

应为需要保护的所有计算机启用备份。 使用备份资源管理器，备份管理员可以快速确定组织中的哪些计算机尚未通过备份进行保护。 若要查看此信息，请选择 "**未启用备份**" 选项卡。

"**未启用备份**" 窗格显示一个表，其中包含未受保护的计算机的列表。 你的组织可能会将不同的标记分配给生产计算机和测试计算机，或者分配给提供多种功能的计算机。 由于计算机的每个类都需要一个单独的备份策略，因此按标记进行筛选有助于查看特定于每个的信息。 选择任何计算机的名称会将你重定向到该计算机的 "**配置" 备份**窗格，你可以在其中选择应用适当的备份策略。


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>导出到 Excel

可以将任何表或图表的内容导出为 Excel 电子表格。 内容将按原样导出，并应用现有筛选器。 若要导出其他表行，可以通过使用每个选项卡顶部的 "**每页的行**数" 下拉列表，增加要在页面上显示的行数。

## <a name="pin-to-the-dashboard"></a>固定到仪表板

可以在每个表或图表顶部选择 "固定" 图标，将其固定到 Azure 门户仪表板。 固定此信息可帮助您创建自定义的仪表板，以便显示最重要的信息。

## <a name="cross-tenant-views"></a>跨租户视图

如果你是具有跨多个租户环境的订阅的委托访问权限的 Azure Lighthouse 用户，则可以使用默认订阅筛选器。 通过选择 Azure 门户右上方的 "筛选器" 图标来显示要查看其数据的订阅。 使用此功能时，备份资源管理器会聚合所选订阅中所有保管库的相关信息。 若要了解详细信息，请参阅[什么是 Azure Lighthouse？](https://docs.microsoft.com/azure/lighthouse/overview)。

## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure Monitor 获取有关备份数据的见解](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
