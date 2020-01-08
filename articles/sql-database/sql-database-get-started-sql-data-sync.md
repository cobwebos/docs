---
title: 设置数据同步
description: 本教程介绍了如何设置 Azure SQL 数据同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461530"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>教程：在 Azure SQL 数据库和本地 SQL Server 之间设置 SQL 数据同步

本教程将介绍如何创建包含 Azure SQL 数据库和 SQL Server 实例的同步组，从而设置 Azure SQL 数据同步。 同步组进行了自定义配置，并根据设置的计划进行同步。

阅读本教程的前提是，至少具有 SQL 数据库和 SQL Server 领域的一些经验。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步跨云和本地数据库同步数据](sql-database-sync-data.md)。

有关如何配置 SQL 数据同步的 PowerShell 示例，请参阅[如何在 Azure SQL 数据库](scripts/sql-database-sync-data-between-sql-databases.md)或 [Azure SQL 数据库和 SQL Server 本地数据库](scripts/sql-database-sync-data-between-azure-onprem.md)之间进行同步

> [!IMPORTANT]
> 目前，Azure SQL 数据同步不支持 Azure SQL 数据库托管实例。

## <a name="create-sync-group"></a>创建同步组

1. 请参阅[Azure 门户](https://portal.azure.com)，查找 SQL 数据库。 搜索并选择“SQL 数据库”。

    ![搜索 SQL 数据库，Microsoft Azure 门户](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. 选择要用作数据同步的中心数据库的数据库。

    ![从 SQL 数据库列表中选择，Microsoft Azure 门户](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > 中心数据库是同步拓扑的中央终结点，其中同步组具有多个数据库终结点。 同步组中具有终结点的所有其他成员数据库会与中心数据库进行同步。

1. 在选定数据库的 " **SQL 数据库**" 菜单中，选择 "**同步到其他数据库**"。

    ![同步到其他数据库、SQL database Microsoft Azure 门户](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. 在“同步到其他数据库”页中，选择“新建同步组”。 “新建同步组”页随即打开，其中突出显示“创建同步组(步骤 1)”。

   ![步骤 1 设置](media/sql-database-get-started-sql-data-sync/stepone.png)

   在“创建数据同步组”页中，请更改以下设置：

   | 设置                        | Description |
   | ------------------------------ | ------------------------------------------------- |
   | **同步组名称** | 输入新同步组的名称。 此名称不同于数据库本身的名称。 |
   | **同步元数据数据库** | 选择创建数据库（推荐）或使用现有数据库。<br/><br/>如果选择“新建数据库”，请选择“创建新数据库”。 然后在“SQL 数据库”页中，命名并配置新数据库，再选择“确定”。<br/><br/>如果选择“使用现有数据库”，请从列表中选择数据库。 |
   | **自动同步** | 选择“开”或“关”。<br/><br/>如果选择“开”，请在“同步频率”部分中输入数字，然后选择“秒”、“分钟”、“小时”或“天”。 |
   | **冲突解决方法** | 选择“中心胜出”或“成员胜出”。<br/><br/>“中心胜出”表示发生冲突时，中心数据库中的数据将覆盖成员数据库中的冲突数据。<br/><br/>“成员胜出”表示发生冲突时，成员数据库中的数据将覆盖中心数据库中的冲突数据。 |

   > [!NOTE]
   > Microsoft 建议新建空的数据库，以用作“同步元数据数据库”。 SQL 数据同步在此数据库中创建表，并经常运行工作负载。 此数据库作为所选区域中所有同步组的“同步元数据数据库”共享，如果不删除该区域中的所有同步组和同步代理，则无法更改数据库或数据库名称。

   选择“确定”，并等待创建和部署同步组。

## <a name="add-sync-members"></a>添加同步成员

创建并部署新的同步组后，“新建同步组”页中将突出显示“添加同步成员(步骤 2)”。

在“中心数据库”部分中，输入中心数据库所在的 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。

![步骤 2 设置](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>添加 Azure SQL 数据库

在“成员数据库”部分中，视需要通过选择“添加 Azure SQL 数据库”，将 Azure SQL 数据库添加到同步组中。 此时，“配置 Azure SQL 数据库”页随即打开。

  ![步骤 2 - 配置数据库](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  在“配置 Azure SQL 数据库”页中，更改以下设置：

  | 设置                       | Description |
  | ----------------------------- | ------------------------------------------------- |
  | **同步成员名称** | 提供新同步成员的名称。 此名称不同于数据库本身的名称。 |
  | **订阅** | 选择关联的 Azure 订阅，以用于计费。 |
  | **Azure SQL Server** | 选择现有 SQL 数据库服务器。 |
  | **Azure SQL 数据库** | 选择现有 SQL 数据库。 |
  | **同步方向** | 选择“双向同步”、“向中心同步”或“从中心同步”。 |
  | “用户名”和“密码” | 输入成员数据库所在的 SQL 数据库服务器的现有凭据。 请勿在此部分中输入新凭据。 |

  选择“确定”，并等待新同步成员创建和部署完成。

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>添加本地 SQL Server 数据库

在“成员数据库”部分中，请根据需要选择“添加本地数据库”，从而将本地 SQL Server 数据库添加到同步组。 随即打开“配置本地”页，可以在其中执行以下操作：

1. 选择“选择同步代理网关”。 “选择同步代理”页随即打开。

   ![创建同步代理](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. 在“选择同步代理”页中，选择是使用现有代理还是创建代理。

   如果选择“现有代理”，请从列表中选择现有代理。

   如果选择“新建代理”，请执行以下操作：

   1. 通过提供的链接下载 Data Sync Agent，并将其安装在 SQL Server 所在的计算机上。 还可以直接从 [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693) 下载代理。

      > [!IMPORTANT]
      > 必须在防火墙中打开出站 TCP 端口 1433，以便客户端代理能够与服务器进行通信。

   1. 输入代理名称。

   1. 选择“创建并生成密钥”并将代理密钥复制到剪贴板。

   1. 选择“确定”，关闭“选择同步代理”页。

1. 在 SQL Server 计算机上，找到并运行客户端同步代理应用程序。

   ![数据同步客户端代理应用程序](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. 在同步代理应用程序中，选择“提交代理密钥”。 此时，“同步元数据数据库配置”对话框打开。

    1. 在“同步元数据数据库配置”对话框中，粘贴从 Azure 门户复制的代理密钥。 还需要输入元数据数据库所在 Azure SQL 数据库服务器的现有凭据。 （如果创建了元数据数据库，则此数据库与中心数据库位于同一服务器上。）选择 **"确定"** ，并等待配置完成。

        ![输入代理密钥和服务器凭据](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > 如果看到防火墙错误消息，请在 Azure 上创建防火墙规则，以允许来自 SQL Server 计算机的传入流量。 可以在门户中或在 SQL Server Management Studio (SSMS) 中手动创建规则。 在 SSMS 中，输入其名称 <hub_database_name>.database.windows.net，即可连接到 Azure 上的中心数据库。

    1. 选择“注册”以向代理注册 SQL Server 数据库。 此时，“SQL Server 数据库配置”对话框打开。

        ![添加和配置 SQL Server 数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. 在“SQL Server 配置”对话框中，选择是使用 SQL Server 身份验证还是使用 Windows 身份验证进行连接。 如果选择 SQL Server 身份验证，请输入现有凭据。 提供 SQL Server 名称和要同步的数据库的名称，然后选择“测试连接”测试设置。 然后选择“保存”，注册的数据库将显示在列表中。

        ![SQL Server 数据库现已注册](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. 关闭客户端同步代理应用。

1. 在门户的“配置本地数据库”页中，选择“选择数据库”。

1. 在“选择数据库”页的“同步成员名称”字段中，输入新同步成员的名称。 此名称不同于数据库本身的名称。 从列表中选择数据库。 在“同步方向”字段中，选择“双向同步”、“向中心同步”或“从中心同步”。

    ![选择本地数据库](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. 选择“确定”，关闭“选择数据库”页。 再选择“确定”，关闭“配置本地数据库”页，并等待新同步成员创建和部署完成。 最后，选择“确定”，关闭“选择同步成员”页。

> [!NOTE]
> 要连接到 SQL 数据同步和本地代理，请将自己的用户名添加到角色 DataSync_Executor。 SQL 数据同步在 SQL Server 实例中创建此角色。

## <a name="configure-sync-group"></a>配置同步组

创建并部署新同步组成员后，“新建同步组”页中将突出显示“配置同步组(步骤 3)”。

![步骤 3 设置](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. 在“表”页中，依次选择同步组成员列表中的数据库和“刷新架构”。

1. 从列表中，选择要同步的表。默认情况下，所有列都处于选中状态，因此禁用不想同步的列的复选框。请确保选中 "主键" 列。

1. 选择“保存”。

1. 默认情况下，在计划或手动运行数据库之前，不会同步数据库。 要运行手动同步，请导航到 Azure 门户中的 SQL 数据库，选择“同步到其他数据库”，然后选择同步组。 “数据同步”页随即打开。 选择“同步”。

    ![手动同步](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>常见问题

数据同步以什么频率同步数据？

同步之间的最短持续时间为五分钟。

SQL 数据同步是否能完全创建表？

如果目标数据库中缺少同步架构表，则 SQL 数据同步会使用所选择的列创建进行创建。 但是，由于以下原因，这不会导致完全保真架构：

- 仅在目标表中创建所选的列。 将忽略未选中的列。
- 仅在目标表中创建选定的列索引。 对于未选中的列，将忽略这些索引。
- 不会创建 XML 类型列的索引。
- 不会创建 CHECK 约束。
- 不会创建源表上的触发器。
- 不会创建视图和存储过程。

考虑到这些限制，我们的建议如下：

- 对于生产环境，请自行创建完全保真架构。
- 在试验服务时，请使用自动预配功能。

**为什么会看到没有创建的表？**

数据同步在数据库中创建其他表用于跟踪更改。 请不要删除这些表，否则数据同步会停止运行。

**同步后的数据是否具有收敛性？**

不一定。 取具有一个中心和三个辐射（A、B 和 C）的同步组，其中同步为中心到 A、中心到 B 和中心到 C。如果在中心到 A 同步后对数据库 A 进行了更改，则在下一次同步任务前，该更改不会写入数据库 B 或数据库 C。

**如何将架构更改应用到同步组？**

手动进行所有架构更改并对其进行传播。

1. 将架构更改手动复制到中心以及所有同步成员。
1. 更新同步架构。

添加新表和新列：

新表和新列在添加到同步架构之前不会影响当前同步，并且数据同步会将其忽略。 添加新的数据库对象时，请遵循以下顺序：

1. 将新表或新列添加到中心，然后添加到所有同步成员。
1. 将新表或新列添加到同步架构。
1. 开始将值插入新表和新列中。

更改列的数据类型：

更改现有列的数据类型时，数据同步会继续运行，前提是新值属于在同步架构中定义的原始数据类型。 例如，如果在源数据库中将类型从 int 更改为 bigint，除非插入的值对于 int 数据类型来说过大，否则数据同步会继续运行。 要完成此更改，请将架构更改手动复制到中心以及所有同步成员，然后更新同步架构。

**如何使用数据同步导出和导入数据库？**

在将数据库导出为 .bacpac 文件，并导入要创建数据库的文件后，请执行以下操作以在新数据库中使用数据同步：

1. 使用[此脚本](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)清理新数据库上的数据同步对象和其他表。 该脚本从数据库中删除所有必需的数据同步对象。
1. 重新创建包含新数据库的同步组。 如果不再需要旧同步组，请删除它。

**在哪里可以找到有关客户端代理的信息？**

有关客户端代理的常见问题解答，请参阅[代理常见问题解答](sql-database-data-sync-agent.md#agent-faq)。

## <a name="next-steps"></a>后续步骤

祝贺。 你已创建了一个包含 SQL 数据库实例和 SQL Server 数据库的同步组。

有关 SQL 数据同步的详细信息，请参阅：

- [Azure SQL 数据同步的 Data Sync Agent](sql-database-data-sync-agent.md)
- [最佳做法](sql-database-best-practices-data-sync.md)和[如何解决 Azure SQL 数据同步问题](sql-database-troubleshoot-data-sync.md)
- [使用 Azure Monitor 日志监视 SQL 数据同步](sql-database-sync-monitor-oms.md)
- [使用 Transact-SQL 更新同步架构](sql-database-update-sync-schema.md) 或使用 [PowerShell](scripts/sql-database-sync-update-schema.md) 更新同步架构

有关 SQL 数据库的详细信息，请参阅：

- [SQL 数据库概述](sql-database-technical-overview.md)
- [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
