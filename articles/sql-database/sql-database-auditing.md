---
title: "Azure SQL 数据库审核入门 | Microsoft 文档"
description: "使用 Azure SQL 数据库审核跟踪写入审核日志的数据库事件。"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.openlocfilehash: c97a9d96dbe6d9bc9eaa189384acad7579365e82
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="get-started-with-sql-database-auditing"></a>SQL 数据库审核入门
Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核还可：

* 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

* 实现并促进遵从合规标准，但不能保证合规性。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。


## <a id="subheading-1"></a>Azure SQL 数据库审核概述
可使用 SQL 数据库审核来：


* **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
* **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
* **分析**报告。 可以查找可疑事件、异常活动和趋势。

可按[为数据库设置审核](#subheading-2)部分中所述，为不同类型的事件类别配置审核。

审核日志会写入 Azure 订阅中的 Azure Blob 存储。


## <a id="subheading-8"></a>定义服务器级和数据库级审核策略

可为特定数据库定义审核策略，也可将审核策略定义为默认服务器策略：

* 服务器策略适用于服务器上的所有现有数据库和新建数据库。

* 如果启用服务器 blob 审核，它将一直应用于数据库。 将不考虑数据库审核设置审核数据库。

* 除在服务器上启用 blob 审核外，在数据库上启用 blob 审核也不会替代或更改服务器 blob 审核的任何设置。 这两种审核会并存。 换言之，会并行对数据库执行两次审核；一次按服务器策略审核，一次按数据库策略审核。

   > [!NOTE]
   > 应避免同时启用服务器 blob 审核和数据库 blob 审核，除非：
    > * 需要对特定数据库使用不同的*存储帐户*或*保留期*。
    > * 对于与服务器上其他数据库不同的特定数据库，应审核事件类型或类别。 例如，可能拥有仅需要针对特定数据库进行审核的表插入。
   >
   > 否则，建议仅启用服务器级 blob 审核，并对所有数据库禁用数据库级审核。


## <a id="subheading-2"></a>为数据库设置审核
以下部分介绍如何使用 Azure 门户配置审核。

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 转到要审核的 SQL 数据库/SQL Server 的“设置”边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。

    <a id="auditing-screenshot"></a> ![导航窗格][1]
3. 如果想设置服务器审核策略，可以选择数据库审核边栏选项卡中的“查看服务器设置”链接。 然后，可查看或修改服务器审核设置。 服务器审核策略应用于此服务器上所有现有和新建数据库。

    ![导航窗格][2]
4. 如果想要在数据库级别启用 blob 审核，为“审核”选择“开”，为“审核类型”选择“Blob”。

    如果启用了服务器 blob 审核，数据库配置的审核会与服务器 blob 审核并存。

    ![导航窗格][3]
5. 若要打开“审核日志存储”边栏选项卡，请选择“存储详细信息”。 依次选择要用于保存日志的 Azure 存储帐户以及保持期。 将删除旧日志。 然后单击“确定”。
   >[!TIP]
   >若要充分利用审核报告模板，请为所有审核的数据库使用相同的存储帐户。

    <a id="storage-screenshot"></a> ![导航窗格][4]
6. 若要自定义已审核的事件，可通过 PowerShell 或 REST API 执行此操作。 
7. 配置审核设置后，可打开新威胁检测功能，并配置电子邮件用于接收安全警报。 使用威胁检测时，会接收针对异常数据库活动（可能表示潜在的安全威胁）发出的前瞻性警报。 有关详细信息，请参阅[威胁检测入门](sql-database-threat-detection-get-started.md)。
8. 单击“保存” 。





## <a id="subheading-3"></a>分析审核日志和报告
审核日志会在安装期间选择的 Azure 存储帐户中进行聚合。 可使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具浏览审核日志。

Blob 审核日志以 blob 文件集合的形式保存在名为 **sqldbauditlogs** 的容器中。

有关存储文件夹层次、命名约定和日志格式的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。

可使用多种方法查看 blob 审核日志：

* 使用 [Azure 门户](https://portal.azure.com)。  打开相关数据库。 在数据库的“审核和威胁检测”边栏选项卡的顶部，单击“查看审核日志”。

    ![导航窗格][7]

    此时会打开“审核记录”边栏选项卡，可在其中查看日志。

    - 可单击“审核记录”边栏选项卡顶部的“筛选”，查看特定的日期。
    - 可在服务器策略审核或数据库策略审核创建的审核记录之间切换。

       ![导航窗格][8]

* 使用系统函数 **sys.fn_get_audit_file** (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file 文档](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。


* 使用 SQL Server Management Studio 中的“合并审核文件”选项（从 SSMS 17 开始）：
    1. 在 SSMS 菜单中，选择“文件” > “打开” > “合并审核文件”。

        ![导航窗格][9]
    2. 此时会打开“添加审核文件”对话框。 通过“添加”选项，选择是合并本地磁盘中的审核文件还是从 Azure 存储中导入。 需要提供 Azure 存储详细信息和帐户密钥。

    3. 添加要合并的所有文件后，单击“确定”完成合并操作。

    4. 合并的文件会在 SSMS 中打开，可在其中进行查看和分析，以及将其作为 XEL 或 CSV 文件导出或导出到表中。

* 使用创建的[同步应用程序](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)。 该应用程序在 Azure 中运行，并利用 Operations Management Suite (OMS) Log Analytics 公共 API 将 SQL 审核日志推送到 OMS 中。 同步应用程序通过 OMS Log Analytics 仪表板将 SQL 审核日志推送到 OMS Log Analytics 中以供使用。

* 使用 Power BI。 可在 Power BI 中查看和分析审核日志数据。 详细了解 [Power BI 及如何访问可下载的模板](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)。

* 通过门户或使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具从 Azure 存储 blob 容器下载日志文件。
    * 在本地下载日志文件后，可双击打开文件，然后在 SSMS 中查看和分析日志。
    * 也可通过 Azure 存储资源管理器同时下载多个文件。 右键单击特定子文件夹，然后选择“另存为”存储在本地文件夹中。

* 其他方法：
   * 下载多个文件或包含日志文件的子文件夹后，可以按照前述 SSMS 合并审核文件说明本地合并它们。

   * 以编程方式查看 blob 审核日志：

     * 使用[扩展事件读取器](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# 库。
     * 使用 PowerShell [查询扩展事件文件](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。




## <a id="subheading-5"></a>生产实践
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">审核异地复制的数据库</a>
通过异地复制数据库，在主数据库上启用审核时，辅助数据库将有相同的审核策略。 还可以在独立于主数据库的“辅助服务器”上启用审核，从而在辅助数据库上设置审核。

* 服务器级（推荐）：同时在主服务器和辅助服务器上启用审核 - 基于各自的服务器级策略，将分别对主数据库和辅助数据库进行审核。

* 数据库级：辅助数据库的数据库级审核只能从主数据库审核设置进行配置。
   * 必须在主数据库本身上启用 blob 审核，而不要在服务器上启用。
   * 在主数据库上启用 blob 审核后，也会在辅助数据库上启用 blob 审核。

     >[!IMPORTANT]
     >在数据库级审核中，辅助数据库的存储设置与主数据库相同，因而会导致生成跨区域流量。 建议仅启用服务器级审核，并对所有数据库禁用数据库级审核。
<br>

### <a id="subheading-6">重新生成存储密钥</a>
在生产环境中，可能会定期刷新存储密钥。 刷新密钥时，需要重新保存审核策略。 过程如下：

1. 打开“存储详细信息”边栏选项卡。 在“存储访问密钥”框中，选择“辅助”并单击“确定”。 然后单击“审核配置”边栏选项卡顶部的“保存”。

    ![导航窗格][5]
2. 转到“存储配置”边栏选项卡，重新生成主访问密钥。

    ![导航窗格][6]
3. 返回“审核配置”边栏选项卡，将“存储访问密钥”从“辅助”切换为“主要”，然后单击“确定”。 然后单击“审核配置”边栏选项卡顶部的“保存”。
4. 返回“存储配置”边栏选项卡并重新生成辅助访问密钥（为下一个密钥刷新周期做好准备）。

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>使用 Azure PowerShell 管理 SQL 数据库审核


* **PowerShell cmdlet**：

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditing][105]
   * [Set-AzureRMSqlServerAuditing][106]

   有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="manage-sql-database-auditing-using-rest-api"></a>使用 REST API 管理 SQL 数据库审核

* **REST API - Blob 审核**：

   * [创建或更新数据库 Blob 审核策略](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [创建或更新服务器 Blob 审核策略](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [获取数据库 Blob 审核策略](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [获取服务器 Blob 审核策略](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [获取服务器 Blob 审核操作结果](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
