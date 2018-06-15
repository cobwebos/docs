---
title: 设置 Azure SQL 数据同步（预览版）| Microsoft Docs
description: 本教程演示如何设置 Azure SQL 数据同步（预览版）
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 7598484a20d2d719c84e1789664ac2b40c2d0639
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647844"
---
# <a name="set-up-sql-data-sync-preview"></a>设置 SQL 数据同步（预览版）
本教程将介绍如何创建包含 Azure SQL 数据库和 SQL Server 实例的混合同步组，从而设置 Azure SQL 数据同步。 新的同步组进行了全面配置，可根据所设定的计划进行同步。

阅读本教程的前提是，至少具有 SQL 数据库和 SQL Server 领域的一些经验。 

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

有关显示如何配置 SQL 数据同步的完整 PowerShell 示例，请参阅下列文章：
-   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
-   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>第 1 步 - 创建同步组

### <a name="locate-the-data-sync-settings"></a>找到数据同步设置

1.  在浏览器中，转到 Azure 门户。

2.  在此门户中，通过仪表板或工具栏上的“SQL 数据库”图标找到 SQL 数据库。

    ![Azure SQL 数据库列表](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  在“SQL 数据库”页中，选择要用作数据同步的中心数据库的现有 SQL 数据库。“SQL 数据库”页随即打开。

    中心数据库是同步拓扑的中央终结点，在同步拓扑中，一个同步组具有多个数据库终结点。 同一同步组中的所有其他数据库终结点（即所有成员数据库）将与中心数据库进行同步。

4.  在选定数据库的“SQL 数据库”页中，选择“同步到其他数据库”。 “数据同步”页随即打开。

    ![“同步到其他数据库”选项](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>新建同步组

1.  在“数据同步”页中，选择“新建同步组”。 “新建同步组”页随即打开，其中将突出显示第 1 步“创建同步组”。 “创建数据同步组”页也随即打开。

2.  在“创建数据同步组”页中，执行以下操作：

    1.  在“同步组名称”字段中，输入新同步组的名称。

    2.  在“同步元数据数据库”部分中，选择是新建数据库（推荐），还是使用现有数据库。

        > [!NOTE]
        > Microsoft 建议新建空的数据库，以用作同步元数据数据库。 SQL 数据同步在此数据库中创建表，并经常运行工作负载。 此数据库自动共享为选定区域中所有同步组的同步元数据数据库。 只有在删除同步元数据数据库后，才能更改此数据库或其名称。

        如果选中“新建数据库”，请选择“创建新数据库”。 “SQL 数据库”页随即打开。 在“SQL 数据库”页中，命名并配置新数据库。 然后选择“确定”。

        如果选中“使用现有数据库”，请从列表中选择数据库。

    3.  在“自动同步”部分中，先选择“开”或“关”。

        如果选择“开”，请在“同步频率”部分中输入一个数字，再选择“秒”、“分钟”、“小时”或“天”。

        ![指定同步频率](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  在“冲突解决”部分中，选择“中心胜出”或“成员胜出”。

        “中心胜出”意味着，当发生冲突时，中心数据库中的数据将覆盖成员数据库中的冲突数据。 “成员胜出”意味着，当发生冲突时，成员数据库中的数据将覆盖中心数据库中的冲突数据。 

        ![指定如何解决冲突](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  选择“确定”，并等待新同步组创建和部署完成。

## <a name="step-2---add-sync-members"></a>第 2 步 - 添加同步成员

创建并部署新同步组后，“新建同步组”页中将突出显示第 2 步“添加同步成员”。

在“中心数据库”部分中，输入中心数据库所在 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。

![已将中心数据库添加到同步组](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>添加 Azure SQL 数据库

在“成员数据库”部分中，请根据需要选择“添加 Azure 数据库”，从而将 Azure SQL 数据库添加到同步组。 “配置 Azure 数据库”页随即打开。

在“配置 Azure 数据库”页中，执行以下操作：

1.  在“同步成员名称”字段中，输入新同步成员的名称。 此名称不同于数据库本身的名称。

2.  在“订阅”字段中，选择关联的 Azure 订阅，以用于计费。

3.  在“Azure SQL Server”字段中，选择现有的 SQL 数据库服务器。

4.  在“Azure SQL 数据库”字段中，选择现有的 SQL 数据库。

5.  在“同步方向”字段中，选择“双向同步”、“向中心同步”或“从中心同步”。

    ![添加新 SQL 数据库同步成员](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  在“用户名”和“密码”字段中，输入成员数据库所在 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。

7.  选择“确定”，并等待新同步成员创建和部署完成。

    ![已添加新 SQL 数据库同步成员](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> 添加本地 SQL Server 数据库

在“成员数据库”部分中，请根据需要选择“添加本地数据库”，从而将本地 SQL Server 数据库添加到同步组。 “配置本地数据库”页随即打开。

在“配置本地数据库”页中，执行以下操作：

1.  选择“选择同步代理网关”。 “选择同步代理”页随即打开。

    ![选择同步代理网关](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  在“选择同步代理网关”页中，选择是使用现有代理，还是新建代理。

    如果选中“现有代理”，请从列表中选择现有代理。

    如果选中“新建代理”，请执行以下操作：

    1.  通过提供的链接下载客户端同步代理软件，并将它安装在 SQL Server 所在的计算机上。
 
        > [!IMPORTANT]
        > 必须在防火墙中打开出站 TCP 端口 1433，以便客户端代理能够与服务器进行通信。


    2.  输入代理名称。

    3.  选择“创建并生成密钥”。

    4.  将代理密钥复制到剪贴板。
        
        ![新建同步代理](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  选择“确定”，关闭“选择同步代理”页。

    6.  在 SQL Server 计算机上，找到并运行客户端同步代理应用程序。

        ![数据同步客户端代理应用程序](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  在同步代理应用程序中，选择“提交代理密钥”。 此时，“同步元数据数据库配置”对话框打开。

    8.  在“同步元数据数据库配置”对话框中，粘贴从 Azure 门户复制的代理密钥。 还需要输入元数据数据库所在 Azure SQL 数据库服务器的现有凭据。 （如果是新建的元数据数据库，此数据库与中心数据库位于同一服务器上。）选择“确定”，并等待配置完成。

        ![输入代理密钥和服务器凭据](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   如果在此时看到防火墙错误消息，必须在 Azure 上创建防火墙规则，以允许来自 SQL Server 计算机的传入流量。 可以在门户中手动创建此规则，但在 SQL Server Management Studio (SSMS) 中创建更容易。 在 SSMS 中，尝试连接到 Azure 上的中心数据库。 输入 <hub_database_name>.database.windows.net 作为名称。 按照对话框中的步骤操作，配置 Azure 防火墙规则。 再返回到客户端同步代理应用程序。

    9.  在客户端同步代理应用程序中，单击“注册”，向代理注册 SQL Server 数据库。 此时，“SQL Server 数据库配置”对话框打开。

        ![添加和配置 SQL Server 数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. 在“SQL Server 数据库配置”对话框中，选择是使用 SQL Server 身份验证，还是使用 Windows 身份验证进行连接。 如果选择 SQL Server 身份验证，请输入现有凭据。 输入 SQL Server 名称和要同步的数据库的名称。选择“测试连接”，以测试设置。 再选择“保存”。 此时，已注册的数据库显示在列表中。

        ![SQL Server 数据库现已注册](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. 此时，可以关闭客户端同步代理应用程序。

    12. 在门户的“配置本地数据库”页中，选择“选择数据库”。 “选择数据库”页随即打开。

    13. 在“选择数据库”页的“同步成员名称”字段中，输入新同步成员的名称。 此名称不同于数据库本身的名称。 从列表中选择数据库。 在“同步方向”字段中，选择“双向同步”、“向中心同步”或“从中心同步”。

        ![选择本地数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. 选择“确定”，关闭“选择数据库”页。 再选择“确定”，关闭“配置本地数据库”页，并等待新同步成员创建和部署完成。 最后，单击“确定”，关闭“选择同步成员”页。

        ![已将本地数据库添加到同步组](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  若要连接到 SQL 数据同步和本地代理，请向角色 `DataSync_Executor` 添加自己的用户名。 SQL 数据同步在 SQL Server 实例中创建此角色。

## <a name="step-3---configure-sync-group"></a>第 3 步 - 配置同步组

创建并部署新同步组成员后，“新建同步组”页中将突出显示第 3 步“配置同步组”。

1.  在“表”页中，依次选择同步组成员列表中的数据库和“刷新架构”。

2.  在可用表列表中，选择要同步的表。

    ![选择要同步的表](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  表中的所有列默认处于选中状态。 如果不想同步所有列，请取消选中不想同步的列的复选框。请务必保持主键列的选中状态不变。

    ![选择要同步的字段](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  最后，选择“保存”。

## <a name="faq-about-setup-and-configuration"></a>设置和配置的常见问题解答

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>数据同步以什么频率同步数据？ 
最小频率是每隔五分钟。

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL 数据同步是否能完全创建和预配表？

如果同步架构表尚未在目标数据库中创建，则 SQL 数据同步（预览版）将使用所选列进行创建。 但是，此行为不会导致完全保真架构，原因如下：

-   在目标表中仅创建所选的列。 如果源表中的某些列不是同步组的一部分，则不会在目标表中预配这些列。

-   仅为所选列创建索引。 如果源表索引包含不是同步组一部分的列，则不会在目标表中预配这些索引。

-   不会预配 XML 类型列上的索引。

-   不会预配 CHECK 约束。

-   不会预配源表上的现有触发器。

-   不会在目标数据库上创建视图和存储的过程。

考虑到这些限制，我们的建议如下：
-   对于生产环境，请自行预配完全保真架构。
-   若要试用服务，SQL 数据同步（预览版）的自动预配功能非常有用。

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>为什么出现了未创建的表？  
数据同步在数据库中创建用于跟踪的端表。 请不要删除这些表，否则数据同步会停止工作。

### <a name="is-my-data-convergent-after-a-sync"></a>同步后我的数据是否具有收敛性？

不一定。 在具有一个中心和三个辐射（A、B 和 C）的同步组中，同步为中心到 A、中心到 B，和中心到 C。如果在中心到 A 同步后更改数据库 A，则下一次同步任务前，更改不会写入数据库 B 或数据库 C。

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>如何将架构更改应用到同步组？

必须手动进行所有架构更改并对其进行传播。
1. 将架构更改手动复制到中心以及所有同步成员。
2. 更新同步架构。

**添加新的表和列**。 新的表和列不影响当前的同步。数据同步会忽略新的表和列，除非你将它们添加到同步架构。 添加新的数据库对象时，需要遵循的最佳顺序如下：
1. 将新的表或列添加到中心，然后添加到所有同步成员。
2. 将新的表或列添加到同步架构。
3. 开始向新的表和列插入值。

**更改列的数据类型**。 更改现有列的数据类型时，数据同步会继续运行，前提是新值属于在同步架构中定义的原始数据类型。 例如，如果在源数据库中将类型从 **int** 更改为 **bigint**，则数据同步会继续运行，除非插入的值对于 **int** 数据类型来说过大。 若要完成此更改，请将架构更改手动复制到中心以及所有同步成员，然后更新同步架构。

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>如何使用数据同步导出和导入数据库？
将数据库导出为 `.bacpac` 文件，并导入文件来新建数据库后，必须执行以下两步操作，才能在新数据库中使用数据同步：
1.  使用[此脚本](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql)清理新数据库上的数据同步对象和端表。 此脚本将从数据库中删除所有相应数据同步对象。
2.  重新创建包含新数据库的同步组。 如果不再需要旧同步组，请删除它。

## <a name="faq-about-the-client-agent"></a>有关客户端代理的常见问题解答

### <a name="why-do-i-need-a-client-agent"></a>为什么需要客户端代理？

SQL 数据同步（预览版）服务通过客户端代理与 SQL Server 数据库进行通信。 此安全功能可防止与防火墙后的数据库进行直接通信。 SQL 数据同步（预览版）服务与代理通信时，使用加密连接和唯一令牌或代理密钥来执行此操作。 SQL Server 数据库使用连接字符串和代理密钥对代理进行身份验证。 这种设计为数据提供高度安全性。

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>可以运行多少个本地代理 UI 实例？

只能运行一个 UI 实例。

### <a name="how-can-i-change-my-service-account"></a>如何更改服务帐户？

安装客户端代理后，更改服务帐户的唯一方法是卸载它，然后使用新的服务帐户安装新的客户端代理。

### <a name="how-do-i-change-my-agent-key"></a>如何更改我的代理密钥？

一个代理只能使用一次代理密钥。 删除并重新安装新代理后，不能重复使用它，它也不能被多个代理使用。 如果需要为现有代理创建新密钥，必须确保使用客户端代理和 SQL 数据同步（预览版）服务记录相同密钥。

### <a name="how-do-i-retire-a-client-agent"></a>如何停用客户端代理？

若要立即使代理失效或停用代理，请在门户中重新生成其密钥，但不要在代理 UI 中提交。 无论相应的代理处于联机还是脱机状态，重新生成密钥都会使以前的密钥失效。

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>如何将客户端代理移至另一台计算机？

如果想要从另一台计算机上运行本地代理，请执行以下操作：

1. 在所需的计算机上安装代理。

2. 登录 SQL 数据同步（预览版）门户，为新代理重新生成代理密钥。

3. 使用新代理的 UI 提交新代理密钥。

4. 客户端代理下载以前已注册的本地数据库列表时，请稍候。

5. 为显示为无法访问的所有数据库提供数据库凭据。 这些数据库必须可从安装代理的新计算机上访问。

## <a name="next-steps"></a>后续步骤
祝贺。 已创建了一个包含 SQL 数据库实例和 SQL Server 数据库的同步组。

有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   [Azure SQL 数据同步最佳实践](sql-database-best-practices-data-sync.md)
-   [使用 Log Analytics 监视 Azure SQL 数据同步](sql-database-sync-monitor-oms.md)
-   [Azure SQL 数据同步问题疑难解答](sql-database-troubleshoot-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
