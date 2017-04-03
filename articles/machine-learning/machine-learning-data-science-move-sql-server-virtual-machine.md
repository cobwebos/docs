---
title: "将数据移到 Azure 虚拟机上的 SQL Server | Microsoft Docs"
description: "将数据从平面文件或本地 SQL Server 移到 Azure 虚拟机上的 SQL Server。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 7ce5ba3c1a844104b4c14c73e05843becd69eb5f
ms.lasthandoff: 01/11/2017


---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>将数据移到 Azure 虚拟机上的 SQL Server
本主题概述了用于将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 移到 Azure 虚拟机上的 SQL Server 的选项。 将数据移到云中的这些任务是“团队数据科学流程”的一部分。

有关概述将数据移到 Azure SQL 数据库进行机器学习的选项的主题，请参阅[将数据移到 Azure SQL 数据库进行 Azure 机器学习](machine-learning-data-science-move-sql-azure.md)。

下面的**菜单**将链接至介绍如何将数据引入可以在“团队数据科学流程（TDSP，Team Data Science Process）”期间存储和处理数据的其他目标环境的主题。

[!INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

下表汇总了用于将数据移到 Azure 虚拟机上的 SQL Server 的选项。

| <b>源</b> | <b>目标：Azure 虚拟机上的 SQL Server</b> |
| --- | --- |
| <b>平面文件</b> |1.<a href="#insert-tables-bcp">命令行大容量复制实用程序 (BCP)</a><br> 2.<a href="#insert-tables-bulkquery">批量插入 SQL 查询</a><br> 3.<a href="#sql-builtin-utilities">SQL Server 中的图形内置实用程序</a> |
| <b>本地 SQL Server</b> |1.<a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">将 SQL Server 数据库部署到 Microsoft Azure 虚拟机向导</a><br> 2.<a href="#export-flat-file">导出到平面文件</a><br> 3.<a href="#sql-migration">SQL 数据库迁移向导</a> <br> 4.<a href="#sql-backup">数据库备份和还原</a><br> |

请注意，本文档假设你从 SQL Server Management Studio 或 Visual Studio 数据库资源管理器执行 SQL 命令。

> [!TIP]
> 你也可以使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)来创建和安排会将数据移动到 Azure 上的 SQL Server 虚拟机的管道。 有关更多信息，请参阅[使用 Azure 数据工厂复制数据（复制活动）](../data-factory/data-factory-data-movement-activities.md)。
>
>

## <a name="prereqs"></a>先决条件
本教程假设你具备：

* 一个 **Azure 订阅**。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户**。 在本教程中，你将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)一文。 创建存储帐户后，你将需要获取用于访问存储的帐户密钥。 请参阅[管理存储访问密钥](../storage/storage-create-storage-account.md#manage-your-storage-access-keys)。
* 在 **Azure 虚拟机上置备了 SQL Server**。 有关说明，请参阅[将 Azure SQL Server 虚拟机设置为用于高级分析的 IPython Notebook 服务器](machine-learning-data-science-setup-sql-server-virtual-machine.md)。
* 已在本地安装和配置 **Azure PowerShell**。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="filesource_to_sqlonazurevm"></a>将数据从平面文件源移动到 Azure 虚拟机上的 SQL Server
如果数据位于平面文件中（以行/列格式排列），则可以通过以下方法将它移到 Azure 上的 SQL Server 虚拟机：

1. [命令行大容量复制实用程序 (BCP)](#insert-tables-bcp)
2. [批量插入 SQL 查询](#insert-tables-bulkquery)
3. [SQL Server 中的图形内置实用程序（导入/导出、SSIS）](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>命令行大容量复制实用程序 (BCP)
BCP 是随 SQL Server 一起安装的命令行实用程序，并且是数据移动的最快方法之一。 它可跨三个 SQL Server 变体（本地 SQL Server、SQL Azure 以及 Azure 上的 SQL Server 虚拟机）运行。

> [!NOTE]
> **对于 BCP 我的数据应在哪里？**  
> 尽管并非必需，但是将包含源数据的文件置于目标 SQL Server 所在的计算机上可以更快地进行传输（网络速度与本地磁盘 IO 速度）。 可以使用各种文件复制工具（如 [AZCopy](../storage/storage-use-azcopy.md)、[Azure 存储资源管理器](http://storageexplorer.com/)，或者通过远程桌面协议 (RDP) 进行 Windows 复制/粘贴），将包含数据的平面文件移到已安装 SQL Server 的计算机。
>
>

1. 确保在目标 SQL Server 数据库上创建数据库和表。 下面是如何使用 `Create Database` 和 `Create Table` 命令执行此操作的示例：

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. 通过从已安装 bcp 的计算机的命令行执行以下命令，生成介绍表架构的格式文件。

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. 使用如下所示的 bcp 命令将数据插入数据库。 假设在同一台计算机上已安装 SQL Server，这应从命令行运行：

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **优化 BCP 插入**，请参阅以下文章[优化批量导入的指南](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx)来优化此类插入。
>
>

### <a name="insert-tables-bulkquery-parallel"></a>可实现更快数据移动的并行插入
如果你正在移动的数据很大，你可以通过在 PowerShell 脚本中同时并行执行多个 BCP 命令加快移动速度。

> [!NOTE]
> **大型数据引入**若要优化大型和超大型数据集的数据加载，请使用多个文件组和分区表对逻辑数据库和物理数据库表进行分区。 有关创建并将数据加载到分区表的详细信息，请参阅[并行加载 SQL 分区表](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。
>
>

下面的示例 PowerShell 脚本演示如何使用 bcp 并行插入：

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>批量插入 SQL 查询
[批量插入 SQL 查询](https://msdn.microsoft.com/library/ms188365)可用于将数据从基于行/列的文件导入数据库（受支持的类型在[准备用于批量导出或导入的数据 (SQL Server) ](https://msdn.microsoft.com/library/ms188609) 主题中有介绍）。

以下是一些用于批量插入的示例命令：  

1. 分析数据并设置任何自定义选项后再导入，以确保 SQL Server 数据库对于任何特殊的字段（例如日期）均假设相同的格式。 以下是如何将日期格式设置为“年-月-日”（如果你的数据包含“年-月-日”格式的日期）的示例：

        SET DATEFORMAT ymd;    
2. 使用批量导入语句导入数据：

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>SQL Server 中的内置实用程序
可以使用 SQL Server 集成服务 (SSIS) 将数据从平面文件导入到 Azure 上的 SQL Server 虚拟机。
SSIS 在两个 Studio 环境中可用。 有关详细信息，请参阅[集成服务 (SSIS) 与 Studio 环境](https://technet.microsoft.com/library/ms140028.aspx)：

* 有关 SQL Server Data Tools 的详细信息，请参阅 [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* 有关导入/导出向导的详细信息，请参阅 [SQL Server 导入和导出向导](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>将数据从本地 SQL Server 移动到 Azure 虚拟机上的 SQL Server
此外，还可以使用以下迁移策略：

1. [将 SQL Server 数据库部署到 Microsoft Azure 虚拟机向导](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [导出到平面文件](#export-flat-file)
3. [SQL 数据库迁移向导](#sql-migration)
4. [数据库备份和还原](#sql-backup)

下面我们将对每种策略进行介绍：

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>将 SQL Server 数据库部署到 Microsoft Azure 虚拟机向导
**将 SQL Server 数据库部署到 Microsoft Azure 虚拟机向导**非常简单，建议采用这种方法将数据从本地 SQL Server 实例移到 Azure 虚拟机上的 SQL Server。 有关详细的步骤以及其他备选方法的讨论，请参阅[将数据库迁移到 Azure 虚拟机上的 SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)。

### <a name="export-flat-file"></a>导出到平面文件
可以使用各种方法从本地 SQL Server 批量导出数据（如[批量导入和导出数据 (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) 主题中所述）。 本文档将举例说明“大容量复制程序 (BCP)”。 一旦数据导出到平面文件，就可以使用批量导入将其导入到另一个 SQL Server。

1. 使用 bcp 实用程序将数据从本地 SQL Server 导出到文件，如下所示

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. 使用步骤 1 中导出的表架构的 `create database` 和 `create table` 在 Azure 上的 SQL Server 虚拟机上创建数据库和表。
3. 创建一个格式文件来描述正导出/导入的数据的表架构。 [创建格式文件 (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx) 中介绍了格式文件的详细信息。

    从 SQL Server 计算机运行 BCP 时生成格式文件

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    对 SQL Server 远程运行 BCP 时生成格式文件

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. 使用[从文件源移动数据](#filesource_to_sqlonazurevm)部分中介绍的任意方法将平面文件中的数据移到 SQL Server。

### <a name="sql-migration"></a>SQL 数据库迁移向导
[SQL Server 数据库迁移向导](http://sqlazuremw.codeplex.com/)提供了一种可在两个 SQL Server 实例之间移动数据的用户友好方法。 它允许用户在源表和目标表之间映射数据架构，选择列类型和各种其他功能。 它使用隐式的大容量复制 (BCP)。 SQL 数据库迁移向导的欢迎屏幕的屏幕快照如下所示。  

![SQL Server 迁移向导][2]

### <a name="sql-backup"></a>数据库备份和还原
SQL Server 支持：

1. [数据库备份和还原功能](https://msdn.microsoft.com/library/ms187048.aspx)（至本地文件或 bacpac 导出到 blob）和[数据层应用程序](https://msdn.microsoft.com/library/ee210546.aspx)（使用 bacpac）。
2. 能够使用复制的数据库直接在 Azure 上创建 SQL Server 虚拟机或复制到现有 SQL Azure 数据库。 有关详细信息，请参阅[使用复制数据库向导](https://msdn.microsoft.com/library/ms188664.aspx)。

SQL Server Management Studio 中的数据库备份/还原选项的屏幕快照如下所示。

![SQL Server 导入工具][1]

## <a name="resources"></a>资源
[将数据库迁移到 Azure 虚拟机上的 SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Azure 虚拟机上的 SQL Server 概述](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

