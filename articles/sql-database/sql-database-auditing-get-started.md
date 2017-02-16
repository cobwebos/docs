---
title: "SQL 数据库审核入门 | Microsoft Docs"
description: "SQL 数据库审核入门"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 7b3c6d29ad7ded2ea65f378a1f807235c688d738
ms.openlocfilehash: 9f51fcdb291021064dc02a71afdf849f1f4a3d85


---
# <a name="get-started-with-sql-database--auditing"></a>SQL 数据库审核入门
Azure SQL 数据库审核跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志内。

审核可帮助你一直保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

审核有助于遵从法规标准，但不能保证遵从法规。 有关可帮助你遵从标准的 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

* [Azure SQL 数据库审核概述]
* [为数据库设置审核]
* [分析审核日志和报告]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL 数据库审核概述
SQL 数据库审核可让你：

* **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
* **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
* **分析**报告。 可以查找可疑事件、异常活动和趋势。

有两种**审核方法**：

* **Blob 审核** - 将日志写入 Azure Blob 存储。 这是一种较新的审核方法，可提供**更高的性能**，支持**更高粒度的对象级审核**，且**更具成本效益**。
* **表审核** - 将日志写入 Azure 表存储。

可按[为数据库设置审核](#subheading-2)部分中所述，为不同类型的事件类别配置审核。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

可以为特定数据库定义审核策略，也可以将审核策略定义为默认服务器策略。 默认的服务器审核策略将应用到服务器上的所有现有数据库和新建的数据库。

## <a name="a-idsubheading-2aset-up-auditing-for-your-database"></a><a id="subheading-2"></a>为数据库设置审核
以下部分介绍如何使用 Azure 门户配置审核。

### <a name="a-idsubheading-2-1blob-auditinga"></a><a id="subheading-2-1">Blob 审核</a>
1. 启动 [Azure 门户](https://portal.azure.com)（https://portal.azure.com）。
2. 导航到你要审核的 SQL 数据库/SQL Server 的设置边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。
   
    <a id="auditing-screenshot"></a>
    ![导航窗格][1]
3. 在数据库审核配置边栏选项卡中，可以选中“从服务器继承审核设置”复选框，指定根据服务器的设置对数据库进行审核。 如果选中此选项，将会看到“查看服务器审核设置”链接，可以使用该链接在此上下文中查看或修改服务器审核设置。
   
    ![导航窗格][2]
4. 若要在数据库级别启用 Blob 审核（结合服务器级审核启用 Blob 审核，或者取代服务器级审核），请**取消选中**“从服务器继承审核设置”选项，将审核设置为“打开”，然后选择“Blob”审核类型。
   
   > 如果启用了服务器 Blob 审核，数据库配置的审核将与服务器 Blob 审核并存。  
   > 
   > 
   
    ![导航窗格][3]
5. 选择“存储详细信息”打开“审核日志存储”边栏选项卡。 选择日志要保存到的 Azure 存储帐户以及保留期（超过此期限的旧日志将被删除），然后单击底部的“确定”。 **提示：**为所有审核的数据库使用相同的存储帐户，以便充分利用审核报告模板。
   
    <a id="storage-screenshot"></a>
    ![导航窗格][4]
6. 若要自定义审核的事件，可以使用 PowerShell 或 REST API - 有关更多详细信息，请参阅[自动化 (PowerShell/REST API)](#subheading-7) 部分。
7. 单击“保存”。

### <a name="a-idsubheading-2-2table-auditinga"></a><a id="subheading-2-2">表审核</a>
> [!NOTE]
> 在设置**表审核**之前，请检查使用的是否为[“下层客户端”](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。 此外，如果有严格的防火墙设置，请注意，在启用表审核时[会更改数据库的 IP 终结点](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。
> 
> 

1. 启动 [Azure 门户](https://portal.azure.com)（https://portal.azure.com）。
2. 导航到你要审核的 SQL 数据库/SQL Server 的设置边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”（[请参阅“Blob 审核”部分中的屏幕截图](#auditing-screenshot)）。
3. 在数据库审核配置边栏选项卡中，可以选中“从服务器继承审核设置”复选框，指定根据服务器的设置对数据库进行审核。 如果选中此选项，将会看到“查看服务器审核设置”链接，可以使用该链接在此上下文中查看或修改服务器审核设置。
   
    ![导航窗格][2]
4. 如果不想要从服务器继承审核设置，请**取消选中**“从服务器继承审核设置”选项，将审核设置为“打开”，然后选择“表”审核类型。
   
    ![导航窗格][3-tbl]
5. 选择“存储详细信息”打开“审核日志存储”边栏选项卡。 选择日志要保存到的 Azure 存储帐户以及保留期（超过此期限的旧日志将被删除）。 **提示：**为所有审核的数据库使用相同的存储帐户，以便充分利用审核报告模板（[请参阅“Blob 审核”部分中的屏幕截图](#storage-screenshot)）。
6. 单击“审核事件”自定义要审核的事件。 在“按事件记录日志”边栏选项卡中，单击“成功”和“失败”记录所有事件，或者选择单个事件类别。
   
   > 也可以通过 PowerShell 或 REST API 自定义审核的事件 - 有关更多详细信息，请参阅[自动化 (PowerShell/REST API)](#subheading-7) 部分。
   > 
   > 
   
    ![导航窗格][5]
7. 配置审核设置后，可以打开新的**威胁检测**（预览版）功能，并配置电子邮件用于接收安全警报。 使用威胁检测可以接收针对异常数据库活动（可能表示潜在的安全威胁）发出的前瞻性警报。 有关更多详细信息，请参阅[威胁检测入门](sql-database-threat-detection-get-started.md)。
8. 单击“保存”。

## <a name="a-idsubheading-3aanalyze-audit-logs-and-reports"></a><a id="subheading-3"></a>分析审核日志和报告
审核日志将在安装期间选择的 Azure 存储帐户中进行聚合。

可以使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具浏览审核日志。

请参阅下面有关分析**表**和 **Blob** 审核日志的具体信息。

### <a name="a-idsubheading-3-1blob-auditinga"></a><a id="subheading-3-1">Blob 审核</a>
Blob 审核日志以 Blob 文件集合的形式保存在名为“**sqldbauditlogs**”的容器中。

有关 Blob 审核日志存储文件夹层次结构、Blob 命名约定和日志格式的详细信息，请参阅 [Blob Audit Log Format Reference（文档下载）](https://go.microsoft.com/fwlink/?linkid=829599)（Blob 审核日志格式参考）。

可通过多种方法查看 Blob 审核日志：

1. 通过 Azure 门户 - **请参阅下面[表审核部分](#activity-ui)中的方法 (1)**（不支持 Excel 下载）。
2. 通过门户或使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具从 Azure 存储 Blob 容器下载日志文件。
   
    在本地下载日志文件后，可以双击该文件将它打开，然后在 SSMS 中查看和分析日志。
   
    其他方法：
   
   * 可以通过 Azure 存储资源管理器**同时下载多个文件** - 右键单击特定的子文件夹（例如包含特定日期生成的所有日志文件的子文件夹），然后选择“另存为”，将文件保存到本地文件夹中。
     
       下载多个文件后（或者一整天的日志文件，如上所述），可按如下所述在本地将它们合并：
     
       **打开 SSMS，选择“文件”->“打开”->“合并扩展事件”-> 选择要合并的所有文件**
   * 以编程方式：
     
     * 扩展事件读取器 **C# 库**（[详细信息](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)）
     * 使用 **PowerShell** 查询扩展事件文件（[详细信息](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)）

### <a name="a-idsubheading-3-2table-auditinga"></a><a id="subheading-3-2">表审核</a>
表审核日志以 Azure 存储表集合的形式保存，具有 **SQLDBAuditLogs** 前缀。

有关表审核日志格式的更多详细信息，请参阅 [Table Audit Log Format Reference（文档下载）](http://go.microsoft.com/fwlink/?LinkId=506733)（表审核日志格式参考）。

可通过多种方法查看表审核日志：

<a id="activity-ui"></a>

1. 通过 [Azure 门户](https://portal.azure.com) - 在“审核和威胁检测”边栏选项卡的顶部单击“更多”，然后单击“浏览”。 此时将打开“审核记录”边栏选项卡，可在其中查看日志。
   
   * 可以通过单击“审核记录”边栏选项卡顶部区域中的“筛选”，选择查看特定的日期
   * 可以通过单击“审核记录”边栏选项卡顶部区域中的“在 Excel 中打开”，下载并查看 Excel 格式的审核日志
     
     ![导航窗格][7]
2. 或者，可以借助以[可下载 Excel 电子表格形式](http://go.microsoft.com/fwlink/?LinkId=403540)提供的预配置报告模板来快速分析日志数据。 若要对审核日志使用模板，需要安装可从[此处](http://www.microsoft.com/download/details.aspx?id=39379)下载的 Excel 2013 或更高版本以及 Power Query。
   
    还可以使用 Power Query 将审核日志从 Azure 存储帐户直接导入 Excel 模板。 然后可以浏览审核记录，并在日志数据顶部创建仪表板和报表。
   
    ![导航窗格][9]

## <a name="a-idsubheading-5apractices-for-usage-in-production"></a><a id="subheading-5"></a>生产环境中的用法实践
<!--The description in this section refers to screen captures above.-->

### <a name="a-idsubheading-6auditing-geo-replicated-databasesa"></a><a id="subheading-6">审核异地复制的数据库</a>
使用异地复制的数据库时，可以根据审核类型，针对主数据库和/或辅助数据库设置审核。

**表审核** - 可以根据[为数据库设置审核](#subheading-2-2)部分中所述，在数据库或服务器级别为两个数据库（主数据库或辅助数据库）中的一个配置不同的策略。

**Blob 审核** - 请遵循以下说明：

1. **主数据库** - 根据[为数据库设置审核](#subheading-2-1)部分中所述，在服务器或数据库本身上打开“Blob 审核”。
2. **辅助数据库** - 仅可以通过主数据库审核设置打开/关闭 Blob 审核。
   
   * 根据[为数据库设置审核](#subheading-2-1)部分中所述，对“主数据库”打开“Blob 审核”。 （**注意：**必须在数据库本身上启用 Blob 审核，而不要在服务器上启用）。
   * 在主数据库上启用 Blob 审核后，也会在辅助数据库上启用 Blob 审核。
   * **重要说明：**默认情况下，辅助数据库的**存储设置**与主数据库相同，因而会导致生成**跨区域流量**。 在**辅助服务器**上启用 Blob 审核并在辅助服务器存储设置中配置**本地存储**可以避免此问题（这会覆盖辅助数据库的存储位置，导致每个数据库将审核日志保存到本地存储中）。  

<br>

### <a name="a-idsubheading-6storage-key-regenerationa"></a><a id="subheading-6">重新生成存储密钥</a>
在生产环境中，可能会定期刷新存储密钥。 刷新密钥时，需要重新保存审核策略。 过程如下：

1. 在存储详细信息边栏选项卡中，将“存储访问密钥”从“主要”切换为“辅助”，然后单击底部的“确定”。 然后，单击审核配置边栏选项卡顶部的“保存”。
   
    ![导航窗格][6]
2. 转到存储配置边栏选项卡，并**重新生成***主访问密钥*。
   
    ![导航窗格][8]
3. 返回到审核配置边栏选项卡，将“存储访问密钥”从“辅助”切换为“主要”，然后单击底部的“确定”。 然后，单击审核配置边栏选项卡顶部的“保存”。
4. 返回到存储配置边栏选项卡并**重新生成***辅助访问密钥*（为下一个密钥刷新周期做好准备）。

## <a name="a-idsubheading-7aautomation-powershell--rest-api"></a><a id="subheading-7"></a>自动化 (PowerShell/REST API)
也可以使用以下自动化工具在 Azure SQL 数据库中配置审核：

1. **PowerShell cmdlets**
   
   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **REST API - Blob 审核**
   
   * [创建或更新数据库 Blob 审核策略](https://msdn.microsoft.com/en-us/library/azure/mt695939.aspx)
   * [创建或更新服务器 Blob 审核策略](https://msdn.microsoft.com/en-us/library/azure/mt771861.aspx)
   * [获取数据库 Blob 审核策略](https://msdn.microsoft.com/en-us/library/azure/mt695938.aspx)
   * [获取服务器 Blob 审核策略](https://msdn.microsoft.com/en-us/library/azure/mt771860.aspx)
   * [获取服务器 Blob 审核操作结果](https://msdn.microsoft.com/en-us/library/azure/mt771862.aspx)
3. **REST API - 表审核**
   
   * [创建或更新数据库审核策略](https://msdn.microsoft.com/en-us/library/azure/mt604471.aspx)
   * [创建或更新服务器审核策略](https://msdn.microsoft.com/en-us/library/azure/mt604383.aspx)
   * [获取数据库审核策略](https://msdn.microsoft.com/en-us/library/azure/mt604381.aspx)
   * [获取服务器审核策略](https://msdn.microsoft.com/en-us/library/azure/mt604382.aspx)

<!--Anchors-->
[Azure SQL 数据库审核概述]: #subheading-1
[为数据库设置审核]: #subheading-2
[分析审核日志和报告]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx



<!--HONumber=Dec16_HO1-->


