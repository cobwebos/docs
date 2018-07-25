---
title: Azure SQL 数据仓库中的审核 | Microsoft 文档
description: 了解审核，以及如何设置 Azure SQL 数据仓库中的审核。
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 3630d4d694452f2c619e707d1e2e58f1bfe71c0e
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37858946"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的审核

了解审核，以及如何设置 Azure SQL 数据仓库中的审核。

## <a name="what-is-auditing"></a>什么是审核？
SQL 数据仓库审核允许用户将数据库中的事件记录到 Azure 存储帐户中的审核日志。 审核可帮助保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

审核工具有助于遵从法规标准，但不能保证遵从法规。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

## <a id="subheading-1"></a>审核基础知识
使用 SQL 数据仓库数据库审核可以：

* **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
* **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
* **分析**报告。 可以查找可疑事件、异常活动和趋势。

审核日志存储在 Azure 存储帐户中。 可以定义审核日志的保持期。


## <a id="subheading-4"></a>定义服务器级和数据库级审核策略

可为特定数据库定义审核策略，也可将审核策略定义为默认服务器策略：

* 服务器策略将**应用于服务器上的所有现有数据库和新建数据库**。

* 如果启用服务器 blob 审核，它将一直应用于数据库。 将不考虑数据库审核设置审核数据库。

* 在数据库上启用审核以及在服务器上启用审核都不会替代或更改服务器 blob 审核的任何设置。 这两种审核会并存。 换言之，会并行对数据库执行两次审核；一次按服务器策略审核，一次按数据库策略审核。

> [!NOTE]
> 建议仅启用**服务器级 blob 审核**，并对所有数据库禁用数据库级审核。
> 应避免同时启用服务器审核和数据库审核，除非：
> * 需要对特定数据库使用不同的*存储帐户*或*保留期*。
> * 对于与服务器上其他数据库不同的特定数据库，应审核事件类型或类别。 例如，可能拥有仅需要针对特定数据库进行审核的表插入。
> * 需要使用威胁检测，它目前仅支持数据库级审核。

> [!IMPORTANT]
>在 Azure SQL 数据仓库或在具有 Azure SQL 数据仓库的服务器上启用审核将导致数据仓库恢复，即使在以前被暂停的情况下也是如此。 请确保在启用审核后再次暂停数据仓库。

## <a id="subheading-5"></a>为所有数据库设置服务器级审核

服务器审核策略将应用于**服务器上的所有现有数据库和新建数据库**。

以下部分介绍如何使用 Azure 门户配置审核。

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 转到要审核的 **SQL Server**（请务必确保正在查看的是 SQL Server，而不是特定数据库/DW）。 在“安全性”菜单中，选择“审核和威胁检测”。

    ![导航窗格][6]
4. 在“审核和威胁检测”边栏选项卡中，针对“审核”选择“开”。 此审核策略将应用于该服务器上的所有现有数据库和新建数据库。

    ![导航窗格][7]
5. 若要打开“审核日志存储”边栏选项卡，请选择“存储详细信息”。 选择或创建要保存日志的 Azure 存储帐户，然后选择保留期（旧日志会被删除）。 然后单击“确定”。

    ![导航窗格][8]

    > [!IMPORTANT]
    > 服务器级审核日志会写入 Azure 订阅的 Azure Blob 存储中的**追加 Blob**。
    >
    > * 追加 Blob 目前不支持高级存储。
    > * 目前不支持 VNet 中的存储。

8. 单击“ **保存**”。



## <a id="subheading-2"></a>为单个数据库设置数据库级审核

可以为特定数据库定义审核策略，也可以将审核策略定义为默认服务器策略。

强烈建议使用服务器级审核而不是数据库级审核，如[定义服务器级和数据库级别审核策略](#subheading-4)中所述。

在设置审核之前，请检查是否正在使用[“下层客户端”](sql-data-warehouse-auditing-downlevel-clients.md)。


1. 启动 [Azure 门户](https://portal.azure.com)。
2. 转到要审核的 SQL 数据仓库的“设置”。 选择“审核和威胁检测”。

    ![][1]
3. 接下来，通过单击“打开”按钮启用审核。

    ![][3]
4. 在审核配置面板中，选择“存储详细信息”打开“审核日志存储”面板。 选择日志的 Azure 存储帐户和保留期。
    >[!TIP]
    >对所有已审核的数据库使用相同的存储帐户，以便充分利用预配置的报告模板。

    ![][4]

5. 单击“确定”按钮保存存储详细信息配置。
6. 在“按事件记录日志”下，单击“成功”和“失败”以记录所有事件，或者选择单个事件类别。
7. 如果要为某个数据库配置审核，可能需要更改客户端的连接字符串，以确保正确捕获数据审核。 对于下层客户端连接，请查看[修改连接字符串中的服务器 FDQN](sql-data-warehouse-auditing-downlevel-clients.md) 主题。
8. 单击“确定”。

## <a id="subheading-3"></a>分析审核日志和报告

###<a name="server-level-policy-audit-logs"></a>服务器级策略审核日志
服务器级审核日志会写入 Azure 订阅的 Azure Blob 存储中的**追加 Blob**。 它们以 blob 文件集合的形式保存在名为 **sqldbauditlogs** 的容器中。

有关存储文件夹层次、命名约定和日志格式的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。

可使用多种方法查看 blob 审核日志：

* 使用 SQL Server Management Studio 中的“合并审核文件”选项（从 SSMS 17 开始）：
    1. 在 SSMS 菜单中，选择“文件” > “打开” > “合并审核文件”。

    2. 此时会打开“添加审核文件”对话框。 通过“添加”选项，选择是合并本地磁盘中的审核文件还是从 Azure 存储中导入。 需要提供 Azure 存储详细信息和帐户密钥。

    3. 添加要合并的所有文件后，单击“确定”完成合并操作。

    4. 合并的文件会在 SSMS 中打开，可在其中进行查看和分析，以及将其作为 XEL 或 CSV 文件导出或导出到表中。

* 使用创建的[同步应用程序](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)。 该应用程序在 Azure 中运行，并利用 Log Analytics 公共 API 将 SQL 审核日志推送到 Log Analytics 中。 同步应用程序通过 Log Analytics 仪表板将 SQL 审核日志推送到 Log Analytics 中以供使用。

* 使用 Power BI。 可在 Power BI 中查看和分析审核日志数据。 详细了解 [Power BI 及如何访问可下载的模板](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)。

* 通过门户或使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具从 Azure 存储 blob 容器下载日志文件。
    * 在本地下载日志文件后，可双击打开文件，然后在 SSMS 中查看和分析日志。
    * 也可通过 Azure 存储资源管理器同时下载多个文件。 右键单击特定子文件夹，然后选择“另存为”存储在本地文件夹中。

* 其他方法：
   * 下载多个文件或包含日志文件的子文件夹后，可以按照前述 SSMS 合并审核文件说明本地合并它们。

   * 以编程方式查看 blob 审核日志：

     * 使用[扩展事件读取器](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# 库。
     * 使用 PowerShell [查询扩展事件文件](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。



<br>
###<a name="database-level-policy-audit-logs"></a>数据库级策略审核日志
数据库级审核日志会在设置期间选择的 Azure 存储帐户中前缀为 **SQLDBAuditLogs** 的一系列存储表内进行聚合。 可以使用工具（比如 [Azure 存储资源管理器](http://azurestorageexplorer.codeplex.com)）查看日志文件。

可以借助以[可下载 Excel 电子表格](http://go.microsoft.com/fwlink/?LinkId=403540)形式提供的预配置仪表板报告模板来快速分析日志数据。 若要对审核日志使用模板，需要安装 Excel 2013 或更高版本以及 Power Query（可[从此处下载](http://www.microsoft.com/download/details.aspx?id=39379)）。

该模板包含虚构的示例数据，可以将 Power Query 设置为直接从 Azure 存储帐户导入审核日志。

## <a id="subheading-4"></a>重新生成存储密钥
在生产环境中，可能会定期刷新存储密钥。 刷新密钥时，需要保存策略。 过程如下：

1. 在前述设置审核部分中介绍的审核配置面板中，将“存储访问密钥”从“主要”更改为“辅助”，然后单击“保存”。

   ![][4]
2. 转到存储配置面板，“重写”“主访问密钥”。
3. 返回审核配置面板，
4. 将“存储访问密钥”从“辅助”切换到“主要”，并按“保存”。
4. 返回存储 UI 并**重新生成**辅助访问密钥（为下一个密钥刷新周期做好准备）。

## <a id="subheading-5"></a>自动化 (PowerShell/REST API)
也可使用以下自动化工具在 Azure SQL 数据仓库中配置审核：

* **PowerShell cmdlet**：

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>审核和动态数据掩码的下层客户端支持
审核适用于支持 TDS 重定向的 SQL 客户端。

任何实现了 TDS 7.4 的客户端同样应当支持重定向。 例外情况包括不完全支持重定向功能的 JDBC 4.0 以及未实现重定向的 Tedious（适用于 Node.JS）。

对于支持 TDS 7.3 版和更低版本的客户端的“下层客户端”，应修改连接字符串中的服务器 FQDN，如下所示：

- 连接字符串中的原始服务器 FQDN：<服务器名称>.database.windows.net
- 连接字符串中的修改后的服务器 FQDN：<服务器名称>.database.**secure**.windows.net

“下层客户端”的部分列表包括：

* .NET 4.0 和更低版本，
* ODBC 10.0 和更低版本。
* JDBC（JDBC 虽然支持 TDS 7.4，但不完全支持 TDS 重定向功能）
* Tedious（适用于 Node.JS）

**注释：** 前面的服务器 FDQN 修改可能还可用于应用 SQL Server 级别的审核策略，而无需在每个数据库中进行配置（临时缓解）。     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
