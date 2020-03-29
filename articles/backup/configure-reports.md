---
title: 配置 Azure 备份报表
description: 使用日志分析和 Azure 工作簿配置和查看 Azure 备份的报告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 651d1383f0f292895ed95c91bafd5206d4f04c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161195"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

备份管理员的常见要求是根据跨越很长时间的数据获取备份见解。 此类解决方案可能有多个用例 - 分配和预测消耗的云存储，审核备份和还原，并在不同粒度级别识别关键趋势。

如今，Azure 备份提供了一个报告解决方案，该解决方案利用[Azure 监视器日志](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)，帮助您在整个备份区获得有关备份的丰富见解。 本文介绍如何配置和查看备份报告。

## <a name="supported-scenarios"></a>支持的方案

* Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA/ASE、Azure 备份代理 （MARS）、Azure 备份服务器 （MABS） 和系统中心 DPM 都支持备份报告。
* 对于 DPM 工作负载，DPM 版本 5.1.363.0 及以上支持备份报告，以及代理版本 2.0.9127.0 及以上。
* 对于 MABS 工作负载，MABS 版本 13.0.415.0 及以上支持备份报告，以及代理版本 2.0.9170.0 及以上。
* 只要备份报告的数据发送到用户有权访问的日志分析 （LA） 工作区，就可以跨所有备份项目、保管库、订阅和区域查看备份报告。 
* 如果您是 Azure[灯塔](https://docs.microsoft.com/azure/lighthouse/)用户，具有对客户订阅的委派访问权限，则可以使用 Azure 灯塔的这些报表查看所有租户的报表。
* 日志备份作业的数据当前未显示在报表中。

## <a name="getting-started"></a>入门

要开始使用报表，请按照以下三个步骤进行操作：

1. **创建日志分析 （LA） 工作区（或使用现有工作区）：**

您需要设置一个或多个 LA 工作区来存储备份报告数据。 可以创建此 LA 工作区的位置和订阅与存在保管库的位置和订阅无关。 

请参阅以下文章：在[Azure 门户中创建日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)以设置 LA 工作区。

默认情况下，LA 工作区中的数据将保留 30 天。 要查看时间跨度较长的数据，更改 LA 工作区的保留期。 要更改保留期，请参阅以下文章：[使用 Azure 监视器日志管理使用情况和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

2. **为保管库配置诊断设置：**

Azure 资源管理器资源（如恢复服务保管库）将有关计划工序和用户触发操作的信息记录为诊断数据。 

在恢复服务保管库的监视部分中，选择**诊断设置**并指定恢复服务保管库的诊断数据的目标。 [了解有关使用诊断事件的更多信息](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

![诊断设置边栏选项卡](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 备份还提供内置 Azure 策略，它自动配置给定作用域中所有保管库的诊断设置。 请参阅以下文章以了解如何使用此策略：[大规模配置保管库诊断设置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

> [!NOTE]
> 配置诊断后，可能需要长达 24 小时才能完成初始数据推送。 数据开始流入 LA 工作区后，可能无法立即在报表中看到数据，因为当前部分日的数据不会显示在报表中（[此处](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports)的更多详细信息）。 因此，建议在配置保管库以将数据发送到日志分析 2 天后开始查看报告。

3. **在 Azure 门户上查看报表：**

将保管库配置为将数据发送到洛杉矶后，通过导航到任何保管库的边栏选项卡并单击 **"备份报告"** 菜单项来查看备份报告。 

![保管库仪表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

单击此链接将打开备份报表工作簿。

> [!NOTE]
> * 目前，报告的初始负载可能需要长达 1 分钟。
> * 恢复服务保管库只是备份报告的一个入口点。 从保管库的边栏选项卡打开备份报表工作簿后，您将能够查看跨所有保管库聚合的数据（通过选择适当的 LA 工作区集）。

下面是报表包含的各种选项卡的说明：

* **摘要**- "摘要"选项卡提供备份空间的高级概述。 在"摘要"选项卡下，您可以快速查看备份项总数、消耗的云存储总数、受保护实例数以及每个工作负载类型的作业成功率。 有关特定备份项目类型的更多详细信息，导航到相应的选项卡。

![“摘要”选项卡](./media/backup-azure-configure-backup-reports/summary.png)

* **备份项目**- "备份项目"选项卡允许您查看在备份项目级别使用的云存储信息和趋势。 例如，如果在 Azure VM 备份中使用 SQL，则可以看到要备份的每个 SQL 数据库都使用的云存储。 您还可以选择查看特定保护状态的备份项目的数据。 例如，单击选项卡顶部的"**保护停止"** 磁贴，筛选以下所有小部件，以仅显示处于"保护停止"状态的备份项目的数据。

![备份项目选项卡](./media/backup-azure-configure-backup-reports/backup-items.png)

* **使用情况**- "使用情况"选项卡可帮助您查看备份的关键计费参数。 此选项卡中显示的信息位于计费实体（受保护容器）级别。 例如，在 DPM 服务器备份到 Azure 的情况下，您可以查看 DPM 服务器使用的受保护实例和云存储的趋势。 同样，如果您在 Azure 备份中使用 SQL 或在 Azure 备份中使用 SAP HANA，此选项卡在这些数据库中包含的虚拟机级别提供与使用情况相关的信息。

![使用情况选项卡](./media/backup-azure-configure-backup-reports/usage.png)

* **作业**- "作业"选项卡允许您查看作业的长期运行趋势，例如每天失败的作业数和作业失败的首要原因。 您可以在聚合级别和备份项目级别查看此信息。 单击网格中的特定备份项，您可以查看所选时间范围内对该备份项上触发的每个作业的详细信息。

![“作业”选项卡](./media/backup-azure-configure-backup-reports/jobs.png)

* **策略**- "策略"选项卡允许您查看所有活动策略的信息，例如关联项目的数量，以及根据给定策略备份的项目使用的总云存储。 单击特定策略可以查看有关其每个关联备份项的信息。

![策略选项卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>导出到 Excel

单击任何小部件（表/图表）右上角的向下箭头按钮，将该小部件的内容导出为 Excel 工作表，按现有筛选器应用。 要将表的更多行导出到 Excel，可以使用每个网格顶部的 **"每页行**"下拉列表来增加页面上显示的行数。

## <a name="pinning-to-dashboard"></a>固定到仪表板

单击每个小部件顶部的"固定图标"，将小部件固定到 Azure 门户仪表板。 这有助于您创建定制的仪表板，以显示所需的最重要的信息。

## <a name="cross-tenant-reports"></a>交叉租户报告

如果您是 Azure[灯塔](https://docs.microsoft.com/azure/lighthouse/)用户，具有对跨多个租户环境的订阅的委派访问权限，则可以使用默认订阅筛选器（通过单击 Azure 门户右上角的筛选器图标）选择要查看数据的所有订阅。 这样做将允许您跨租户选择 LA 工作区以查看多租户报表。

## <a name="conventions-used-in-backup-reports"></a>备份报告中使用的约定

* 筛选器在每个选项卡上从左到右和从上到下工作，也就是说，任何筛选器仅适用于定位在该筛选器右侧或筛选器下方的所有小部件。 
* 单击彩色磁贴可筛选磁贴下方的微件，以查找与该磁贴的值相关的记录。 例如，单击"备份项目"选项卡中的 *"保护停止*"磁贴会筛选下面的网格和图表，以显示处于"保护已停止"状态的备份项目的数据。
* 未着色的磁贴不可单击。
* 报表中不显示当前未满一天的数据。 因此，当 **"时间范围**"的选定值***为"最近 7 天***"时，报表将显示最近 7 个已完成天数（不包括当前日期）的记录。
* 该报告显示在选定时间范围内**触发**的作业（日志作业外）的详细信息。 
* 云存储和受保护实例显示的值位于所选时间范围的**末尾**。
* 报表中显示的备份项目是在所选时间范围**末尾**存在的项。 不会显示在选定时间范围中间删除的备份项目。 相同的约定也适用于备份策略。

## <a name="query-load-times"></a>查询加载时间

备份报告中的小部件由 Kusto 查询提供支持，这些查询在用户的 LA 工作区上运行。 由于这些查询通常涉及处理大量数据，以及多个联接以启用更丰富的见解，因此当用户跨大型备份区查看报表时，小部件可能无法立即加载。 下表根据备份项数和查看报表的时间范围，粗略估计了不同小部件加载的时间：

| **• 数据源**                         | **时间地平线** | **加载时间（约）**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ±5 K                       | 1 个月          | 瓷砖： 5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报告级过滤器：5-10 秒|
| ±5 K                       | 3 个月          | 瓷砖： 5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报告级过滤器：5-10 秒|
| Φ10 K                       | 3 个月          | 瓷砖： 15-20 秒 <br> 网格：15-20 秒 <br> 图表： 1-2 分钟 <br> 报告级过滤器：25-30 秒|
| Φ15 K                       | 1 个月          | 瓷砖： 15-20 秒 <br> 网格：15-20 秒 <br> 图表：50-60 秒 <br> 报告级过滤器：20-25 秒|
| Φ15 K                       | 3 个月          | 瓷砖： 20-30 秒 <br> 网格：20-30 秒 <br> 图表： 2-3 分钟 <br> 报告级过滤器：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 报告发生了什么情况？
* 我们较早的 Power BI 模板应用用于报告（从 Azure 存储帐户获取数据）位于弃用路径上。 建议开始发送保管库诊断数据到日志分析如上所述，以查看报告。

* 此外，将诊断数据发送到存储帐户或 LA 工作区的 V1 架构也位于弃用路径上。 这意味着，如果您已根据 V1 架构编写了任何自定义查询或自动化，则建议您更新这些查询以使用当前支持的 V2 架构。

## <a name="next-steps"></a>后续步骤
[了解有关使用 Azure 备份进行监视和报告的更多内容](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)