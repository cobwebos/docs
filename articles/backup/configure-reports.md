---
title: 配置 Azure 备份报表
description: 使用 Log Analytics 和 Azure 工作簿配置及查看 Azure 备份报表
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e0c7418d7141a3b12f367f1b12ee740eaac64703
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797531"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

对备份管理员的一个常见要求是根据时间跨度较长的数据获取有关备份的见解。 此类解决方案的用例包括：

- 分配和预测使用的云存储空间。
- 审核备份和还原。
- 确定不同粒度级别的关键趋势。

如今，Azure 备份提供使用 [Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和 [Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)的报告解决方案。 这些资源有助于跨整个备份资产获得有关备份的丰富见解。 本文介绍如何配置和查看 Azure 备份报表。

## <a name="supported-scenarios"></a>支持的方案

- Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA、Microsoft Azure 恢复服务 (MARS) 代理、Microsoft Azure 备份服务器 (MABS) 和 System Center Data Protection Manager (DPM) 均支持备份报表。 Azure 文件共享备份的数据目前在备份报表中不可见。
- 对于 DPM 工作负荷，DPM 版本 5.1.363.0 及更高版本和代理版本 2.0.9127.0 及更高版本支持备份报表。
- 对于 MABS 工作负荷，MABS 版本 13.0.415.0 及更高版本和代理版本 2.0.9170.0 及更高版本支持备份报表。
- 只要将备份报表的数据发送到用户有权访问的 Log Analytics 工作区，就可以在所有备份项、保管库、订阅和区域中查看备份报表。 若要查看一组保管库的报表，只需具有对 Log Analytics 工作区（保管库将数据发送到该工作区）的读取访问权限即可。 无需具有对单个保管库的访问权限。
- 如果你是 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 用户并具有对客户订阅的委托访问权限，则可以将这些报表与 Azure Lighthouse 配合使用，以查看跨所有租户的报表。
- 目前可以在备份报表中查看最多 100 个 Log Analytics 工作区（跨租户）的数据。
- 报表中目前不显示日志备份作业的数据。

## <a name="get-started"></a>入门

执行以下步骤，开始使用报表。

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1.创建 Log Analytics 工作区或使用现有工作区

设置一个或多个 Log Analytics 工作区来存储备份报表数据。 可在其中创建此 Log Analytics 工作区的位置和订阅与保管库所在的位置和订阅无关。

若要设置 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

默认情况下，Log Analytics 工作区中的数据将保留 30 天。 若要查看更长时间范围的数据，请更改 Log Analytics 工作区的保持期。 若要更改保持期，请参阅[使用 Azure Monitor 日志管理使用情况和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2.配置保管库的诊断设置

Azure 资源管理器资源（例如恢复服务保管库）将有关计划的操作和用户触发的操作的信息记录为诊断数据。

在恢复服务保管库的“监视”部分，选择“诊断设置”并指定恢复服务保管库的诊断数据的目标。 若要了解有关使用诊断事件的详细信息，请参阅[使用恢复服务保管库的诊断设置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![诊断设置窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 备份还提供内置 Azure Policy 定义，该定义会自动配置给定范围内的所有保管库的诊断设置。 若要了解如何使用此策略，请参阅[大规模配置保管库诊断设置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)。

> [!NOTE]
> 配置诊断后，最多可能需要 24 小时才能完成初始数据推送。 数据开始流入 Log Analytics 工作区后，可能无法立即在报表中看到数据，因为报表中不显示目前未满一天的数据。 有关详细信息，请参阅[备份报表中使用的约定](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)。 我们建议在将保管库配置为向 Log Analytics 发送数据的两天后再开始查看报表。

#### <a name="3-view-reports-in-the-azure-portal"></a>3.在 Azure 门户中查看报表

将保管库配置为将数据发送到 Log Analytics 后，请转到任意保管库的窗格并选择“备份报表”，以查看备份报表。

![保管库仪表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

选择此链接可打开备份报表工作簿。

> [!NOTE]
>
> - 目前，报表的初始加载最多可能需要 1 分钟。
> - 恢复服务保管库只是备份报表的入口点。 从保管库的窗格打开备份报表工作簿后，请选择适当的 Log Analytics 工作区集以查看跨所有保管库聚合的数据。

报表包含各种选项卡：

- **汇总**：使用此选项卡可获取备份资产的简要概述。 你可以快速查看备份项的总数、使用的云存储空间总量、受保护实例的数量以及每种工作负荷类型的作业成功率。 有关特定备份项目类型的更多详细信息，请转到相应的选项卡。

   ![“摘要”选项卡](./media/backup-azure-configure-backup-reports/summary.png)

- **备份项**：使用此选项卡可以查看备份项级别使用的云存储空间的信息和趋势。 例如，如果在 Azure VM 备份中使用 SQL，则可以看到要备份的每个 SQL 数据库所使用的云存储空间。 也可以选择查看具有特定保护状态的备份项的数据。 例如，选择选项卡顶部的“保护已停止”磁贴会筛选下面的所有小组件，以仅显示处于“保护已停止”状态的备份项的数据。

   ![“备份项”选项卡](./media/backup-azure-configure-backup-reports/backup-items.png)

- **使用情况**：使用此选项卡可以查看备份的主要计费参数。 此选项卡上显示的信息属于计费实体（受保护容器）级别。 例如，在将 DPM 服务器备份到 Azure 的情况下，可以查看 DPM 服务器使用的受保护实例和云存储空间的趋势。 同样，如果在 Azure 备份中使用 SQL 或 SAP HANA，则此选项卡将提供包含这些数据库的虚拟机级别的使用情况相关信息。

   ![“使用情况”选项卡](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> 对于 DPM 工作负荷，用户可能会发现报表中显示的使用情况值与“恢复服务保管库概述”选项卡中显示的聚合使用情况值之间存在细微差异（每个 DPM 服务器相差大约 20 MB）。出现这种差异的原因在于，每个要注册备份的 DPM 服务器都有一个关联的“元数据”数据源，该数据源未作为报告项目显示。

- **作业**：使用此选项卡可以查看作业的长时间运行趋势，例如每天失败的作业数以及作业失败的主要原因。 可以在聚合级别和备份项级别查看此信息。 选择网格中的特定备份项，即可查看选定时间范围内在该备份项上触发的每个作业的详细信息。

   ![“作业”选项卡](./media/backup-azure-configure-backup-reports/jobs.png)

- **策略**：使用此选项卡可以查看有关所有活动策略的信息，例如关联项的数量以及根据给定策略备份的项所使用的云存储空间总量。 选择特定策略以查看与其关联的每个备份项的信息。

   ![“策略”选项卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>导出到 Excel

选择任何小组件（如表或图表）右上方的向下箭头按钮，即可在应用了现有筛选器的情况下将该小组件的内容按原样导出为 Excel 工作表。 若要将更多表行导出到 Excel，可以使用每个网格顶部的“每页行数”下拉箭头来增加页面上显示的行数。

## <a name="pin-to-dashboard"></a>固定到仪表板

选择每个小组件顶部的固定按钮，将小组件固定到 Azure 门户仪表板。 此功能可帮助创建自定义的仪表板，以显示所需的最重要的信息。

## <a name="cross-tenant-reports"></a>跨租户报表

如果使用 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 并具有可跨多个租户环境访问订阅的委托权限，则可以使用默认订阅筛选器。 选择 Azure 门户右上角的筛选器按钮，以选择要查看其数据的所有订阅。 这样一来，就可以跨租户选择 Log Analytics 工作区来查看多租户报表。

## <a name="conventions-used-in-backup-reports"></a>备份报表中使用的约定

- 筛选器在每个选项卡上从左向右、从上向下工作。也就是说，任何筛选器都只应用于位于该筛选器右侧或该筛选器下方的所有小组件。
- 选择彩色磁贴会筛选磁贴下方的小组件，以显示与该磁贴的值相关的记录。 例如，选择“备份项”选项卡上的“保护已停止”磁贴会筛选下方的网格和图表，以显示处于“保护已停止”状态的备份项的数据。
- 无法单击未着色的磁贴。
- 报表中不显示目前未满一天的数据。 因此，当“时间范围”的选定值为“过去 7 天”时，报表将显示过去完整的 7 天内的记录。 当前日期不包括在内。
- 报表显示选定时间范围内触发的作业（日志作业除外）的详细信息。
- 显示的“云存储空间”和“受保护实例”的值为选定时间范围结束时的值。
- 报表中显示的备份项为选定时间范围结束时的项。 不会显示在选定时间范围内删除的备份项。 同样的约定也适用于备份策略。

## <a name="query-load-times"></a>查询加载时间

备份报表中的小组件由 Kusto 查询提供支持，这些查询在用户的 Log Analytics 工作区中运行。 这些查询通常涉及处理大量数据，它们使用多个联接获得更丰富的见解。 因此，当用户查看跨大型备份资产的报表时，小组件可能不会立即加载。 此表根据备份项的数量和查看报表的时间范围，粗略估计了加载不同小组件所需的时间。

| **数据源数量**                         | **时间范围** | **大致加载时间**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| 约 5,000 个                       | 1 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级筛选器：5-10 秒|
| 约 5,000 个                       | 3 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级筛选器：5-10 秒|
| 约 10,000 个                       | 3 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：1-2 分钟 <br> 报表级筛选器：25-30 秒|
| 约 15,000 个                       | 1 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：50-60 秒 <br> 报表级筛选器：20-25 秒|
| 约 15,000 个                       | 3 个月          | 磁贴：20-30 秒 <br> 网格：20-30 秒 <br> 图表：2-3 分钟 <br> 报表级筛选器：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 报表有哪些变化？

- 早期用于报告的 Power BI 模板应用（从 Azure 存储帐户中获取数据）即将弃用。 我们建议开始将保管库诊断数据发送到 Log Analytics 以查看报表。

- 此外，将诊断数据发送到存储帐户或 LA 工作区的 [V1 架构](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema)也即将弃用。 这意味着，如果基于 V1 架构编写了任何自定义查询或自动化，则建议更新这些查询以使用当前支持的 V2 架构。

## <a name="next-steps"></a>后续步骤

[了解有关使用 Azure 备份进行监视和报告的详细信息](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
