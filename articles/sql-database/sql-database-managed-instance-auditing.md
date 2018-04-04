---
title: Azure SQL 数据库托管实例审核 | Microsoft Docs
description: 了解如何通过 T-SQL 开始使用 Azure SQL 数据库托管实例审核
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 3d5a4ad3f4046dfdfe6eb3f7ddd931ccb240b1a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>开始使用 Azure SQL 数据库托管实例审核

[Azure SQL 数据库托管实例](sql-database-managed-instance.md)审核功能可跟踪数据库事件，并将事件写入到 Azure 存储帐户中的审核日志。 审核还可：
- 帮助保持合规性、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。
- 实现并促进遵从合规标准，但不能保证合规性。 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。


## <a name="set-up-auditing-for-your-server"></a>为服务器设置审核

以下部分介绍了托管实例上的审核配置。
1. 转到 [Azure 门户](https://portal.azure.com)。
2. 以下步骤创建一个在其中存储审核日志的 Azure 存储**容器**。

   - 导航到要在其中存储审核日志的 Azure 存储。

     > [!IMPORTANT]
     > 请使用与托管实例服务器位于同一区域中的存储帐户以避免跨区域读取/写入。

   - 在存储帐户中，转到“概述”，然后单击“Blob”。

     ![导航窗格][1]

   - 在顶部菜单中，单击“+ 容器”以创建新容器。

     ![导航窗格][2]

   - 提供一个容器**名称**，将公共访问级别设置为“专用”，然后单击“确定”。

     ![导航窗格][3]

   - 在容器列表中，单击新创建的容器，然后单击“容器属性”。

     ![导航窗格][4]

   - 通过单击复制图标来复制容器 URL并保存该 URL（例如在记事本中）供将来使用。 容器 URL 格式应当为 `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![导航窗格][5]

3. 下列步骤生成一个 Azure 存储 **SAS 令牌**，该令牌用来向“托管实例审核”授予对存储帐户的访问权限。

   - 导航到在前面的步骤中你在其中创建了容器的 Azure 存储帐户。

   - 在“存储设置”菜单中单击“共享访问签名”。

     ![导航窗格][6]

   - 如下所述配置 SAS：
     - **允许的服务**：Blob
     - **开始日期**：为避免与时区相关的问题，建议使用昨天的日期。
     - **结束日期**：选择此 SAS 令牌的到期日期。 

       > [!NOTE]
       > 在到期时续订令牌，以避免审核失败。

     - 单击“生成 SAS”。

       ![导航窗格][7]

   - 单击“生成 SAS”令牌后，SAS 令牌将出现在底部。 通过单击复制图标来复制令牌并保存该令牌（例如在记事本中）供将来使用。

     > [!IMPORTANT]
     > 从令牌的开头删除问号 (“?”) 字符。

     ![导航窗格][8]

4. 通过 SQL Server Management Studio (SSMS) 连接到你的托管实例。

5. 执行以下 T-SQL 语句来使用你在前面的步骤中创建的容器 URL 和 SAS 令牌**创建新凭据**：

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. 执行以下 T-SQL 语句来创建新的服务器审核（选择你自己的审核名称，使用你在前面的步骤中创建的容器 URL）：

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    如果未指定，则 `RETENTION_DAYS` 默认值为 0（无限保留期）。

    了解更多信息：
    - [托管实例、Azure SQL DB 和 SQL Server 之间的审核差异](#subheading-3)
    - [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. 像针对 SQL Server 那样创建一个服务器审核规范或数据库审核规范：
    - [创建服务器审核规范 T-SQL 指南](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [创建数据库审核规范 T-SQL 指南](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. 启用在步骤 6 中创建的服务器审核：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>分析审核日志
可使用多种方法查看 blob 审核日志。

- 使用系统函数 `sys.fn_get_audit_file` (T-SQL) 以表格格式返回审核日志数据。 有关使用此函数的详细信息，请参阅 [sys.fn_get_audit_file 文档](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 有关审核日志使用方法的完整列表，请参阅 [SQL 数据库审核入门](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing)。

> [!IMPORTANT]
> 对于托管实例，当前未提供从 Azure 门户查看审核记录的方法（“审核记录”窗格）。

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>托管实例、Azure SQL 数据库和 SQL Server 之间的审核差异

托管实例、Azure SQL 数据库与本地 SQL Server 之间的主要 SQL 审核差异为：

- 在托管实例中，SQL 审核在服务器级别工作，在 Azure Blob 存储帐户中存储 `.xel` 日志文件。
- 在 Azure SQL 数据库中，SQL 审核在数据库级别工作。
- 在本地 SQL Server/虚拟机中，SQL 审核在服务器级别工作，但在文件系统/Windows 事件日志中存储事件。

托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 **不支持**文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：
- 提供了新语法 `TO URL`，该语法允许指定用于放置 `.xel` 文件的 Azure Blob 存储容器的 URL。
- **不支持**语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。
- **不支持**关闭选项。
- **不支持** `queue_delay` 为 0。


## <a name="next-steps"></a>后续步骤

- 有关审核日志使用方法的完整列表，请参阅 [SQL 数据库审核入门](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing)。
- 有关可帮助遵从标准 Azure 计划的详细信息，请参阅 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
