---
title: 配置 Azure 备份报表
description: 使用 Log Analytics 和 Azure 工作簿配置和查看 Azure 备份报告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: c1af9a532b390b428e74957c455988dfd4df3967
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184939"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

备份管理员的一个常见要求是，根据跨越长时间的数据获取有关备份的见解。 此类解决方案的用例包括：

- 已使用的云存储的分配和预测。
- 审核备份和还原。
- 确定不同粒度级别的关键趋势。

如今，Azure 备份提供了一种使用[Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)的报表解决方案。 这些资源可帮助你获取跨整个备份空间的备份的丰富见解。 本文介绍如何配置和查看 Azure 备份报表。

## <a name="supported-scenarios"></a>支持的方案

- Azure vm、azure vm 中的 SQL、Azure Vm 中的 SAP HANA/ASE、Microsoft Azure 恢复服务（MARS）代理、Microsoft Azure 备份服务器（MABS）和 System Center Data Protection Manager （DPM）支持备份报表。 Azure 文件共享备份的数据当前在备份报表中不可见。
- 对于 DPM 工作负荷，DPM 版本5.1.363.0 和更高版本以及代理版本2.0.9127.0 及更高版本支持备份报表。
- 对于 MABS 工作负荷，MABS 版本13.0.415.0 和更高版本以及代理版本2.0.9170.0 及更高版本支持备份报表。
- 只要备份项的数据发送到用户有权访问的 Log Analytics 工作区，就可以跨所有备份项、保管库、订阅和区域查看备份报表。 若要查看一组保管库的报表，只需具有对保管库发送其数据的 "Log Analytics" 工作区的 "读取者" 访问权限。 您无需具有对单个保管库的访问权限。
- 如果你是有权访问客户订阅的[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/)用户，则可以将这些报告与 azure Lighthouse 配合使用来查看所有租户的报告。
- 日志备份作业的数据当前未显示在报表中。

## <a name="get-started"></a>入门

按照以下步骤开始使用报告。

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. 创建 Log Analytics 的工作区或使用现有工作区

设置一个或多个 Log Analytics 工作区来存储备份报表数据。 可以创建此 Log Analytics 工作区的位置和订阅独立于保管库所在的位置和订阅。

若要设置 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。

默认情况下，Log Analytics 工作区中的数据将保留30天。 若要查看更长时间范围内的数据，请更改 "Log Analytics" 工作区的保持期。 若要更改保持期，请参阅[使用 Azure Monitor 日志管理使用情况和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. 为保管库配置诊断设置

Azure 资源管理器资源，如恢复服务保管库，记录有关计划操作和用户触发的操作的信息（作为诊断数据）。

在恢复服务保管库的 "监视" 部分，选择 "**诊断设置**" 并指定恢复服务保管库的诊断数据的目标。 若要了解有关使用诊断事件的详细信息，请参阅[使用恢复服务保管库的诊断设置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![诊断设置窗格](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 备份还提供内置的 Azure 策略定义，该定义自动配置给定范围内所有保管库的诊断设置。 若要了解如何使用此策略，请参阅[大规模配置保管库诊断设置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)。

> [!NOTE]
> 配置诊断后，最长可能需要24小时才能完成初始数据推送。 数据开始流向 Log Analytics 工作区后，你可能不会立即看到报表中的数据，因为报表中没有显示当前部分日期的数据。 有关详细信息，请参阅[备份报表中使用的约定](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)。 建议你在将保管库配置为将数据发送到 Log Analytics 后，开始查看报表两天。

#### <a name="3-view-reports-in-the-azure-portal"></a>3. 查看 Azure 门户中的报表

将保管库配置为将数据发送到 Log Analytics 后，请转到任一保管库的窗格并选择 "**备份报表**" 来查看备份报表。

![保管库仪表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

选择此链接可打开备份报表工作簿。

> [!NOTE]
>
> - 当前，报表的初始加载最多可能需要1分钟。
> - 恢复服务保管库只是备份报表的入口点。 在 "备份报表" 工作簿从保管库的窗格打开后，选择适当的一组 Log Analytics 工作区，以查看跨所有保管库聚合的数据。

报表包含各种选项卡：

- **摘要**：使用此选项卡可大致了解备份空间。 您可以快速浏览备份项总数、使用的云存储总数、受保护实例的数目，以及每个工作负荷类型的作业成功率。 有关特定备份项目类型的更多详细信息，请参阅相应的选项卡。

   ![“摘要”选项卡](./media/backup-azure-configure-backup-reports/summary.png)

- **备份项**：使用此选项卡查看在备份项级别使用的云存储的信息和趋势。 例如，如果在 Azure VM 备份中使用 SQL，则可以查看正在备份的每个 SQL 数据库使用的云存储空间。 你还可以选择查看特定保护状态的备份项的数据。 例如，选择选项卡顶部的 "**保护已停止**" 磁贴将筛选下面的所有小组件，以便仅显示处于保护停止状态的备份项的数据。

   ![备份项选项卡](./media/backup-azure-configure-backup-reports/backup-items.png)

- **用法**：使用此选项卡查看备份的按键帐单参数。 此选项卡上显示的信息是一个计费实体（受保护容器）级别。 例如，在将 DPM 服务器备份到 Azure 的情况下，你可以查看 DPM 服务器的受保护实例和云存储的使用趋势。 同样，如果使用 azure 备份中的 SQL 或 Azure 备份中的 SAP HANA，则此选项卡将提供包含这些数据库的虚拟机级别的使用情况相关信息。

   ![用法选项卡](./media/backup-azure-configure-backup-reports/usage.png)

- **作业**：使用此选项卡可以查看作业的长时间运行趋势，例如每天失败的作业数，以及失败的原因的主要原因。 可以在聚合级别和备份项级别查看此信息。 选择网格中的特定备份项，查看在所选时间范围内该备份项上触发的每个作业的详细信息。

   ![“作业”选项卡](./media/backup-azure-configure-backup-reports/jobs.png)

- **策略**：使用此选项卡可以查看所有活动策略的相关信息，例如，关联的项目数和在给定策略中备份的项目所消耗的云存储总量。 选择特定策略以查看其每个相关备份项的相关信息。

   ![策略选项卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>导出到 Excel

选择任何小组件右上角的向下箭头按钮（如表或图表），以将该小组件的内容导出为 Excel 工作表，并将其应用于现有筛选器。 若要将表的更多行导出到 Excel 中，可以通过使用每个网格顶部的 "**每页行**数" 下拉箭头，增加页面上显示的行数。

## <a name="pin-to-dashboard"></a>固定到仪表板

选择每个小组件顶部的 "固定" 按钮，将小组件固定到 Azure 门户仪表板。 此功能可帮助你创建自定义的仪表板，以便显示所需的最重要的信息。

## <a name="cross-tenant-reports"></a>跨租户报告

如果对跨多个租户环境的订阅使用[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) ，则可以使用默认订阅筛选器。 选择 Azure 门户右上角的 "筛选器" 按钮，选择要查看其数据的所有订阅。 这样做使您可以选择在租户中 Log Analytics 工作区，以查看 multitenanted 报告。

## <a name="conventions-used-in-backup-reports"></a>备份报表中使用的约定

- 筛选器的工作方式是从左到右，从上到下。也就是说，任何筛选器仅适用于所有位于该筛选器或该筛选器下方的小组件。
- 选择彩色磁贴会筛选磁贴下面的小组件，查找与该图块的值相关的记录。 例如，选择 "**备份项**" 选项卡上的 "**已停止保护**" 磁贴将筛选下面的网格和图表，以显示处于保护停止状态的备份项的数据。
- 未着色的磁贴不能单击。
- 报表中不显示当前部分的数据。 因此，当 "**时间范围**" 的选定值为 "**最近7天**" 时，该报表将显示最近七天内的记录。 当前日期不包括在内。
- 该报表显示在所选时间范围内*触发*的作业的详细信息（除日志作业之外）。
- 为**云存储**和**受保护实例**显示的值位于所选时间范围的*末尾*。
- 报表中显示的备份项是在所选时间范围*结束*时存在的项。 不会显示在所选时间范围内删除的备份项。 同样，也适用于备份策略。

## <a name="query-load-times"></a>查询加载时间

备份报表中的小组件由 Kusto 查询提供支持，这些查询在用户的 Log Analytics 工作区中运行。 这些查询通常涉及处理大量数据，具有多个联接以实现更丰富的见解。 因此，当用户跨大型备份空间查看报表时，小组件可能不会立即加载。 此表基于备份项的数量和查看报表的时间范围，提供了不同小组件可用于加载的时间的粗略估计。

| **数据源数**                         | **时间范围** | **大致加载时间**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| 约 5 K                       | 1 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级别筛选器：5-10 秒|
| 约 5 K                       | 3 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级别筛选器：5-10 秒|
| 约 10 K                       | 3 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：1-2 分钟 <br> 报表级别筛选器：25-30 秒|
| 约 15 K                       | 1 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：50-60 秒 <br> 报表级别筛选器：20-25 秒|
| 约 15 K                       | 3 个月          | 磁贴：20-30 秒 <br> 网格：20-30 秒 <br> 图表：2-3 分钟 <br> 报表级别筛选器：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 报表发生了什么？

- 用于报告的早期 Power BI 模板应用（从 Azure 存储帐户获取数据）位于弃用路径上。 建议你开始将保管库诊断数据发送到 Log Analytics 以查看报表。

- * 此外，将诊断数据发送到存储帐户或 LA 工作区的[V1 架构](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema)也位于弃用路径上。 这意味着，如果你已经编写了基于 V1 架构的任何自定义查询或自动化，则建议更新这些查询以使用当前支持的 V2 架构。

## <a name="next-steps"></a>后续步骤

[详细了解如何在 Azure 备份中进行监视和报告](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
