---
title: "Azure SQL 数据仓库中的审核 | Microsoft 文档"
description: "Azure SQL 数据仓库中的审核入门"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 08/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: f851c82ebeaa647f663d499a4d327c3479e36121
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的审核
> [!div class="op_single_selector"]
> * [审核](sql-data-warehouse-auditing-overview.md)
> * [威胁检测](sql-data-warehouse-security-threat-detection.md)
> 
> 

SQL 数据仓库审核允许用户将数据库中的事件记录到 Azure 存储帐户中的审核日志。 审核可帮助保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。 SQL 数据仓库审核还与 Microsoft Power BI 集成，适用于向下钻取报告和分析数据。

审核工具有助于遵从法规标准，但不能保证遵从法规。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 信任中心</a>。

* [数据库审核基础知识]
* [为数据库设置审核]
* [分析审核日志和报告]

## <a id="subheading-1"></a>Azure SQL 数据仓库数据库审核基础知识
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
2. 转到要审核的 SQL 数据仓库的“设置”边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。
   
    ![][1]
3. 接下来，通过单击“打开”按钮启用审核。
   
    ![][3]
4. 在审核配置边栏选项卡中，选择“存储详细信息”打开“审核日志存储”边栏选项卡。 选择要用于保存日志的 Azure 存储帐户以及保持期。 
>[!TIP]
>对所有已审核的数据库使用相同的存储帐户，以便充分利用预配置的报告模板。
   
    ![][4]
5. 单击“确定”按钮保存存储详细信息配置。
6. 在“按事件记录日志”下，单击“成功”和“失败”以记录所有事件，或者选择单个事件类别。
7. 如果要为某个数据库配置审核，可能需要更改客户端的连接字符串，以确保正确捕获数据审核。 对于下层客户端连接，请查看[修改连接字符串中的服务器 FDQN](sql-data-warehouse-auditing-downlevel-clients.md) 主题。
8. 单击 **“确定”**。

## <a id="subheading-3"></a>分析审核日志和报告
审核日志会在设置期间选择的 Azure 存储帐户中前缀为 **SQLDBAuditLogs** 的一系列存储表内进行聚合。 可以使用工具（比如 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 存储资源管理器</a>）查看日志文件。

可以借助以<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">可下载 Excel 电子表格</a>形式提供的预配置仪表板报告模板来快速分析日志数据。 若要对审核日志使用模板，需要安装可从<a href="http://www.microsoft.com/download/details.aspx?id=39379">此处</a>下载的 Excel 2013 或更高版本以及 Power Query。

该模板包含虚构的示例数据，可以将 Power Query 设置为直接从 Azure 存储帐户导入审核日志。

## <a id="subheading-4"></a>重新生成存储密钥
在生产环境中，可能会定期刷新存储密钥。 刷新密钥时，需要保存策略。 过程如下：

1. 在“审核配置”边栏选项卡中（如上面“设置审核”部分中所述），将“存储访问密钥”从“主”切换为“辅助”，然后单击“保存”。

   ![][4]
2. 转到存储配置边栏选项卡，并**重新生成**主访问密钥。
3. 返回审核配置边栏选项卡，将“存储访问密钥”从“辅助”切换为“主”，然后按“保存”。
4. 返回存储 UI 并**重新生成**辅助访问密钥（为下一个密钥刷新周期做好准备）。

## <a id="subheading-5"></a>自动化 (PowerShell/REST API)
也可使用以下自动化工具在 Azure SQL 数据仓库中配置审核：

* **PowerShell cmdlet**：

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

<!--Anchors-->
[数据库审核基础知识]: #subheading-1
[为数据库设置审核]: #subheading-2
[分析审核日志和报告]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy