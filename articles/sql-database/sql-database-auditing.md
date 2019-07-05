---
title: Azure SQL 数据库审核入门 | Microsoft 文档
description: 使用 Azure SQL 数据库审核跟踪写入审核日志的数据库事件。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 1b3a6a18d10b9d9f6ab6456ae2911e54f5c56a71
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544085"
---
# <a name="get-started-with-sql-database-auditing"></a>SQL 数据库审核入门

审核 Azure [SQL 数据库](sql-database-technical-overview.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)会跟踪数据库事件，并将这些事件写入 Azure 存储帐户、OMS 工作区或事件中心中的审核日志。 审核还可：

- 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

- 实现并促进遵从合规标准，但不能保证合规性。 有关支持标准符合性的 Azure 程序的详细信息，请参阅 [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，可以从中找到 SQL 数据库符合性认证的最新列表。


> [!NOTE] 
> 本主题适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Azure SQL 数据库审核概述

可使用 SQL 数据库审核来：

- **保留**选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
- **报告**数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
- **分析**报告。 可以查找可疑事件、异常活动和趋势。

> [!IMPORTANT]
> 审核日志会写入到 Azure 订阅的 Azure Blob 存储中的**追加 Blob**。
>
> - 支持所有存储类型（v1、v2、blob）。
> - 支持所有存储复制配置。
> - 目前不支持高级存储   。
> - 目前不支持 VNet 中的存储   。
> - 目前不支持“应用有防火墙的存储”   。

## <a id="subheading-8"></a>定义服务器级和数据库级审核策略

可为特定数据库定义审核策略，也可将审核策略定义为默认服务器策略：

- 服务器策略适用于服务器上的所有现有数据库和新建数据库。

- 如果启用服务器 blob 审核，它将一直应用于数据库。   将不考虑数据库审核设置审核数据库。

- 除在服务器上启用 blob 审核外，在数据库或数据仓库上启用 blob 审核也不会替代或更改服务器 blob 审核的任何设置。  这两种审核会并存。 换言之，会并行对数据库执行两次审核；一次按服务器策略审核，一次按数据库策略审核。

   > [!NOTE]
   > 应避免同时启用服务器 blob 审核和数据库 blob 审核，除非：
    > - 需要对特定数据库使用不同的*存储帐户*或*保留期*。
    > - 对于与服务器上其他数据库不同的特定数据库，应审核事件类型或类别。 例如，可能拥有仅需要针对特定数据库进行审核的表插入。
   >
   > 否则，建议仅启用服务器级 blob 审核，并对所有数据库禁用数据库级审核。

## <a id="subheading-2"></a>为数据库设置审核

以下部分介绍如何使用 Azure 门户配置审核。

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 导航到“SQL 数据库/服务器”窗格中“安全性”标题下的“审核”  。

    <a id="auditing-screenshot"></a> ![导航窗格][1]

3. 如果想设置服务器审核策略，可以选择数据库审核页中的“查看服务器设置”链接。  然后，可查看或修改服务器审核设置。 服务器审核策略应用于此服务器上所有现有和新建数据库。

    ![导航窗格][2]

4. 如果希望在数据库级别启用审核，请将“审核”  切换到“启用”  。

    如果启用了服务器审核，数据库配置的审核将与服务器审核并存。

    ![导航窗格][3]

5. **新建** - 现在有多个选项，可以用来配置写入审核日志的位置。 到 Azure 存储帐户、 Azure Monitor 日志由 Log Analytics 工作区或事件中心以便使用事件中心，可以编写日志。 可以将这些选项随意组合起来进行配置，审核日志会写入到每一个之中。

   > [!WARNING]
   > 启用审核到 Log Analytics 将产生成本基于引入速率。 请特别注意与使用此相关的开销[选项](https://azure.microsoft.com/pricing/details/monitor/)，或者考虑在 Azure 存储帐户中存储审核日志。

    ![存储选项](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. 若要配置将审核日志写入存储帐户的操作，请选择“存储”，打开“存储详细信息”。   依次选择要用于保存日志的 Azure 存储帐户以及保持期。 将删除旧日志。 然后单击“确定”  。

    ![存储帐户](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. 若要配置将审核日志写入 Log Analytics 工作区的操作，请选择“Log Analytics (预览版)”，并打开“Log Analytics 详细信息”。   选择或创建要将日志写入到其中的 Log Analytics 工作区，然后单击“确定”。 

    ![Log Analytics 工作区](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. 若要配置将审核日志写入事件中心的操作，请选择“事件中心(预览版)”，打开“事件中心详细信息”。   选择要将日志写入到的事件中心，然后单击“确定”。  请确保事件中心与数据库和服务器位于同一区域。

    ![事件中心](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. 单击“ **保存**”。
10. 若要自定义已审核的事件，可通过 [PowerShell cmdlet](#subheading-7) 或 [REST API](#subheading-9) 执行此操作。
11. 配置审核设置后，可打开新威胁检测功能，并配置电子邮件用于接收安全警报。 使用威胁检测时，会接收针对异常数据库活动（可能表示潜在的安全威胁）发出的前瞻性警报。 有关详细信息，请参阅[威胁检测入门](sql-database-threat-detection-get-started.md)。

> [!IMPORTANT]
> 不能启用审核上暂停 Azure SQL 数据仓库。 若要启用它，请取消暂停数据仓库。

> [!WARNING]
> 具有 Azure SQL 数据仓库上它的服务器上启用审核**会导致数据仓库正在恢复和重新再次暂停**这可能会产生计费费用中。

## <a id="subheading-3"></a>分析审核日志和报告

如果您选择将审核日志写入到 Azure Monitor 日志：

- 使用 [Azure 门户](https://portal.azure.com)。  打开相关数据库。 在数据库的“审核”  页的顶部，单击“查看审核日志”  。

    ![查看审核日志](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- 然后单击“审核记录”页面顶部的“在 OMS 中打开”，   ，此时会在 Log Analytics 中打开“日志”视图，以便在其中自定义时间范围和搜索查询。

    ![在 Log Analytics 中打开](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- 也可从 Log Analytics 边栏选项卡访问审核日志。 打开 Log Analytics 工作区，然后在“常规”部分单击“日志”。   一开始可以使用简单的查询（例如：搜索“SQLSecurityAuditEvents”  ）来查看审核日志。
    在这里，可以使用[Azure Monitor 日志](../log-analytics/log-analytics-log-search.md)审核日志数据上运行的高级的搜索。 有了 Azure Monitor 日志，就可以使用集成的搜索和自定义仪表板来轻松分析所有工作负荷和服务器上的数百万记录，获得实时操作见解。 有关 Azure Monitor 日志搜索语言和命令的其他有用信息，请参阅 [Azure Monitor 日志搜索参考](../log-analytics/log-analytics-log-search.md)。

如果已选择将审核日志写入到事件中心，请执行以下操作：

- 若要使用事件中心的审核日志数据，需设置一个流来使用事件并将其写入到目标。 有关详细信息，请参阅 [Azure 事件中心文档](https://docs.microsoft.com/azure/event-hubs/)。
- 事件中心内的审核日志在 [Apache Avro](https://avro.apache.org/) 事件的主体中捕获，并使用带有 UTF-8 编码的 JSON 格式进行存储。 若要读取审核日志，可以使用 [Avro 工具](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools)或处理此格式的类似工具。

如果选择将审核日志写入到 Azure 存储帐户，可以使用多种方法来查看日志：

- 审核日志会在安装期间选择的帐户中进行聚合。 可使用 [Azure 存储资源管理器](https://storageexplorer.com/)等工具浏览审核日志。 在 Azure 存储中，审核日志以 Blob 文件集合的形式保存在名为 **sqldbauditlogs** 的容器中。 有关更多详细信息的存储文件夹层次结构，命名约定和日志格式，请参阅[SQL 数据库审核日志格式](https://go.microsoft.com/fwlink/?linkid=829599)。

- 使用 [Azure 门户](https://portal.azure.com)。  打开相关数据库。 在数据库的“审核”  页的顶部，单击“查看审核日志”  。

    ![导航窗格][7]

    此时会打开“审核记录”  ，可在其中查看日志。

  - 可单击“审核记录”  页顶部的“筛选”  ，查看特定的日期。
  - 可以通过切换“审核源”  在服务器审核策略  和数据库审核策略  创建的审核记录之间进行切换。
  - 通过选中“仅显示 SQL 注入的审核记录”  复选框，可以仅查看与 SQL 注入相关的审核记录。

       ![导航窗格][8]

- 使用系统函数 **sys.fn_get_audit_file** (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 使用 SQL Server Management Studio 中的“合并审核文件”  选项（从 SSMS 17 开始）：
    1. 在 SSMS 菜单中，选择“文件”   > “打开”   > “合并审核文件”  。

        ![导航窗格][9]
    2. 此时会打开“添加审核文件”  对话框。 通过“添加”选项，选择是合并本地磁盘中的审核文件还是从 Azure 存储中导入。  需要提供 Azure 存储详细信息和帐户密钥。

    3. 添加要合并的所有文件后，单击“确定”  完成合并操作。

    4. 合并的文件会在 SSMS 中打开，可在其中进行查看和分析，以及将其作为 XEL 或 CSV 文件导出或导出到表中。

- 使用 Power BI。 可在 Power BI 中查看和分析审核日志数据。 如需详细信息并访问可下载的模板，请参阅[在 Power BI 中分析审核日志数据](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)。
- 通过门户或使用 [Azure 存储资源管理器](https://storageexplorer.com/)等工具从 Azure 存储 blob 容器下载日志文件。
  - 在本地下载日志文件后，可双击打开文件，然后在 SSMS 中查看和分析日志。
  - 也可通过 Azure 存储资源管理器同时下载多个文件。 为此，请右键单击特定子文件夹，然后选择“另存为”，以便在本地文件夹中进行保存。 

- 其他方法：

  - 下载多个文件或包含日志文件的子文件夹后，可以按照前述 SSMS 合并审核文件说明在本地合并它们。
  - 以编程方式查看 blob 审核日志：

    - 使用 PowerShell [查询扩展事件文件](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/)。

## <a id="subheading-5"></a>生产做法

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">审核异地复制的数据库</a>

通过异地复制数据库，在主数据库上启用审核时，辅助数据库将有相同的审核策略。 还可以在独立于主数据库的“辅助服务器”上启用审核，从而在辅助数据库上设置审核。 

- 服务器级（推荐  ）：服务器级（推荐）：同时在主服务器和辅助服务器上启用审核 - 基于各自的服务器级策略，将分别对主数据库和辅助数据库进行审核。  
- 数据库级：辅助数据库的数据库级审核只能从主数据库审核设置进行配置。
  - 必须在主数据库本身  上启用审核，而不是在服务器上启用。
  - 在主数据库上启用审核后，也会在辅助数据库上启用审核。

    >[!IMPORTANT]
    >在数据库级审核中，辅助数据库的存储设置与主数据库相同，因而会导致生成跨区域流量。 建议仅启用服务器级审核，并对所有数据库禁用数据库级审核。
    > [!WARNING]
    > 异地复制的辅助数据库当前不支持在服务器级别的审核日志为目标使用事件中心或 Azure Monitor 日志。

### <a id="subheading-6">重新生成存储密钥</a>

在生产环境中，可能会定期刷新存储密钥。 如果向 Azure 存储写入审核日志，则需在刷新密钥时重新保存审核策略。 过程如下：

1. 打开“存储详细信息”。  在“存储访问密钥”  框中，选择“辅助”  并单击“确定”  。 然后单击“审核配置”页顶部的“保存”  。

    ![导航窗格][5]
2. 转到存储配置页，重新生成主访问密钥。

    ![导航窗格][6]
3. 返回“审核配置”页，将“存储访问密钥”从“辅助”切换为“主要”，然后单击“确定”  。 然后单击“审核配置”页顶部的“保存”  。
4. 返回“存储配置”页并重新生成辅助访问密钥（为下一个密钥刷新周期做好准备）。

## <a name="additional-information"></a>其他信息

- 有关日志格式、存储文件夹的层次结构和命名约定的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。

    > [!IMPORTANT]
    > Azure SQL 数据库审核在审核记录中存储字符字段的 4000 个字符的数据。 当可审核操作返回的**语句**或 **data_sensitivity_information** 值包含超过 4000 个的字符时，超出前 4000 个字符的任何数据将**被截去不进行审核**。

- 审核日志会写入到 Azure 订阅的 Azure Blob 存储中的追加 Blob  。
  - 追加 Blob 目前不支持高级存储   。
  - 目前不支持 VNet 中的存储   。

- 默认审核策略包括所有操作和下列操作组集合，将用于审核针对数据库执行的所有查询和存储过程以及成功和失败的登录：

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    可以按照[使用 Azure PowerShell 管理 SQL 数据库审核](#subheading-7)部分中所述，使用 PowerShell 配置不同类型的操作和操作组的审核。

- 使用 AAD 身份验证时，失败的登录记录将不会  出现在 SQL 审核日志中。 若要查看失败的登录审核记录，需要访问 [Azure Active Directory 门户]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)，该门户记录这些事件的详细信息。


## <a id="subheading-7"></a>使用 Azure PowerShell 管理 SQL 数据库审核

**PowerShell cmdlet（包括对附加筛选的 WHERE 子句支持）** ：

- [创建或更新数据库审核策略 (设置 AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [创建或更新服务器审核策略 (设置 AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [获取数据库审核策略 (Get AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [获取服务器审核策略 (Get AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [删除数据库审核策略 (删除 AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [删除服务器审核策略 (删除 AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a id="subheading-9"></a>使用 REST API 管理 SQL 数据库审核

**REST API**：

- [创建或更新数据库审核策略](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [创建或更新服务器审核策略](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [获取数据库审核策略](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [获取服务器审核策略](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

支持使用 WHERE 子句执行附加筛选的扩展策略：

- [创建或更新数据库扩展  审核策略](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [创建或更新服务器扩展  审核策略](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [获取数据库扩展  审核策略](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [获取服务器扩展  审核策略](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>使用 ARM 模板管理 SQL 数据库审核

可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模板管理 Azure SQL 数据库审核，如以下示例所示：

- [部署启用了审核的 Azure SQL Server，以将审核日志写入 Azure Blob 存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [部署启用了审核的 Azure SQL Server，以将审核日志写入 Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [部署启用了审核的 Azure SQL Server，以将审核日志写入事件中心](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> 链接的示例是在外部公共存储库上，将提供是，不含任何保证，和不受任何 Microsoft 支持程序/服务。

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

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
