---
title: SQL 托管实例审核
description: 了解如何通过 T-SQL 开始使用 Azure SQL 托管实例审核
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 213a4fdb0e064e1c36a04f7190f14fab80cb4daa
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117355"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Azure SQL 托管实例审核入门
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL 托管实例](sql-managed-instance-paas-overview.md)审核会跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核还可：

- 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。
- 实现并促进遵从合规标准，但不能保证合规性。 有关支持标准符合性的 Azure 程序的详细信息，请参阅 " [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)"，其中可以找到最新的符合性认证列表。

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>将服务器的审核设置为 Azure 存储

以下部分介绍了托管实例上的审核配置。

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 创建一个用于存储审核日志的 Azure 存储**容器**。

   1. 导航到要在其中存储审核日志的 Azure 存储帐户。

      > [!IMPORTANT]
      > - 请使用与托管实例位于同一区域中的存储帐户，以避免跨区域读取/写入。 
      > - 如果你的存储帐户位于虚拟网络或防火墙后面，请参阅[授予从虚拟网络进行访问的权限](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)。
      > - 请注意，如果将保留期从 0（无限期保留）更改为任何其他值，则该保留期将仅应用于在更改保留期值后所写入的日志（即使启用了上述保留期，在保留期值设置为“无限制”期间所写入的日志仍将无限期保留）。

   1. 在存储帐户中，转到“概述”，然后单击“Blob”。 

      ![Azure Blob 小组件](./media/auditing-configure/1_blobs_widget.png)

   1. 在顶部菜单中，单击“+ 容器”以创建新容器。

      ![创建 Blob 容器图标](./media/auditing-configure/2_create_container_button.png)

   1. 提供一个容器名称，将公共访问级别设置为“专用”，然后单击“确定”   。

      ![创建 Blob 容器配置](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > 如果客户希望为服务器级或数据库级审核事件配置不可变的日志存储，则应遵循 [Azure 存储提供的说明](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)。 （请确保在配置不可变的 blob 存储时，选择了“允许额外追加”。）
  
3. 为审核日志创建容器后，可通过两种方式将其配置为审核日志的目标：[使用 T-SQL](#blobtsql)，或[使用 SQL Server Management Studio (SSMS) UI](#blobssms)：

   - <a id="blobtsql"></a>使用 T-SQL 为审核日志配置 Blob 存储：

     1. 在容器列表中，单击新创建的容器，然后单击“容器属性”。

        ![Blob 容器属性按钮](./media/auditing-configure/4_container_properties_button.png)

     1. 通过单击复制图标来复制容器 URL并保存该 URL（例如在记事本中）供将来使用。 容器 URL 格式应当为 `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob 容器复制 URL](./media/auditing-configure/5_container_copy_name.png)

     1. 生成一个 Azure 存储 SAS 令牌，用于向存储帐户授予托管实例审核访问权限：

        - 导航到在前面的步骤中你在其中创建了容器的 Azure 存储帐户。

        - 在“存储设置”菜单中单击“共享访问签名” 。

          ![存储设置菜单中的“共享访问签名”图标](./media/auditing-configure/6_storage_settings_menu.png)

        - 如下所述配置 SAS：

          - **允许的服务**：Blob

          - **开始日期**：若要避免时区相关的问题，请使用昨天的日期

          - **结束日期**：选择此 SAS 令牌的到期日期

            > [!NOTE]
            > 在到期时续订令牌，以避免审核失败。

          - 单击“生成 SAS”。

            ![SAS 配置](./media/auditing-configure/7_sas_configure.png)

        - SAS 令牌会显示在底部。 通过单击复制图标来复制令牌并保存该令牌（例如在记事本中）供将来使用。

          ![复制 SAS 令牌](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > 从令牌的开头删除问号 (“?”) 字符。

     1. 通过 SQL Server Management Studio 或任何其他支持的工具连接到托管实例。

     1. 执行以下 T-SQL 语句来使用你在前面的步骤中创建的容器 URL 和 SAS 令牌创建新凭据：

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 执行以下 T-SQL 语句来创建新的服务器审核（请选择自己的审核名称，并使用在前面步骤中创建的容器 URL）。 如果未指定，则 `RETENTION_DAYS` 默认为 0（无限期保留）：

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

        继续[创建服务器审核规范或数据库审核规范](#createspec)。

   - <a id="blobssms"></a>使用 SQL Server Management Studio 18（预览版）为审核日志配置 Blob 存储：

     1. 使用 SQL Server Management Studio UI 连接到托管实例。

     1. 展开对象资源管理器的根节点。

     1. 展开“安全性”节点，右键单击“审核”节点，然后单击“新建审核”  ：

        ![展开“安全性”和“审核”节点](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. 确保“审核目标”中已选择“URL”，然后单击“浏览”  ：

        ![浏览 Azure 存储](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. （可选）登录到 Azure 帐户：

        ![登录 Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. 从下拉列表中选择订阅、存储帐户和 Blob 容器，或者单击“创建”来创建自己的容器。 完成后，单击“确定”：

        ![选择 Azure 订阅、存储帐户和 Blob 容器](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. 在“创建审核”对话框中单击“确定” 。

4. <a id="createspec"></a>将 Blob 容器配置为审核日志的目标后，创建并启用服务器审核规范或数据库审核规范（就像对 SQL Server 操作一样）：

   - [创建服务器审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [创建数据库审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. 启用在步骤 3 中创建的服务器审核：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

了解更多信息：

- [Azure SQL 托管实例和 SQL Server 中的数据库之间的审核差异](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>将服务器的审核设置为事件中心或 Azure Monitor 日志

可将托管实例的审核日志发送到 Azure 事件中心或 Azure Monitor 日志。 本部分介绍如何进行以下配置：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到托管实例。

2. 单击“诊断设置”。

3. 单击“启用诊断”。 如果已启用诊断，则会显示“+ 添加诊断设置”。

4. 在日志列表中选择“SQLSecurityAuditEvents”。

5. 选择审核事件的目标：事件中心、Azure Monitor 日志或两者。 为每个目标配置所需的参数（例如，Log Analytics 工作区）。

6. 单击“保存” 。

    ![配置诊断设置](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. 使用 **SQL Server Management Studio (SSMS)** 或任何其他支持的客户端连接到托管实例。

8. 执行下面的 T-SQL 语句，创建服务器审核：

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. 像对 SQL Server 操作一样创建并启用服务器审核规范或数据库审核规范：

   - [创建服务器审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [创建数据库审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 启用在步骤 8 中创建的服务器审核：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>使用审核日志

### <a name="consume-logs-stored-in-azure-storage"></a>使用存储在 Azure 存储中的日志

可使用多种方法查看 blob 审核日志。

- 使用系统函数 `sys.fn_get_audit_file` (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file 文档](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 可使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具浏览审核日志。 在 Azure 存储中，审核日志作为 Blob 文件的集合保存在定义为存储审核日志的容器中。 有关存储文件夹层次、命名约定和日志格式的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。

- 有关审核日志使用方法的完整列表，请参阅 [Azure SQL 数据库审核入门](../../azure-sql/database/auditing-overview.md)。

### <a name="consume-logs-stored-in-event-hubs"></a>使用在事件中心中存储的日志

若要使用事件中心的审核日志数据，需设置一个流来使用事件并将其写入到目标。 有关详细信息，请参阅 Azure 事件中心文档。

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>使用和分析存储在 Azure Monitor 日志中的日志

如果将审核日志写入到 Azure Monitor 日志，则可以在 Log Analytics 工作区中使用它们，可以在其中对审核数据运行高级搜索。 首先，导航到 Log Analytics 工作区。 在“常规”部分下，单击“日志”，然后输入一个简单的查询（例如 `search "SQLSecurityAuditEvents"`）以查看审核日志 。  

有了 Azure Monitor 日志，就可以使用集成的搜索和自定义仪表板来轻松分析所有工作负荷和服务器上的数百万记录，获得实时操作见解。 有关 Azure Monitor 日志搜索语言和命令的其他有用信息，请参阅 [Azure Monitor 日志搜索参考](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Azure SQL 托管实例中的数据库与 SQL Server 中的数据库之间的审核差异

在审核 Azure SQL 托管实例和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 托管实例，审核在服务器级别进行，并在 Azure Blob 存储中存储 `.xel` 日志文件。
- 在 SQL Server 中，审核也在服务器级别执行，但在文件系统/Windows 事件日志中存储事件。

托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 **不支持**文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了新语法 `TO URL`，该语法允许指定用于放置 `.xel` 文件的 Azure Blob 存储容器的 URL。
- 提供了新语法 `TO EXTERNAL MONITOR` 以启用事件中心和 Azure Monitor 日志这两个目标。
- 不支持语法 `TO FILE`，因为 Azure SQL 托管实例无法访问 Windows 文件共享。
- **不支持**关闭选项。
- **不支持**`queue_delay` 为 0。

## <a name="next-steps"></a>后续步骤

- 有关审核日志使用方法的完整列表，请参阅 [Azure SQL 数据库审核入门](../../azure-sql/database/auditing-overview.md)。
- 有关支持标准符合性的 Azure 程序的详细信息，请参阅 " [Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)"，其中可以找到最新的符合性认证列表。

<!--Image references-->
