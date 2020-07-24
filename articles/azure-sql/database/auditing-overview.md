---
title: Azure SQL 数据库和 Azure Synapse Analytics 的 Azure SQL 审核
description: 使用 Azure SQL 数据库审核跟踪写入审核日志的数据库事件。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 24c3ec1ee16123cef0c4e2bd230bfdb66915fc9f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040592"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 数据库和 Azure Synapse Analytics 的审核
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

审核 Azure [SQL 数据库](sql-database-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 可跟踪数据库事件，并将这些事件写入 Azure 存储帐户、Log Analytics 工作区或事件中心内的审核日志。

审核还可：

- 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

- 实现并促进遵从合规标准，但不能保证合规性。 有关支持标准符合性的 Azure 程序的详细信息，请参阅 [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，可以从中找到 Azure SQL 符合性认证的最新列表。

> [!NOTE]
> 有关 Azure SQL 托管实例审核的信息，请参阅以下文章：[SQL 托管实例审核入门](../managed-instance/auditing-configure.md)。

## <a name="overview"></a><a id="overview"></a>概述

可使用 SQL 数据库审核来：

- **保留** 选定事件的审核痕迹。 可以定义要审核的数据库操作的类别。
- **报告** 数据库活动。 可以使用预配置的报告和仪表板快速开始使用活动和事件报告。
- **分析** 报告。 可以查找可疑事件、异常活动和趋势。

> [!IMPORTANT]
> Azure SQL 数据库审核已针对可用性和性能进行优化。 在活动量极高的情况下，Azure SQL 数据库或 Azure Synapse 允许操作继续进行，可能不会记录某些已审核的事件。

### <a name="auditing-limitations"></a>审核限制

- 目前不支持高级存储 。
- **Azure Data Lake Storage Gen2 存储帐户**的**分层命名空间**目前**不受支持**。
- 不支持在已暂停的 Azure Synapse 上启用审核。 若要启用审核，请恢复运行 Azure Synapse。

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>定义服务器级和数据库级审核策略

可为特定数据库定义审核策略，也可将审核策略定义为 Azure（托管 SQL 数据库或 Azure Synapse）中的默认[服务器](logical-servers.md)策略：

- 服务器策略适用于服务器上的所有现有数据库和新建数据库。

- 如果启用服务器审核，它将一直应用于数据库。  将不考虑数据库审核设置审核数据库。

- 在数据库上启用审核以及在服务器上启用审核都不会替代或更改服务器审核的任何设置。 这两种审核会并存。 换言之，会并行对数据库执行两次审核；一次按服务器策略审核，一次按数据库策略审核。

   > [!NOTE]
   > 应避免同时启用服务器审核和数据库 blob 审核，除非：
    >
    > - 需要对特定数据库使用不同的存储帐户、保持期或 Log Analytics 工作区。
    > - 对于与服务器上其他数据库不同的特定数据库，应审核事件类型或类别。 例如，可能拥有仅需要针对特定数据库进行审核的表插入。
   >
   > 否则，建议仅启用服务器级审核，并对所有数据库禁用数据库级审核。

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>为服务器设置审核

默认审核策略包括所有操作和下列操作组集合，将用于审核针对数据库执行的所有查询和存储过程以及成功和失败的登录：
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
可以按照[使用 Azure PowerShell 管理 SQL 数据库审核](#manage-auditing)部分中所述，使用 PowerShell 配置不同类型的操作和操作组的审核。

Azure SQL 数据库和 Azure Synapse 审核在审核记录中存储字符字段的 4000 个字符的数据。 当可审核操作返回的**语句**或 **data_sensitivity_information** 值包含超过 4000 个的字符时，超出前 4000 个字符的任何数据将**被截去不进行审核**。
以下部分介绍如何使用 Azure 门户配置审核。

  > [!NOTE]
  > 不能对已暂停的 Synapse SQL 池启用审核。 若要启用审核，请取消暂停 Synapse SQL 池。 了解有关[SYNAPSE SQL 池](https://docs.microsoft.com/azure/synapse-analytics/sql/best-practices-sql-pool)的详细信息。

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 导航到“SQL 数据库”或“SQL Server”窗格中“安全性”标题下的“审核”  。
3. 如果想设置服务器审核策略，可以选择数据库审核页中的“查看服务器设置”链接。 然后，可查看或修改服务器审核设置。 服务器审核策略应用于此服务器上所有现有和新建数据库。

    ![导航窗格](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. 如果希望在数据库级别启用审核，请将“审核”切换到“启用”。 如果启用了服务器审核，数据库配置的审核将与服务器审核并存。

5. 你有多个选项，用于配置将在其中写入审核日志的位置。 可将日志写入 Azure 存储帐户、写入 Log Analytics 工作区（供 Azure Monitor 日志（预览版）使用）或写入事件中心（通过事件中心（预览版）使用）。 可以将这些选项随意组合起来进行配置，审核日志会写入到每一个之中。
  
   ![存储选项](./media/auditing-overview/auditing-select-destination.png)

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>对存储目标的审核

若要配置将审核日志写入存储帐户的操作，请选择“存储”，打开“存储详细信息”。  依次选择要用于保存日志的 Azure 存储帐户以及保持期。 。 早于保留期的日志会被删除。

- 保持期的默认值为 0（无限制保留）。 在配置用于审核的存储帐户时，可以通过在“存储设置”中移动“保留期（天数）”滑块来更改此值。
  - 如果将保留期从 0（无限期保留）更改为任何其他值，请注意：保留期仅应用于在保留期值更改后写入的日志（在保留期设置为“无限期”期间写入的日志会保留，即使在启用保留期后也是如此）。

  ![存储帐户](./media/auditing-overview/auditing_select_storage.png)

#### <a name="remarks"></a>备注

- 审核日志将写入到 Azure 订阅的 Azure Blob 存储中的追加 Blob
- 若要为服务器或数据库级审核事件配置不可变的日志存储，请按照[Azure 存储提供的说明](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)进行操作。 在配置不可变 blob 存储时，请确保已选择 "**允许额外追加**"。
- 可以将审核日志写入到 VNet 或防火墙后面的 Azure 存储帐户。 有关具体说明，请参阅[将审核写入 VNet 和防火墙后面的存储帐户](audit-write-storage-account-behind-vnet-firewall.md)。
- 配置审核设置后，可打开新威胁检测功能，并配置电子邮件用于接收安全警报。 使用威胁检测时，会接收针对异常数据库活动（可能表示潜在的安全威胁）发出的前瞻性警报。 有关详细信息，请参阅[威胁检测入门](threat-detection-overview.md)。
- 有关日志格式、存储文件夹的层次结构和命名约定的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。
- 使用 AAD 身份验证时，失败的登录记录将不会出现在 SQL 审核日志中。 若要查看失败的登录审核记录，需要访问 [Azure Active Directory 门户](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)，该门户记录这些事件的详细信息。
- 对[只读副本](read-scale-out.md)的审核会自动启用。 若要详细了解存储文件夹、命名约定和日志格式的层次结构，请参阅 [SQL 数据库审核日志格式](audit-log-format.md)。

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>对 Log Analytics 目标的审核
  
若要配置将审核日志写入 Log Analytics 工作区的操作，请选择“Log Analytics (预览版)”，并打开“Log Analytics 详细信息”。  选择或创建要将日志写入到其中的 Log Analytics 工作区，然后单击“确定”。

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

有关 Azure Monitor 日志工作区的更多详细信息，请参阅[设计 Azure Monitor 日志部署](https://docs.microsoft.com/azure/azure-monitor/platform/design-logs-deployment)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>对事件中心目标的审核

若要配置将审核日志写入事件中心的操作，请选择“事件中心(预览版)”，打开“事件中心详细信息”。  选择要将日志写入到的事件中心，然后单击“确定”。 请确保事件中心与数据库和服务器位于同一区域。

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>分析审核日志和报告

如果选择将审核日志写入 Azure Monitor 日志：

- 使用 [Azure 门户](https://portal.azure.com)。 打开相关数据库。 在数据库的“审核”页的顶部，选择“查看审核日志” 。

    ![查看审核日志](./media/auditing-overview/auditing-view-audit-logs.png)

- 然后，可以通过两种方式查看日志：

    单击“审核记录”页面顶部的“Log Analytics”会在 Log Analytics 工作区中打开“日志”视图，在其中可以自定义时间范围和搜索查询。 

    ![在 Log Analytics 工作区中打开](./media/auditing-overview/auditing-log-analytics.png)

    单击“审核记录”页面顶部的“查看仪表板”会打开一个显示审核日志信息的仪表板，在其中可以深化到“安全见解”、“访问敏感数据”，等等。  此仪表板旨在帮助你获取数据的安全见解。
    还可以自定义时间范围和搜索查询。
    ![查看 Log Analytics 仪表板](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics 仪表板](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics 安全见解](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- 也可从 Log Analytics 边栏选项卡访问审核日志。 打开 Log Analytics 工作区，然后在“常规”部分单击“日志”。  一开始可以使用简单的查询（例如：搜索“SQLSecurityAuditEvents”）来查看审核日志。
    在这里，还可以使用 [Azure Monitor 日志](../../azure-monitor/log-query/log-query-overview.md)来对审核日志数据运行高级搜索。 在 Azure Monitor 日志中，可以使用集成的搜索和自定义仪表板随时分析所有工作负荷和服务器中的数百万条记录，以获得实时操作见解。 若要详细了解 Azure Monitor 日志搜索语言和命令，请参阅 [Azure Monitor 日志搜索参考](../../azure-monitor/log-query/log-query-overview.md)。

如果已选择将审核日志写入到事件中心，请执行以下操作：

- 若要使用事件中心的审核日志数据，需设置一个流来使用事件并将其写入到目标。 有关详细信息，请参阅 [Azure 事件中心文档](../index.yml)。
- 事件中心内的审核日志在 [Apache Avro](https://avro.apache.org/) 事件的主体中捕获，并使用带有 UTF-8 编码的 JSON 格式进行存储。 若要读取审核日志，可以使用 [Avro 工具](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools)或处理此格式的类似工具。

如果选择将审核日志写入到 Azure 存储帐户，可以使用多种方法来查看日志：

- 审核日志会在安装期间选择的帐户中进行聚合。 可使用 [Azure 存储资源管理器](https://storageexplorer.com/)等工具浏览审核日志。 在 Azure 存储中，审核日志以 Blob 文件集合的形式保存在名为 **sqldbauditlogs** 的容器中。 有关存储文件夹的层次结构、命名约定和日志格式的详细信息，请参阅 [SQL 数据库审核日志格式](https://go.microsoft.com/fwlink/?linkid=829599)。

- 使用 [Azure 门户](https://portal.azure.com)。  打开相关数据库。 在数据库的“审核”页的顶部，单击“查看审核日志”。

    ![导航窗格](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    此时会打开“审核记录”，可在其中查看日志。

  - 可单击“审核记录”页顶部的“筛选”，查看特定的日期。
  - 可以通过切换“审核源”在服务器审核策略和数据库审核策略创建的审核记录之间进行切换。
  - 通过选中“仅显示 SQL 注入的审核记录”复选框，可以仅查看与 SQL 注入相关的审核记录。

       ![导航窗格]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- 使用系统函数 **sys.fn_get_audit_file** (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 使用 SQL Server Management Studio 中的“合并审核文件”选项（从 SSMS 17 开始）：
    1. 在 SSMS 菜单中，选择“文件” > “打开” > “合并审核文件”。

        ![导航窗格](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. 此时会打开“添加审核文件”对话框。 通过“添加”选项，选择是合并本地磁盘中的审核文件还是从 Azure 存储中导入。 需要提供 Azure 存储详细信息和帐户密钥。

    3. 添加要合并的所有文件后，单击“确定”完成合并操作。

    4. 合并的文件会在 SSMS 中打开，可在其中进行查看和分析，以及将其作为 XEL 或 CSV 文件导出或导出到表中。

- 使用 Power BI。 可在 Power BI 中查看和分析审核日志数据。 如需详细信息并访问可下载的模板，请参阅[在 Power BI 中分析审核日志数据](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)。
- 通过门户或使用 [Azure 存储资源管理器](https://storageexplorer.com/)等工具从 Azure 存储 blob 容器下载日志文件。
  - 在本地下载日志文件后，可双击打开文件，然后在 SSMS 中查看和分析日志。
  - 也可通过 Azure 存储资源管理器同时下载多个文件。 为此，请右键单击特定子文件夹，然后选择“另存为”，以便在本地文件夹中进行保存。

- 其他方法：

  - 下载多个文件或包含日志文件的子文件夹后，可以按照前述 SSMS 合并审核文件说明在本地合并它们。
  - 以编程方式查看 blob 审核日志：

    - 使用 PowerShell [查询扩展事件文件](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。

## <a name="production-practices"></a><a id="production-practices"></a>生产做法


### <a name="auditing-geo-replicated-databases"></a>审核异地复制的数据库

通过异地复制数据库，在主数据库上启用审核时，辅助数据库将有相同的审核策略。 还可以在独立于主数据库的“辅助服务器”上启用审核，从而在辅助数据库上设置审核。

- 服务器级（推荐）：服务器级（推荐）：同时在主服务器和辅助服务器上启用审核 - 基于各自的服务器级策略，将分别对主数据库和辅助数据库进行审核。 
- 数据库级：辅助数据库的数据库级审核只能从主数据库审核设置进行配置。
  - 必须在主数据库本身上启用审核，而不是在服务器上启用。
  - 在主数据库上启用审核后，也会在辅助数据库上启用审核。

    > [!IMPORTANT]
    > 在数据库级审核中，辅助数据库的存储设置与主数据库相同，因而会导致生成跨区域流量。 建议仅启用服务器级审核，并对所有数据库禁用数据库级审核。

### <a name="storage-key-regeneration"></a>重新生成存储密钥

在生产环境中，可能会定期刷新存储密钥。 如果向 Azure 存储写入审核日志，则需在刷新密钥时重新保存审核策略。 过程如下：

1. 打开“存储详细信息”。 在“存储访问密钥”框中，选择“辅助”并单击“确定”。 然后单击“审核配置”页顶部的“保存”。

    ![导航窗格](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. 转到存储配置页，重新生成主访问密钥。

    ![导航窗格](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. 返回“审核配置”页，将“存储访问密钥”从“辅助”切换为“主要”，然后单击“确定”。 然后单击“审核配置”页顶部的“保存”。
4. 返回“存储配置”页并重新生成辅助访问密钥（为下一个密钥刷新周期做好准备）。

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>管理 Azure SQL 数据库审核

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

**PowerShell cmdlet（包括支持使用 WHERE 子句进行其他筛选）** ：

- [创建或更新数据库审核策略 (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [创建或更新服务器审核策略 (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [获取数据库审核策略 (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [获取服务器审核策略 (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [删除数据库审核策略 (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [删除服务器审核策略 (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/auditing-threat-detection-powershell-configure.md)。

### <a name="using-rest-api"></a>使用 REST API

**REST API**：

- [创建或更新数据库审核策略](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [创建或更新服务器审核策略](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [获取数据库审核策略](/rest/api/sql/database%20auditing%20settings/get)
- [获取服务器审核策略](/rest/api/sql/server%20auditing%20settings/get)

支持使用 WHERE 子句执行附加筛选的扩展策略：

- [创建或更新数据库扩展审核策略](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [创建或更新服务器扩展审核策略](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [获取数据库扩展审核策略](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [获取服务器扩展审核策略](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

可以使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)模板管理 Azure SQL 数据库审核，如以下示例中所示：

- [部署启用了审核的 Azure SQL 数据库，以将审核日志写入 Azure Blob 存储帐户](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [部署启用了审核的 Azure SQL 数据库，以将审核日志写入 Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [部署启用了审核的 Azure SQL 数据库，以将审核日志写入事件中心](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> 链接的示例位于外部公共存储库中，按 "原样" 提供，不含任何担保，在任何 Microsoft 支持计划/服务下均不受支持。
