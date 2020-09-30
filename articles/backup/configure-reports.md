---
title: 配置 Azure 备份报表
description: 使用 Log Analytics 和 Azure 工作簿配置和查看 Azure 备份的报表
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: d40da1ebd87ef7d7a43d0be9ae0d34911e854d0e
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567428"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

备份管理员的一个常见需求是根据时间跨度较长的数据获取有关备份的见解。 此类解决方案的用例包括：

- 分配和预测需使用的云存储空间。
- 审核备份和还原。
- 确定不同粒度级别的关键趋势。

如今，Azure 备份提供了一种使用 [Azure Monitor 日志](../azure-monitor/log-query/get-started-portal.md)和 [Azure 工作簿](../azure-monitor/platform/workbooks-overview.md)的报表解决方案。 这些资源有助于你跨整个备份资产获得有关备份的丰富见解。 本文介绍如何配置和查看 Azure 备份报表。

## <a name="supported-scenarios"></a>支持的方案

- Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA、Microsoft Azure 恢复服务 (MARS) 代理、Microsoft Azure 备份服务器 (MABS) 和 System Center Data Protection Manager (DPM) 均支持备份报表。 对于 Azure 文件共享备份，将显示在2020年6月1日或之后创建的所有记录的数据。
- 对于 Azure 文件共享备份，受保护实例上的数据当前不会显示在报表中 (默认值为零，所有备份项) 。
- 对于 DPM 工作负载，DPM 版本 5.1.363.0 及更高版本和代理版本 2.0.9127.0 及更高版本支持备份报表。
- 对于 MABS 工作负载，MABS 版本 13.0.415.0 及更高版本和代理版本 2.0.9170.0 及更高版本支持备份报表。
- 只要将备份报告的数据发送到用户有权访问的 Log Analytics 工作区，就可以在所有备份项目、保管库、订阅和区域中查看备份报表。 若要查看一组保管库的报表，只需具有对 Log Analytics 工作区（保管库将数据发送到该工作区）的读取访问权限。 无需具有对单个保管库的访问权限。
- 如果你是 [Azure Lighthouse](../lighthouse/index.yml) 用户并具有对客户订阅的委托访问权限，则可以将这些报表与 Azure Lighthouse 配合使用，以查看跨所有租户的报表。
- 目前可以在备份报表中查看最多 100 个 Log Analytics 工作区（跨租户）的数据。
- 报表中目前不显示日志备份作业的数据。

## <a name="get-started"></a>入门

请遵循以下步骤开始使用报表。

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1.创建 Log Analytics 工作区或使用现有工作区

设置一个或多个 Log Analytics 工作区来存储备份报表数据。 可以创建此 Log Analytics 工作区的位置和订阅与保管库所处的位置和订阅无关。

若要设置 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

默认情况下，Log Analytics 工作区中的数据将保留 30 天。 若要查看更长时间范围的数据，请更改 Log Analytics 工作区的保持期。 若要更改保持期，请参阅[通过 Azure Monitor 日志管理使用情况和成本](../azure-monitor/platform/manage-cost-storage.md)。

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2.为保管库配置诊断设置

恢复服务保管库等 Azure 资源管理器资源会记录有关计划的操作和用户触发的操作的信息作为诊断数据。

在“恢复服务保管库”的“监视”部分，选择“诊断设置”并指定恢复服务保管库的诊断数据的目标。 若要了解有关使用诊断事件的详细信息，请参阅[对恢复服务保管库使用诊断设置](./backup-azure-diagnostic-events.md)。

![诊断设置窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 备份还提供内置 Azure Policy 定义，该定义会自动配置给定范围内的所有保管库的诊断设置。 若要了解如何使用此策略，请参阅[大规模配置保管库诊断设置](./azure-policy-configure-diagnostics.md)。

> [!NOTE]
> 配置诊断后，可能需要 24 小时才能完成初始数据推送。 数据开始流入 Log Analytics 工作区后，可能无法立即在报表中看到数据，因为报表中不显示当前未满一天的数据。 有关详细信息，请参阅[备份报表中使用的约定](#conventions-used-in-backup-reports)。 建议在将保管库配置为将数据发送到 Log Analytics 的两天后开始查看报表。

#### <a name="3-view-reports-in-the-azure-portal"></a>3.在 Azure 门户中查看报表

将保管库配置为将数据发送到 Log Analytics 后，请转到任何保管库的窗格并选择“备份报表”来查看备份报表。

![保管库仪表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

选择此链接可打开备份报表工作簿。

> [!NOTE]
>
> - 目前，报表的初始加载可能需要 1 分钟。
> - 恢复服务保管库仅仅是备份报表的入口点。 从保管库的窗格打开备份报表工作簿后，请选择适当的 Log Analytics 工作区集以查看跨所有保管库聚合的数据。

报表包含各种选项卡：

##### <a name="summary"></a>总结

使用此选项卡可大致了解备份空间。 你可以快速查看备份项的总数、使用的云存储空间总量、受保护实例的数量以及每种工作负载类型的作业成功率。 有关特定备份项目类型的详细信息，请转到相应的选项卡。

   ![“摘要”选项卡](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>备份项

使用此选项卡查看在备份项级别使用的云存储的信息和趋势。 例如，如果在 Azure VM 备份中使用 SQL，则可以看到正在备份的每个 SQL 数据库所使用的云存储空间。 你还可以选择查看特定保护状态的备份项的数据。 例如，选择选项卡顶部的“保护已停止”磁贴将对下面的小组件进行筛选，只显示处于“保护已停止”状态的备份项的数据。

   ![“备份项”选项卡](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>使用情况

使用此选项卡查看备份的按键帐单参数。 此选项卡上显示的信息可归于计费实体（受保护容器）级别。 例如，如果要将 DPM 服务器备份到 Azure，则可以查看 DPM 服务器使用的受保护实例和云存储的趋势。 同样，如果在 Azure 备份中使用 SQL，或者在 Azure 备份中使用 SAP HANA，此选项卡将在包含这些数据库的虚拟机级别上提供与使用情况相关的信息。

   ![“使用情况”选项卡](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> 对于 DPM 工作负荷，用户可能会发现，在报表中显示的使用值与 "恢复服务保管库 **概述** " 选项卡中所示的聚合使用值相比，在报表中所示的使用值之间，每个 DPM) 服务器 (的顺序略有不同。这种差异的原因在于，为备份注册的每个 DPM 服务器都有关联的 "元数据" 数据源，而不会将其视为报告的项目。

##### <a name="jobs"></a>作业

使用此选项卡可以查看作业的长时间运行趋势，例如每天失败的作业数，以及作业失败的主要原因。 你可以在聚合级别和备份项级别查看此信息。 选择网格中的特定备份项，即可查看在选定时间范围内该备份项上触发的每个作业的详细信息。

   ![“作业”选项卡](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>策略

使用此选项卡查看所有活动策略的相关信息，例如，关联的项目数和在给定策略中备份的项目所消耗的云存储总量。 选择特定策略以查看与其关联的每个备份项的信息。

   ![“策略”选项卡](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>优化

使用此选项卡可查看备份的潜在成本优化机会。 下面是 "优化" 选项卡当前提供见解的方案：

###### <a name="inactive-resources"></a>非活动资源

使用此视图，您可以识别在很长一段时间内未成功备份的那些备份项。 这可能意味着正在备份的底层计算机不再存在 (，因此会导致备份失败) ，或在阻止备份被可靠处理的计算机上出现一些问题。

若要查看非活动资源，请导航到 " **优化** " 选项卡，然后选择 " **非活动资源** " 磁贴。 选择此磁贴会显示一个网格，其中包含所选范围内的所有非活动资源的详细信息。 默认情况下，此网格显示在过去7天内没有恢复点的项。 若要在不同时间范围内查找非活动资源，可以在该选项卡的顶部调整 **时间范围** 筛选器。

确定非活动资源后，可通过导航到该资源的 "备份项" 仪表板或 "Azure 资源" 窗格 (，无论是否适用) ，都可以进一步调查该问题。 根据你的方案，你可以选择停止 (计算机的备份（如果不存在），) 并删除不必要的备份，这将节省成本，或者你可以修复计算机中的问题，以确保备份可靠。

![优化选项卡-非活动资源](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>保留期较长的备份项

使用此视图，你可以识别其备份保留时间比组织所需时间长的项目。

选择 " **策略优化** " 磁贴，然后选择 " **保留优化** " 磁贴，将显示一个网格，其中包含每日、每周、每月或每年保留点 (RP) 的保留期大于指定值的所有备份项。 默认情况下，网格将显示所选作用域中的所有备份项。 你可以使用筛选器进行每日、每周、每月和每年 RP 保留，以便进一步筛选网格，并识别可能降低保留期以节省备份存储成本的项目。

对于数据库工作负荷（如 SQL 和 SAP HANA），网格中显示的保持期对应于完整备份点的保留期，而不是与差异备份点相对应的保留期。 同样还适用于保留筛选器。  

![优化选项卡-保留优化](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>针对每日完整备份配置的数据库

使用此视图，可以标识已配置为进行每日完整备份的数据库工作负载。 通常，将每日差异备份与每周完整备份一起使用更为经济高效。

选择后跟 "**备份计划优化**" 磁贴的 "**策略优化**" 磁贴将显示一个网格，其中包含具有每日完整备份策略的所有数据库。 你可以选择导航到特定的备份项，并修改策略以使用每日差异备份和每周完整备份。

选项卡顶部的 " **备份管理类型** 筛选器" 应在 **azure vm 中** 选择 "SQL"， **在 azure vm 中** 选择 "SAP HANA"，以便网格能够按预期方式显示数据库工作负荷。

![优化选项卡-备份计划优化](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

## <a name="export-to-excel"></a>导出到 Excel

选择任何小组件（如表或图表）右上角的向下箭头按钮，并在应用了现有筛选器的情况下，将该小组件的内容按原样导出为 Excel 工作表。 若要将表的更多行导出到 Excel，可以使用每个网格顶部的“每页行数”下拉箭头来增加页面上显示的行数。

## <a name="pin-to-dashboard"></a>固定到仪表板

选择每个小组件顶部的固定按钮，将小组件固定到 Azure 门户仪表板。 此功能可帮助创建自定义的仪表板，以显示所需的最重要的信息。

## <a name="cross-tenant-reports"></a>跨租户报表

如果使用 [Azure Lighthouse](../lighthouse/index.yml) 并具有可跨多个租户环境访问订阅的委托权限，则可以使用默认订阅筛选器。 选择 Azure 门户右上角的筛选器按钮，以选择要查看其数据的所有订阅。 这样一来，就可以跨租户选择 Log Analytics 工作区来查看多租户报表。

## <a name="conventions-used-in-backup-reports"></a>备份报表中使用的约定

- 筛选器在每个选项卡上从左到右、从上到下工作。也就是说，任何筛选器都只应用于位于筛选器右侧或筛选器下方的所有小组件。
- 选择彩色磁贴将筛选磁贴下面的小组件，仅显示与该磁贴的值相关的记录。 例如，选择备份项选项卡上的“保护已停止”磁贴将筛选下面的网格和图表，以显示处于“保护已停止”状态的备份项的数据 。
- 未着色的磁贴无法选择。
- 报表中不显示当前未满一天的数据。 因此，当选定的“时间范围”值为“过去 7 天”时，报告将显示过去完整的 7 天内的记录 。 当前日期不包括在内。
- 报表显示在选定的时间范围内触发的作业（日志作业除外）的详细信息。
- 显示的“云存储空间”和“受保护的实例”的值为选定时间范围结束时的值 。
- 报表中显示的备份项为选定时间范围结束时的项。 不会显示在选定时间范围中间删除的备份项。 同样的约定也适用于备份策略。

## <a name="query-load-times"></a>查询加载时间

备份报表中的小组件由 Kusto 查询提供支持，该查询在用户的 Log Analytics 工作区上运行。 这些查询通常涉及处理大量数据，它们使用多个连接来获得更丰富的见解。 因此，当用户跨大型备份资产查看报表时，小组件可能不会立即加载。 此表根据备份项的数量和查看报表的时间范围，粗略估计了加载不同的小组件所需的时间。

| **数据源数量**                         | **时间范围** | **大致加载时间**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级筛选器：5-10 秒|
| ~5 K                       | 3 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级筛选器：5-10 秒|
| ~10 K                       | 3 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：1-2 分钟 <br> 报表级筛选器：25-30 秒|
| ~15 K                       | 1 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：50-60 秒 <br> 报表级筛选器：20-25 秒|
| ~15 K                       | 3 个月          | 磁贴：20-30 秒 <br> 网格：20-30 秒 <br> 图表：2-3 分钟 <br> 报表级筛选器：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 报表有哪些变化？

- 早期用于报表的 Power BI 模板应用（从 Azure 存储帐户中获取数据）即将弃用。 我们建议开始将保管库诊断数据发送到 Log Analytics 以查看报表。

- 此外，将诊断数据发送到存储帐户或 LA 工作区的 [V1 架构](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema)也即将弃用。 这意味着，如果你已经编写了基于 V1 架构的任何自定义查询或自动化，则建议更新这些查询以使用当前支持的 V2 架构。

## <a name="next-steps"></a>后续步骤

[详了解与 Azure 备份相关的监视和报表](./backup-azure-monitor-alert-faq.md)
