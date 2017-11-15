---
title: "使用 OMS Log Analytics 监视 Azure SQL 数据同步 | Microsoft 文档"
description: "了解如何通过使用 OMS Log Analytics 监视 Azure SQL 数据同步"
services: sql-database
ms.date: 11/7/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: e1099d2cd7eeccbe76d762028a0c5d5f95f53026
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="monitor-azure-sql-data-sync-preview-with-oms-log-analytics"></a>使用 OMS Log Analytics 监视 Azure SQL 数据同步（预览版） 

若要检查 SQL 数据同步活动日志并检测错误和警告，以前必须在 Azure 门户手动检查 SQL 数据同步，或者使用 PowerShell 或 REST API。 请按照本文中的步骤配置自定义解决方案，以便改进数据同步监控体验。 可以自定义该解决方案以适合你的方案。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>监视所有同步组的仪表板 

再也无需单独仔细查看每个同步组的日志就能查找问题。 通过使用自定义的 OMS (Operations Management Suite) 视图，你可以在一个位置监控任何订阅中的所有同步组。 此视图显示对 SQL 数据同步客户很重要的信息。

![数据同步监控仪表板](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.jpg)

## <a name="automated-email-notifications"></a>自动电子邮件通知

无需在 Azure 门户中手动或通过 PowerShell、REST API 检查日志。 你可以利用 [OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 创建警报，以便在发生错误时直接转到需要查看的电子邮件地址。

![数据同步电子邮件通知](media/sql-database-sync-monitor-oms/sync-email-notifications.jpg)

## <a name="how-do-you-set-this-up"></a>如何设置该通知？ 

通过执行以下操作，即可在不到一小时内为 SQL 数据同步实施自定义 OMS 监控解决方案。

你将需要配置 3 个组件：

-   将 SQL 数据同步日志数据输送到 OMS 的 PowerShell runbook。

-   OMS Log Analytics 电子邮件通知警报。

-   可监控的 OMS 视图。

### <a name="samples-to-download"></a>要下载的示例

可下载以下两个示例：

-   [数据同步日志 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [数据同步日志 OMS 视图](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>先决条件

请确保已设置以下内容：

-   一个 Azure 自动化帐户

-   与 OMS 工作区链接的 Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>要获取 SQL 数据同步日志的 PowerShell Runbook 

使用托管在 Azure 自动化中的 PowerShell runbook，以便拉取 SQL 数据同步日志数据并将其发送到 OMS。 已包含示例脚本。 作为先决条件，需要有一个 Azure 自动化帐户。 然后，你需要创建 runbook 并安排它运行。 

### <a name="create-a-runbook"></a>创建 runbook

有关创建 runbook 的详细信息，请参阅[我的第一个 PowerShell runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)。

1.  在 Azure 自动化帐户下，请选择“流程自动化”下的“Runbook”选项卡。

2.  在 Runbook 页面左上角选择“添加 Runbook”。

3.  选择“导入现有的 Runbook”。

4.  在“Runbook 文件”下，使用给定的 `DataSyncLogPowerShellRunbook` 文件。 将“Runbook 类型”设置为 `PowerShell`。 为 runbook 提供一个名称。

5.  选择“创建”。 现在你拥有了一个 runbook。

6.  在 Azure 自动化帐户下，请选择“共享资源”下的“变量”选项卡。

7.  在“变量”页上，选择“添加变量”。 我们需要创建一个变量来存储 runbook 的上次执行时间。 如果你有多个 runbook，则每个 runbook 都需要一个变量。

8.  该变量名称设置为 `DataSyncLogLastUpdatedTime`，并将其类型设置为 DateTime。

9.  选择 runbook，然后单击页顶部的编辑按钮。

10. 请为你的帐户和 SQL 数据同步配置执行所需的更改。 （如需更多详细信息，请参阅示例脚本。）

    1.  Azure 信息。

    2.  同步组信息。

    3.  OMS 信息。 在 OMS 门户查找此信息 |设置 |连接的源。 有关将数据发送到 Log Analytics 的详细信息，请参阅[使用 HTTP 数据收集器 API（公共预览版）将数据发送到 Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)。

11. 在“测试”窗格中运行 runbook。 检查以确保它已运行成功。

    如果遇到错误，请确保已安装最新的PowerShell 模块。 你可以在自动化帐户的“模块库”中执行此操作。

12. 单击“发布”。

### <a name="schedule-the-runbook"></a>计划 runbook。

要计划 runbook，请执行以下操作：

1.  在 runbook 下，选择“资源”下的“计划”选项卡。

2.  在“计划”页上选择“添加计划”。

3.  选择“将一个计划链接到你的 Runbook”。

4.  选择“创建新计划”。

5.  将“定期”设置为“重复执行”，并设置所需间隔。 在脚本和 OMS 中，请在此处使用相同的间隔。

6.  选择“创建”。

### <a name="check-the-automation"></a>检查自动化

若要监控你的自动化设置是否在按预期方式运行，请在自动化帐户的“概述”下，查找“监控”下的“作业统计信息”视图。 将此项固定到仪表板以便于查看。 成功运行的 runbook 显示为“已完成”，失败的运行显示为“失败”。

## <a name="create-an-oms-log-reader-alert-for-email-notifications"></a>创建 OMS Log Reader 电子邮件通知警报

若要创建一个使用 OMS Log Analytics 的警报，请执行以下操作。 作为先决条件，你需要将 Log Analytics 与一个 OMS 工作区进行链接。

1.  在 OMS 门户中，选择“日志搜索”。

2.  创建查询，以便在所选的间隔内按同步组选择错误和警告。 例如：

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  运行查询之后，请选择指示“警报”的钟形图标。

4.  在“基于以下项生成警报”中，请选择“指标度量值”。

    1.  将聚合值设置为“大于”。

    2.  在“大于”后，输入接收通知之前等待的阈值。 在数据同步中可能会出现暂时性的错误。建议将该阈值设置为 5，以减少干扰。

5.  在“操作”下，将“电子邮件通知”设置为“是”。 输入所需的电子邮件收件人。

6.  单击“保存”。 现在，当错误发生时，指定的收件人就会收到电子邮件通知。

## <a name="create-an-oms-view-for-monitoring"></a>创建要监控的 OMS 视图。

此步骤将创建一个 OMS 视图，以直观地监控所有指定的同步组。 该视图包括多个组件：

-   概述磁贴 - 显示所有同步组具有多少个错误、成功和警告。

-   所有同步组的磁贴 - 显示每个同步组的错误数和警告数。 没有问题的组将不会在此磁贴中显示。

-   每个同步的磁贴 - 显示错误数、成功数和警告数，以及最近的错误消息数。

若要配置 OMS 视图，请执行以下操作：

1.  在 OMS 主页上，选择左侧的加号，打开“视图设计器”。

2.  在视图设计器的顶部栏中选择“导入”。 然后选择“DataSyncLogOMSView”示例文件。

3.  该示例视图用于管理两个同步组。 编辑此视图以适合你的方案。 打开“编辑”并进行以下更改：

    1.  请根据需要从库创建新的“圆环图和列表”对象。

    2.  在每个磁贴中，使用你的信息更新查询。

        1.  在每个磁贴上，请根据需要更改 TimeStamp_t 间隔。

        2.  在每个同步组的磁贴上，更新同步组名称。

    3.  在每个磁贴上，请根据需要更新磁贴。

4.  单击“保存”，视图即已准备就绪。

## <a name="cost-of-this-solution"></a>此解决方案的成本

在大多数情况下，此解决方案是免费的。

Azure 自动化：Azure 自动化帐户可能会产生成本，具体要取决于你的使用情况。 每月前 500 分钟的作业运行时间是免费的。 在大多数情况下，此解决方案预计每个月使用不到 500 分钟。 为了避免收费，请计划 Runbook 以两个小时或更长的间隔运行。 有关详细信息，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

OMS 日志分析：可能会有与 OMS 相关的成本，具体要取决于你的使用情况。 免费层包括每日 500 MB 的引入数据。 在大多数情况下，此解决方案预计每天引入的数据不超过 500 MB。 若要减少使用，请使用 runbook 中包含的“仅失败筛选”。 如果你每天使用的数据超过 500 MB，请升级到付费层，以避免在达到此限制时停止分析的风险。 有关详细信息，请参阅[Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)。

## <a name="code-samples"></a>代码示例

从以下位置下载本文介绍的代码示例：

-   [数据同步日志 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [数据同步日志 OMS 视图](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   [Azure SQL 数据同步入门](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 数据同步问题疑难解答](sql-database-troubleshoot-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
