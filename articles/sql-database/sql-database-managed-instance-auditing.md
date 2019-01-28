---
title: Azure SQL 数据库托管实例审核 | Microsoft Docs
description: 了解如何通过 T-SQL 开始使用 Azure SQL 数据库托管实例审核
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 04c4bba2647b9b17b1282c9a1608fd2e9325f661
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427910"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>开始使用 Azure SQL 数据库托管实例审核

[Azure SQL 数据库托管实例](sql-database-managed-instance.md)审核功能可跟踪数据库事件，并将事件写入到 Azure 存储帐户中的审核日志。 审核还可：

- 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。
- 实现并促进遵从合规标准，但不能保证合规性。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>将服务器的审核设置为 Azure 存储

以下部分介绍了托管实例上的审核配置。

1. 转到 [Azure 门户](https://portal.azure.com)。
1. 创建一个用于存储审核日志的 Azure 存储**容器**。

   1. 导航到要在其中存储审核日志的 Azure 存储。

      > [!IMPORTANT]
      > 请使用与托管实例服务器位于同一区域中的存储帐户以避免跨区域读取/写入。

   1. 在存储帐户中，转到“概述”，然后单击“Blob”。

      ![Azure Blob 小组件](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. 在顶部菜单中，单击“+ 容器”以创建新容器。

      ![创建 Blob 容器图标](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. 提供一个容器**名称**，将公共访问级别设置为“专用”，然后单击“确定”。

     ![创建 Blob 容器配置](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. 为审核日志创建容器后，可通过两种方式将其配置为审核日志的目标：[使用 T-SQL](#blobtsql)，或[使用 SQL Server Management Studio (SSMS) UI](#blobssms)：

   - <a id="blobtsql"></a>使用 T-SQL 为审核日志配置 Blob 存储：

     1. 在容器列表中，单击新创建的容器，然后单击“容器属性”。

        ![Blob 容器属性按钮](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. 通过单击复制图标来复制容器 URL并保存该 URL（例如在记事本中）供将来使用。 容器 URL 格式应当为 `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob 容器复制 URL](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. 生成一个 Azure 存储 **SAS 令牌**，用于向存储帐户授予“托管实例审核”访问权限。

        - 导航到在前面的步骤中你在其中创建了容器的 Azure 存储帐户。

        - 在“存储设置”菜单中单击“共享访问签名”。

          ![存储设置菜单中的“共享访问签名”图标](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - 如下所述配置 SAS：

          - **允许的服务**：Blob

          - **开始日期**：为避免与时区相关的问题，建议使用昨天的日期

          - **结束日期**：选择此 SAS 令牌的到期日期

            > [!NOTE]
            > 在到期时续订令牌，以避免审核失败。

          - 单击“生成 SAS”。
            
            ![SAS 配置](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - 单击“生成 SAS”令牌后，SAS 令牌将出现在底部。 通过单击复制图标来复制令牌并保存该令牌（例如在记事本中）供将来使用。

          ![复制 SAS 令牌](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > 从令牌的开头删除问号 (“?”) 字符。

     1. 通过 SQL Server Management Studio (SSMS) 或支持的其他任何工具连接到托管实例。

     1. 执行以下 T-SQL 语句来使用你在前面的步骤中创建的容器 URL 和 SAS 令牌**创建新凭据**：

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 执行以下 T-SQL 语句来创建新的服务器审核（请选择自己的审核名称，并使用在前面步骤中创建的容器 URL）。 如果未指定，则 `RETENTION_DAYS` 默认为 0（无限期保留）：

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

      1. 继续[创建服务器审核规范或数据库审核规范](#createspec)

   - <a id="blobssms"></a>使用 SQL Server Management Studio (SSMS) 18（预览版）为审核日志配置 Blob 存储：

     1. 使用 SQL Server Management Studio (SSMS) UI 连接到托管实例。

     1. 展开对象资源管理器的根节点。

     1. 展开“安全性”节点，右键单击“审核”节点，然后单击“新建审核”：

        ![展开“安全性”和“审核”节点](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. 确保“审核目标”中已选择“URL”，然后单击“浏览”：

        ![浏览 Azure 存储](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. （可选）登录到 Azure 帐户：

        ![登录 Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. 从下拉列表中选择订阅、存储帐户和 Blob 容器，或者单击“创建”来创建自己的容器。 完成后，单击“确定”：

        ![选择 Azure 订阅、存储帐户和 Blob 容器](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. 在“创建审核”对话框中单击“确定”。

1. <a id="createspec"></a>将 Blob 容器配置为审核日志的目标后，像使用 SQL Server 时一样创建服务器审核规范或数据库审核规范：

   - [创建服务器审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [创建数据库审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. 启用在步骤 6 中创建的服务器审核：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

了解更多信息：

- [托管实例、Azure SQL DB 和 SQL Server 之间的审核差异](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-log-analytics"></a>将服务器的审核设置为事件中心或 Log Analytics

使用 Azure Monitor 将托管实例的审核日志发送到事件中心或 Log Analytics。 本部分介绍如何进行以下配置：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到 SQL 数据库托管实例。

2. 单击“诊断设置”。

3. 单击“启用诊断”。 如果已启用诊断，则会显示“+ 添加诊断设置”。

4. 在日志列表中选择“SQLSecurityAuditEvents”。

5. 选择事件中心和/或 Log Analytics 的审核事件的目标。 为每个目标配置所需的参数（例如，Log Analytics 工作区）。

6. 单击“ **保存**”。

    ![配置诊断设置](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. 使用“SQL Server Management Studio (SSMS)”或任何其他支持的客户端连接到托管实例。

8. 执行下面的 T-SQL 语句，创建服务器审核：

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. 像针对 SQL Server 那样创建一个服务器审核规范或数据库审核规范：

   - [创建服务器审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [创建数据库审核规范 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 启用在步骤 7 中创建的服务器审核：
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>使用审核日志

### <a name="consume-logs-stored-in-azure-storage"></a>使用存储在 Azure 存储中的日志

可使用多种方法查看 blob 审核日志。

- 使用系统函数 `sys.fn_get_audit_file` (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file 文档](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 可使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等工具浏览审核日志。 在 Azure 存储中，审核日志作为 Blob 文件的集合保存在定义为存储审核日志的容器中。 有关存储文件夹层次、命名约定和日志格式的详细信息，请参阅 [Blob 审核日志格式参考](https://go.microsoft.com/fwlink/?linkid=829599)。

- 有关审核日志使用方法的完整列表，请参阅 [SQL 数据库审核入门](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)。

  > [!IMPORTANT]
  > 对于托管实例，当前不可以从 Azure 门户查看审核记录（“审核记录”窗格）。

### <a name="consume-logs-stored-in-event-hub"></a>使用存储在事件中心中的日志

若要使用事件中心的审核日志数据，需设置一个流来使用事件并将其写入到目标。 有关详细信息，请参阅 Azure 事件中心文档。

### <a name="consume-and-analyze-logs-stored-in-log-analytics"></a>使用和分析存储在 Log Analytics 中的日志

如果将审核日志写入到 Log Analytics，则可以在 Log Analytics 工作区中使用它们，可以在其中对审核数据运行高级搜索。 首先，导航到 Log Analytics，在“常规”部分下单击“日志”，然后输入一个简单查询（例如：`search "SQLSecurityAuditEvents"`）以查看审核日志。  

有了 Log Analytics，就可以使用集成的搜索和自定义仪表板来轻松分析所有工作负荷和服务器上的数百万记录，获得实时操作见解。 有关 Log Analytics 搜索语言和命令的其他有用信息，请参阅 [Log Analytics 搜索参考](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>托管实例、Azure SQL 数据库和 SQL Server 之间的审核差异

托管实例、Azure SQL 数据库与本地 SQL Server 之间的主要 SQL 审核差异为：

- 在托管实例中，SQL 审核在服务器级别工作，在 Azure Blob 存储帐户中存储 `.xel` 日志文件。
- 在 Azure SQL 数据库中，SQL 审核在数据库级别工作。
- 在本地 SQL Server/虚拟机中，SQL 审核在服务器级别工作，但在文件系统/Windows 事件日志中存储事件。

托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 **不支持**文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了新语法 `TO URL`，该语法允许指定用于放置 `.xel` 文件的 Azure Blob 存储容器的 URL。
- 提供了新语法 `TO EXTERNAL MONITOR` 以启用事件中心和 Log Analytics 目标。
- **不支持**语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。
- **不支持**关闭选项。
- **不支持** `queue_delay` 为 0。

## <a name="next-steps"></a>后续步骤

- 有关审核日志使用方法的完整列表，请参阅 [SQL 数据库审核入门](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)。
- 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。

<!--Image references-->









