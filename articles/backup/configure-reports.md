---
title: 配置 Azure 备份报表
description: 使用 Log Analytics 和 Azure 工作簿配置和查看 Azure 备份报告
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 5c43efc8d61d7aa6c8fc94c6c39294d744cd6b87
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501116"
---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表

备份管理员的常见要求是根据长时间跨越的数据获取有关备份的见解。 此类解决方案可能有多个用例，即分配和预测使用的云存储、审核备份和还原，以及确定不同粒度级别的关键趋势。

如今，Azure 备份提供了一种利用[Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)和[Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)的报表解决方案，帮助你在整个备份空间中获得有关备份的丰富见解。 本文介绍如何配置和查看备份报表。

## <a name="supported-scenarios"></a>支持的方案

* Azure vm 中的 SQL、azure vm 中的 SQL SAP HANA、azure Vm、Azure 备份代理（MARS）、Azure 备份服务器（MABS）和 System Center DPM 支持备份报表。
* 对于 DPM 工作负荷，DPM 版本5.1.363.0 和更高版本以及代理版本2.0.9127.0 及更高版本支持备份报表。
* 对于 MABS 工作负荷，MABS 版本13.0.415.0 和更高版本以及代理版本2.0.9170.0 及更高版本支持备份报表。
* 只要备份项的数据发送到用户有权访问的 Log Analytics （LA）工作区，就可以跨所有备份项、保管库、订阅和区域查看备份报表。 
* 如果你是有权访问客户订阅的[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/)用户，则可以将这些报告与 azure Lighthouse 配合使用来查看所有租户的报告。
* 日志备份作业的数据当前未显示在报表中。

## <a name="getting-started"></a>入门

若要开始使用报表，请执行下面详细介绍的三个步骤：

1. **创建 Log Analytics （LA）工作区（或使用现有工作区）：**

需要设置一个或多个 "LA" 工作区来存储备份报表数据。 可在其中创建此 LA 工作区的位置和订阅独立于保管库所在的位置和订阅。 

请参阅以下文章：[在 Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)以设置 LA 工作区。

默认情况下，LA 工作区中的数据将保留30天。 若要查看更长时间范围内的数据，请更改 LA 工作区的保持期。 若要更改保持期，请参阅以下文章：[使用 Azure Monitor 日志管理使用情况和成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

2. **为保管库配置诊断设置：**

Azure 资源管理器资源，如恢复服务保管库，记录有关计划操作和用户触发的操作的信息（作为诊断数据）。 

在恢复服务保管库的 "监视" 部分，选择 "**诊断设置**"，然后指定恢复服务保管库的诊断数据的目标。 [了解有关使用诊断事件的详细信息](https://aka.ms/AzureBackupDiagnosticDocs)。

![诊断设置边栏选项卡](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure 备份还提供内置的 Azure 策略，该策略自动配置给定范围内所有保管库的诊断设置。 请参阅以下文章，了解如何使用此策略：[大规模配置保管库诊断设置](https://aka.ms/AzureBackupDiagnosticsPolicyDocs)

3. **查看 Azure 门户上的报表：**

将保管库配置为将数据发送到 LA 后，可通过导航到任一保管库的边栏选项卡并单击 "**备份报表**" 菜单项来查看备份报表。 

![保管库仪表板](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

单击此链接将打开备份报表工作簿。 

> [!NOTE]
> 当前，报表的初始加载最多可能需要1分钟。

下面是报表包含的各个选项卡的说明：

* **摘要**-"摘要" 选项卡提供备份场所的高级概述。 在 "摘要" 选项卡下，你可以快速浏览备份项的总数、已用的总云存储、受保护实例的数量以及每个工作负荷类型的作业成功率。 若要详细了解特定的备份项目类型，请导航到相应的选项卡。

![摘要选项卡](./media/backup-azure-configure-backup-reports/summary.png)

* **备份项**-"备份项" 选项卡可让你查看在备份项级别使用的云存储的信息和趋势。 例如，如果在 Azure VM 备份中使用 SQL，则可以查看每个要备份的 SQL 数据库使用的云存储空间。 你还可以选择查看特定保护状态的备份项的数据。 例如，单击选项卡顶部的 "**已停止保护**" 磁贴，筛选以下所有小组件，以便仅显示处于保护停止状态的备份项目的数据。

![备份项选项卡](./media/backup-azure-configure-backup-reports/backup-items.png)

* **用法**-"用法" 选项卡可帮助你查看备份的关键计费参数。 此选项卡中显示的信息是一个计费实体（受保护容器）级别。 例如，在将 DPM 服务器备份到 Azure 的情况下，你可以查看 DPM 服务器的受保护实例和云存储的使用趋势。 同样，如果使用 azure 备份中的 SQL 或 Azure 备份中的 SAP HANA，此选项卡可在包含这些数据库的虚拟机级别提供与使用情况相关的信息。

![用法选项卡](./media/backup-azure-configure-backup-reports/usage.png)

* **作业**-"作业" 选项卡使你可以查看作业的长时间运行趋势，例如每天失败的作业数，以及失败的原因的主要原因。 可以在聚合级别和备份项级别查看此信息。 单击网格中的特定备份项可查看所选时间范围内该备份项上触发的每个作业的详细信息。

![“作业”选项卡](./media/backup-azure-configure-backup-reports/jobs.png)

* **策略**-"策略" 选项卡可让你查看有关所有活动策略的信息，例如关联的项目数，以及在给定策略中备份的项目所消耗的云存储总量。 单击特定策略可查看有关其每个相关备份项的信息。

![策略选项卡](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>导出到 Excel

单击任意小组件右上角的向下箭头按钮（表/图表）会将该小组件的内容导出为 Excel 工作表，就像现有筛选器一样。 若要将表的更多行导出到 Excel 中，可以通过使用每个网格顶部**每页**下拉列表中显示的行数来增加页上显示的行数。

## <a name="pinning-to-dashboard"></a>固定到仪表板

单击每个小组件顶部的 "固定" 图标，将小组件固定到 Azure 门户仪表板。 这可以帮助你创建自定义的仪表板以显示你需要的最重要的信息。

## <a name="cross-tenant-reports"></a>跨租户报告

如果你是拥有跨多个租户环境的订阅的委托访问权限的[Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/)用户，则可以使用默认订阅筛选器（通过单击 Azure 门户的右上方的 "筛选器" 图标）选择要查看其数据的所有订阅。 这样，便可以在租户中选择 "LA 工作区" 以查看多租户报告。

## <a name="conventions-used-in-backup-reports"></a>备份报表中使用的约定

* 筛选器的工作方式是从左到右，从上到下，每个选项卡上的都是，任何筛选器仅适用于所有位于该筛选器或该筛选器下的小组件。 
* 单击彩色磁贴会过滤磁贴下面的小组件，查找与该图块的值相关的记录。 例如，单击 "备份项" 选项卡中的 "*已停止保护*" 磁贴将筛选下面的网格和图表，以显示处于 "保护已停止" 状态的备份项的数据。
* 未着色的磁贴不可单击。
* 报表中不显示当前未满一天的数据。 因此，当 "**时间范围**" 的选定值为 "***最近7天***" 时，该报表将显示最近7个完成的日期（不包括当天）的记录。
* 该报表显示在所选时间范围内**触发**的作业的详细信息（除日志作业之外）。 
* 为云存储和受保护实例显示的值位于所选时间范围的**末尾**。
* 报表中显示的备份项是在所选时间范围**结束**时存在的项。 不会显示在所选时间范围内删除的备份项。 同样，也适用于备份策略。

## <a name="query-load-times"></a>查询加载时间

备份报表中的小组件由在用户的 LA 工作区上运行的 Kusto 查询提供支持。 由于这些查询通常涉及处理大量数据，并且有多个联接来实现更丰富的见解，因此当用户在大型备份场所查看报表时，小组件可能不会立即加载。 下表根据备份项的数量和查看报表的时间范围，对不同小组件所能执行的时间进行了大致估计：

| **# 数据源**                         | **时间范围** | **加载时间（约）**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| 约 5 K                       | 1 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级别筛选器：5-10 秒|
| 约 5 K                       | 3 个月          | 磁贴：5-10 秒 <br> 网格：5-10 秒 <br> 图表：5-10 秒 <br> 报表级别筛选器：5-10 秒|
| 约 10 K                       | 3 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：1-2 分钟 <br> 报表级别筛选器：25-30 秒|
| 约 15 K                       | 1 个月          | 磁贴：15-20 秒 <br> 网格：15-20 秒 <br> 图表：50-60 秒 <br> 报表级别筛选器：20-25 秒|
| 约 15 K                       | 3 个月          | 磁贴：20-30 秒 <br> 网格：20-30 秒 <br> 图表：2-3 分钟 <br> 报表级别筛选器：50-60 秒 |

## <a name="what-happened-to-the-power-bi-reports"></a>Power BI 报表发生了什么情况？
* 用于报告的早期 Power BI 模板应用（来自 Azure 存储帐户的数据）位于弃用的路径上。 建议按如上所述的方式开始将保管库诊断数据发送到 Log Analytics，以便查看报表。

* 此外，将诊断数据发送到存储帐户或 LA 工作区的 V1 架构也位于弃用路径上。 这意味着，如果你已经编写了基于 V1 架构的任何自定义查询或自动化，则建议更新这些查询以使用当前支持的 V2 架构。

## <a name="next-steps"></a>后续步骤
[详细了解如何在 Azure 备份中进行监视和报告](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)