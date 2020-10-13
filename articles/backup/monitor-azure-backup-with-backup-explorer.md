---
title: 使用备份资源管理器监视备份
description: 本文介绍如何使用备份资源管理器跨保管库、订阅、区域和租户实时监视备份。
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: a50b9ee05be48113221f2a12f968540bd3a00b3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88824406"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>使用备份资源管理器监视备份

随着组织将越来越多的计算机备份到云中，有效监视这些备份变得越来越重要。 要开始监视，最好的方法是在一个中心位置跨大型资产查看操作信息。

备份资源管理器是一个内置的 Azure Monitor 工作簿，它将这个单一中心位置提供给 Azure 备份客户。 备份资源管理器可帮助你跨 Azure 上整个备份资产监视操作活动，范围包括租户、位置、订阅、资源组和保管库。 大致来说，备份资源管理器提供以下功能：

* **大规模透视**：获取尚未为跨整个资产的备份配置的备份项、作业、警报、策略和资源的聚合视图。
* **深化分析**：在同一位置显示关于每个作业、警报、策略和备份项的详细信息。
* **可操作接口**：确定问题后，可无缝访问相关备份项或 Azure 资源来解决该问题。

通过与 Azure Resource Graph 和 Azure Monitor 工作簿的本机集成，这些功能现成可用。

> [!NOTE]
>
> * 备份资源管理器当前仅可用于 Azure 虚拟机 (VM) 数据。
> * 备份资源管理器旨在充当可操作的仪表板，用于查看过去 7 天（上限）备份的相关信息。
> * 国家云当前不支持备份资源管理器。
> * 当前不支持自定义备份资源管理器模板。
> * 不建议在 Azure 资源关系图数据上编写自定义自动化。
> * 当前，可通过备份资源管理器监视最多跨 1,000 个订阅（跨租户）的备份。

## <a name="get-started"></a>入门

可转到任何恢复服务保管库，然后在“概述”窗格中，选择“备份资源管理器”链接来访问该资源管理器 。

![保管库快速链接](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

选择此链接将打开备份资源管理器，后者跨你有权访问的所有保管库和订阅提供了聚合视图。 如果使用的是 Azure Lighthouse 帐户，则可查看你有权访问的所有租户中的数据。 有关详细信息，请参阅本文末尾的“跨租户视图”部分。

![备份资源管理器登陆页面](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>备份资源管理器用例

备份资源管理器显示多个选项卡，每个选项卡均提供有关特定备份项目（例如备份项、作业或策略）的详细信息。 本部分提供每个选项卡的简要概述。 视频提供了每个备份项的示例用例及可用控件的说明。

### <a name="the-summary-tab"></a>“摘要”选项卡

通过“摘要”选项卡，可快速浏览备份资产的整体状况。 例如，可查看受保护的项目数、尚未启用保护的项目数或最近 24 小时内成功的作业数。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>“备份项”选项卡

可按订阅、保管库和其他特性筛选和查看每个备份项。 通过选择备份项的名称，可打开该项的 Azure 窗格。 例如，你可能会在表中看到上次对项 X 的备份失败。选择 X 可打开该项的“备份”窗格，在其中触发按需备份操作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>“作业”选项卡

选择“作业”选项卡可查看过去 7 天内触发的所有作业的详细信息。 可在这里根据作业操作、作业状态和错误代码（针对失败的作业）进行筛选  。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>“警报”选项卡

选择“警报”选项卡可查看过去 7 天内在保管库中生成的所有警报的详细信息。 可根据类型（“备份失败”或“还原失败”）、当前状态（“活动”或“已解决”）和严重性（“严重”、“警告”或“信息性”）对警报进行筛选      。 还可选择链接转到 Azure VM 并执行任何必要的操作。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>“策略”选项卡

可选择“策略”选项卡来查看有关跨备份资产创建的所有备份策略的关键信息。 可查看与每个策略关联的项目数，以及策略所指定的保持期和备份频率。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>“未启用备份”选项卡

应为所有需要保护的计算机启用备份。 使用备份资源管理器，备份管理员可以快速确定组织中的哪些计算机尚未通过备份进行保护。 要查看此信息，请选择“未启用备份”选项卡。

“未启用备份”窗格会显示一张表，其中包含未受保护的计算机的列表。 你的组织可能会将不同的标记分配给生产计算机和测试计算机，或者分配给提供多种功能的计算机。 由于每类计算机都需要一个单独的备份策略，因此按标记进行筛选有助于查看每个类别特定的信息。 选择任何计算机的名称都会将你重定向到该计算机的“配置备份”窗格，可在其中选择应用相应的备份策略。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>导出到 Excel

可将任何表或图表的内容导出为 Excel 电子表格。 内容将按原样导出，并应用现有筛选器。 若要导出其他表行，可使用每个选项卡顶部的“每页行数”下拉列表来增加要在页上显示的行数。

## <a name="pin-to-the-dashboard"></a>固定到仪表板

可选择每张表或图表顶部的“固定”图标，将其固定到 Azure 门户仪表板。 固定此信息可帮助你创建自定义仪表板，它专门用于显示对你而言最重要的信息。

## <a name="cross-tenant-views"></a>跨租户视图

如果你是一名 Azure Lighthouse 用户，且具有委托权限可跨多个租户环境访问订阅，那么你可使用默认订阅筛选器。 通过选择 Azure 门户右上方的“筛选器”图标，可显示要查看其数据的订阅。 使用此功能时，备份资源管理器会将跨所选订阅的所有保管库的相关信息聚合在一起。 要了解详细信息，请参阅[什么是 Azure Lighthouse？](../lighthouse/overview.md)。

## <a name="next-steps"></a>后续步骤

[了解如何使用 Azure Monitor 获取有关备份数据的见解](./backup-azure-monitoring-use-azuremonitor.md)
