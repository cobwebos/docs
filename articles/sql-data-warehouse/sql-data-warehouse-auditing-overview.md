---
title: Azure SQL 数据仓库中的审核 | Microsoft 文档
description: 了解审核，以及如何设置 Azure SQL 数据仓库中的审核。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: rortloff
ms.reviewer: jrj
ms.openlocfilehash: 7bce59114dcfef7f9faa7f112c62dbb88044f2ab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的审核

了解审核，以及如何设置 Azure SQL 数据仓库中的审核。

## <a name="what-is-auditing"></a>什么是审核？
SQL 数据仓库审核允许用户将数据库中的事件记录到 Azure 存储帐户中的审核日志。 审核可帮助保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。 SQL 数据仓库审核还与 Microsoft Power BI 集成，用于报告和分析数据。

审核工具有助于遵从法规标准，但不能保证遵从法规。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

## <a id="subheading-1"></a>审核基础知识
使用 SQL 数据仓库数据库审核可以：

* **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
* **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
* **分析**报告。 可以查找可疑事件、异常活动和趋势。

可以为以下事件类别配置审核：

**普通 SQL** 和**参数化 SQL**，且为其收集的审核日志归类为  

* **对数据的访问**
* **架构更改 (DDL)**
* **数据更改 (DML)**
* **帐户、角色和权限 (DCL)**
* **存储过程**、**登录**和**事务管理**。

针对每个事件类别，分别配置**成功**和**失败**操作的审核。

有关审核的活动和事件的详细信息，请参阅<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">审核日志格式参考（doc 文件下载）</a>。

审核日志存储在 Azure 存储帐户中。 可以定义审核日志的保持期。

可以为特定数据库定义审核策略，也可以将审核策略定义为默认服务器策略。 默认服务器审核策略将应用于服务器上所有未定义特定重写数据库审核策略的数据库。

在设置审核之前，请检查是否正在使用[“下层客户端”](sql-data-warehouse-auditing-downlevel-clients.md)。

## <a id="subheading-2"></a>为数据库设置审核
1. 启动 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。
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
审核日志会在设置期间选择的 Azure 存储帐户中前缀为 **SQLDBAuditLogs** 的一系列存储表内进行聚合。 可以使用工具（比如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 存储资源管理器</a>）查看日志文件。

可以借助以<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下载 Excel 电子表格</a>形式提供的预配置仪表板报告模板来快速分析日志数据。 若要对审核日志使用模板，需要安装可从<a href="http://www.microsoft.com/download/details.aspx?id=39379">此处</a>下载的 Excel 2013 或更高版本以及 Power Query。

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

**注释：**前面的服务器 FDQN 修改可能还可用于应用 SQL Server 级别的审核策略，而无需在每个数据库中进行配置（临时缓解）。     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


