---
title: "Azure SQL 数据同步入门（预览版） | Microsoft Docs"
description: "此为 Azure SQL 数据同步（预览版）入门教程。"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 2d0f9d7f32ad79f49d58165d734b9df4af862835
ms.contentlocale: zh-cn
ms.lasthandoff: 08/05/2017

---
# <a name="getting-started-with-azure-sql-data-sync-preview"></a>Azure SQL 数据同步入门（预览版）
本教程将介绍如何创建包含 Azure SQL 数据库和 SQL Server 实例的混合同步组，从而设置 Azure SQL 数据同步。 新的同步组进行了全面配置，可根据所设定的计划进行同步。

阅读本教程的前提是，至少具有 SQL 数据库和 SQL Server 领域的一些经验。 

有关 SQL 数据同步的概述，请参阅[同步数据](sql-database-sync-data.md)。

有关显示如何配置 SQL 数据同步的完整 PowerShell 示例，请参阅下列文章：
-   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
-   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!NOTE]
> 有关 Azure SQL 数据同步的完整技术文档集以前位于 MSDN 中，现在以 .pdf 文档提供。 可从[此处](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)下载。

## <a name="step-1---create-sync-group"></a>第 1 步 - 创建同步组

### <a name="locate-the-data-sync-settings"></a>找到数据同步设置

1.  在浏览器中，转到 Azure 门户。

2.  在此门户中，通过仪表板或工具栏上的“SQL 数据库”图标找到 SQL 数据库。

    ![Azure SQL 数据库列表](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  在“SQL 数据库”边栏选项卡中，选择要用作数据同步的中心数据库的现有 SQL 数据库。 此时，选定数据库的“SQL 数据库”边栏选项卡打开。

4.  在选定数据库的“SQL 数据库”边栏选项卡中，选择“同步到其他数据库”。 此时，“数据同步”边栏选项卡打开。

    ![“同步到其他数据库”选项](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>新建同步组

1.  在“数据同步”边栏选项卡中，选择“新建同步组”。 此时，“新建同步组”边栏选项卡打开，其中突出显示第 1 步“创建同步组”。 “创建数据同步组”边栏选项卡也随即打开。

2.  在“创建数据同步组”边栏选项卡中，执行以下操作：

    1.  在“同步组名称”字段中，输入新同步组的名称。

    2.  在“同步元数据数据库”部分中，选择是新建数据库（推荐），还是使用现有数据库。

        > [!NOTE]
        > Microsoft 建议新建空的数据库，以用作同步元数据数据库。 SQL 数据同步在此数据库中创建表，并经常运行工作负载。 此数据库自动共享为选定区域中所有同步组的同步元数据数据库。 只有在删除同步元数据数据库后，才能更改此数据库、数据库名称或服务级别。

        如果选中“新建数据库”，请选择“创建新数据库”。 此时，“SQL 数据库”边栏选项卡打开。 在“SQL 数据库”边栏选项卡中，命名并配置新数据库。 然后选择“确定”。

        如果选中“使用现有数据库”，请从列表中选择数据库。

    3.  在“自动同步”部分中，先选择“开”或“关”。

        如果选择“开”，请在“同步频率”部分中输入一个数字，再选择“秒”、“分钟”、“小时”或“天”。

        ![指定同步频率](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  在“冲突解决”部分中，选择“中心胜出”或“成员胜出”。

        ![指定如何解决冲突](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  选择“确定”，并等待新同步组创建和部署完成。

## <a name="step-2---add-sync-members"></a>第 2 步 - 添加同步成员

创建并部署新同步组后，“新建同步组”边栏选项卡中突出显示第 2 步“添加同步成员”。

在“中心数据库”部分中，输入中心数据库所在 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。

![已将中心数据库添加到同步组](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

## <a name="add-an-azure-sql-database"></a>添加 Azure SQL 数据库

在“成员数据库”部分中，请根据需要选择“添加 Azure 数据库”，从而将 Azure SQL 数据库添加到同步组。 此时，“配置 Azure 数据库”边栏选项卡打开。

在“配置 Azure 数据库”边栏选项卡中，执行以下操作：

1.  在“同步成员名称”字段中，输入新同步成员的名称。 此名称不同于数据库本身的名称。

2.  在“订阅”字段中，选择关联的 Azure 订阅，以用于计费。

3.  在“Azure SQL Server”字段中，选择现有的 SQL 数据库服务器。

4.  在“Azure SQL 数据库”字段中，选择现有的 SQL 数据库。

5.  在“同步方向”字段中，选择“双向同步”、“向中心同步”或“从中心同步”。

    ![添加新 SQL 数据库同步成员](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  在“用户名”和“密码”字段中，输入成员数据库所在 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。

7.  选择“确定”，并等待新同步成员创建和部署完成。

    ![已添加新 SQL 数据库同步成员](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

## <a name="add-an-on-premises-sql-server-database"></a>添加本地 SQL Server 数据库

在“成员数据库”部分中，请根据需要选择“添加本地数据库”，从而将本地 SQL Server 数据库添加到同步组。 此时，“配置本地数据库”边栏选项卡打开。

在“配置本地数据库”边栏选项卡中，执行以下操作：

1.  选择“选择同步代理网关”。 此时，“选择同步代理”边栏选项卡打开。

    ![选择同步代理网关](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  在“选择同步代理网关”边栏选项卡中，选择是使用现有代理，还是新建代理。

    如果选中“现有代理”，请从列表中选择现有代理。

    如果选中“新建代理”，请执行以下操作：

    1.  通过提供的链接下载客户端同步代理软件，并将它安装在 SQL Server 所在的计算机上。
 
        > [!IMPORTANT]
        > 必须在防火墙中打开出站 TCP 端口 1433，以便客户端代理能够与服务器进行通信。


    2.  输入代理名称。

    3.  选择“创建并生成密钥”。

    4.  将代理密钥复制到剪贴板。
        
        ![新建同步代理](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  选择“确定”，关闭“选择同步代理”边栏选项卡。

    6.  在 SQL Server 计算机上，找到并运行客户端同步代理应用程序。

        ![数据同步客户端代理应用程序](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  在同步代理应用程序中，选择“提交代理密钥”。 此时，“同步元数据数据库配置”对话框打开。

    8.  在“同步元数据数据库配置”对话框中，粘贴从 Azure 门户复制的代理密钥。 还需要输入元数据数据库所在 Azure SQL 数据库服务器的现有凭据。 （如果是新建的元数据数据库，此数据库与中心数据库位于同一服务器上。）选择“确定”，并等待配置完成。

        ![输入代理密钥和服务器凭据](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   如果在此时看到防火墙错误消息，必须在 Azure 上创建防火墙规则，以允许来自 SQL Server 计算机的传入流量。 可以在门户中手动创建此规则，但在 SQL Server Management Studio (SSMS) 中创建更容易。 在 SSMS 中，尝试连接到 Azure 上的中心数据库。 输入 \<hub_database_name\>.database.windows.net 作为名称。 按照对话框中的步骤操作，配置 Azure 防火墙规则。 再返回到客户端同步代理应用程序。

    9.  在客户端同步代理应用程序中，单击“注册”，向代理注册 SQL Server 数据库。 此时，“SQL Server 数据库配置”对话框打开。

        ![添加和配置 SQL Server 数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. 在“SQL Server 数据库配置”对话框中，选择是使用 SQL Server 身份验证，还是使用 Windows 身份验证进行连接。 如果选择 SQL Server 身份验证，请输入现有凭据。 输入 SQL Server 名称和要同步的数据库的名称。 选择“测试连接”，以测试设置。 再选择“保存”。 此时，已注册的数据库显示在列表中。

        ![SQL Server 数据库现已注册](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. 此时，可以关闭客户端同步代理应用程序。

    12. 在门户的“配置本地数据库”边栏选项卡中，选择“选择数据库”。 此时，“选择数据库”边栏选项卡打开。

    13. 在“选择数据库”边栏选项卡的“同步成员名称”字段中，输入新同步成员的名称。 此名称不同于数据库本身的名称。 从列表中选择数据库。 在“同步方向”字段中，选择“双向同步”、“向中心同步”或“从中心同步”。

        ![选择本地数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. 选择“确定”，关闭“选择数据库”边栏选项卡。 再选择“确定”，关闭“配置本地数据库”边栏选项卡，并等待新同步成员创建和部署完成。 最后，单击“确定”，关闭“选择同步成员”边栏选项卡。

        ![已将本地数据库添加到同步组](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  若要连接到 SQL 数据同步和本地代理，请向角色 `DataSync_Executor` 添加自己的用户名。 SQL 数据同步在 SQL Server 实例中创建此角色。

## <a name="step-3---configure-sync-group"></a>第 3 步 - 配置同步组

创建并部署新同步组成员后，“新建同步组”边栏选项卡中突出显示第 3 步“配置同步组”。

1.  在“表”边栏选项卡中，依次选择同步组成员列表中的数据库和“刷新架构”。

2.  在可用表列表中，选择要同步的表。

    ![选择要同步的表](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  表中的所有列默认处于选中状态。 如果不想同步所有列，请取消选中不想同步的列的复选框。 请务必保持主键列的选中状态不变。

    ![选择要同步的字段](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  最后，选择“保存”。

## <a name="next-steps"></a>后续步骤
祝贺。 已创建了一个包含 SQL 数据库实例和 SQL Server 数据库的同步组。

有关 SQL 数据库和 SQL 数据同步的详细信息，请参阅：

-   [下载完整的 SQL 数据同步技术文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)
-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)
-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)

