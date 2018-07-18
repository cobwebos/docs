---
title: 识别 Azure 机器学习的高级分析方案 | Microsoft 文档
description: 选择适当的方案，用于执行具有团队数据科学过程的高级预测分析。
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: a194061bf443f50bed673a518ab62746f67b4a78
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838153"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>用于 Azure 机器学习中高级分析的方案
本文概述了各种示例数据源和可通过[团队数据科学过程 (TDSP)](overview.md) 处理的目标场景。 TDSP 为团队提供了一个系统性方法，可协作构建智能应用程序。 此处介绍的情景说明了数据处理工作流中的可用选项，具体取决于 Azure 中的数据特征、源位置和目标存储库。

**决策树**用于选择适合最后部分中出现的数据和对象的示例情景。

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

以下各部分都提供了一个示例情景。 每个情景，都列出了可能数据科学或高级分析流和支持 Azure 资源。

> [!NOTE]
> **对于所有以下情况，需要：**
> <br/>
> 
> * [创建存储帐户](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [创建 Azure 机器学习工作区](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>场景 \#1：本地文件中的小型到中型表格数据集
![小型到中型本地文件][1]

#### <a name="additional-azure-resources-none"></a>其他 Azure 资源：无
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
2. 上传数据集。
3. 开始使用上传的数据集生成 Azure 机器学习实验流。

## <a name="smalllocalprocess"></a>场景 \#2：需要处理的本地文件的小型到中型数据集
![需要处理的小型到中型本地文件][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机。
2. 将数据上传到 Azure 存储容器。
3. 访问 Azure 存储容器中的数据，预处理并清理 IPython Notebook 中的数据。
4. 将数据转换为已清除的表格格式。
5. 将已转换的数据保存在 Azure blob 中。
6. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
7. 使用[导入数据][import-data]模块从 Azure blob 中读取数据。
8. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="largelocal"></a>场景 \#3：本地文件针对 Azure Blob 的大型数据集
![大型本地文件][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机。
2. 将数据上传到 Azure 存储容器。
3. 访问 Azure blob 中的数据，预处理并清理 IPython Notebook 中的数据。
4. 如果需要，可将数据转换为已清除的表格格式。
5. 浏览数据，并根据需要创建功能。
6. 提取小到中型数据示例。
7. 将采样的数据保存在 Azure blob 中。
8. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
9. 使用[导入数据][import-data]模块从 Azure blob 中读取数据。
10. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="smalllocaltodb"></a>方案 \#4：本地文件中针对 Azure 虚拟机中 SQL Server 的小型到中型数据集
![Azure 中 SQL DB 的小型到中型本地文件][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server + IPython Notebook 的 Azure 虚拟机。
2. 将数据上传到 Azure 存储容器。
3. 使用 IPython Notebook 预处理并清理 Azure 存储容器中的数据。
4. 如果需要，可将数据转换为已清除的表格格式。
5. 将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
6. 将数据加载到在 Azure VM 上运行的 SQL Server 数据库。
   
   选项 \#1：使用 SQL Server Management Studio。
   
   * 登录到 SQL Server VM
   * 运行 SQL Server Management Studio。
   * 创建数据库和目标表。
   * 使用某个大容量导入方法从本地 VM 文件加载数据。
   
   选项 \#2：使用 IPython Notebook - 不建议将其用于中型和较大型数据集
   
   <!-- -->    
   * 使用 ODBC 连接字符串访问 VM 上的 SQL Server。
   * 创建数据库和目标表。
   * 使用某个大容量导入方法从本地 VM 文件加载数据。
7. 浏览数据，根据需要创建功能。 请注意，这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
8. 如果需要和/或想要，请确定数据示例大小。
9. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
10. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 粘贴需要的查询，根据需要，这会直接在[导入数据][import-data]查询中提取字段、创建功能和示例数据。
11. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="largelocaltodb"></a>场景 \#5：本地文件中针对 Azure VM 中 SQL Server 的大型数据集
![Azure 中 SQL DB 的大型本地文件][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server 和 IPython Notebook 服务器的 Azure 虚拟机。
2. 将数据上传到 Azure 存储容器。
3. （可选）预处理并清理数据。
   
   a.  预处理并清理 IPython Notebook 中的数据，从 Azure 访问数据
   
       blobs.
   
   b.  如果需要，可将数据转换为已清除的表格格式。
   
   c.  将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
4. 将数据加载到在 Azure VM 上运行的 SQL Server 数据库。
   
   a.  登录到 SQL Server VM。
   
   b.  如果数据尚未保存，请从 Azure 下载数据文件夹
   
       storage container to local-VM folder.
   
   c.  运行 SQL Server Management Studio。
   
   d.  创建数据库和目标表。
   
   e.  使用某个大容量导入方法加载数据。
   
   f.  如果需要表联接，可创建索引以加快联接。
   
   > [!NOTE]
   > 为了加快较大数据大小的加载速度，建议创建分区表，并大容量导入并行数据。 有关详细信息，请参阅[将并行数据导入到 SQL 分区表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
5. 浏览数据，根据需要创建功能。 请注意，这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
6. 如果需要和/或想要，请确定数据示例大小。
7. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
8. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 粘贴需要的查询，根据需要，这会直接在[导入数据][import-data]查询中提取字段、创建功能和示例数据。
9. 开始使用上传的数据集对 Azure 机器学习实验流进行采样

## <a name="largedbtodb"></a>场景 \#6：本地 SQL Server 数据库中针对 Azure 虚拟机中 SQL Server 的大型数据集
![Azure 中 SQL DB 的本地大型 SQL DB][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server 和 IPython Notebook 服务器的 Azure 虚拟机。
2. 使用一种数据导出方法将数据从 SQL Server 导出到转储文件。
   
   > [!NOTE]
   > 如果决定移动本地数据库中所有数据，另一个方法（更快）是将完整数据库移动到 Azure 中的 SQL Server 实例。 跳过导出数据、创建数据库和将数据加载/导入到目标数据库的步骤，使用另一个方法。
   > 
   > 
3. 将转储文件上传到 Azure 存储容器。
4. 将数据加载到在 Azure 虚拟机上运行的 SQL Server 数据库。
   
   a.  登录到 SQL Server VM。
   
   b.  从 Azure 存储容器将数据文件下载到本地 VM 文件夹。
   
   c.  运行 SQL Server Management Studio。
   
   d.  创建数据库和目标表。
   
   e.  使用某个大容量导入方法加载数据。
   
   f.  如果需要表联接，可创建索引以加快联接。
   
   > [!NOTE]
   > 为了加快较大数据大小的加载速度，可创建分区表，并大容量导入并行数据。 有关详细信息，请参阅[将并行数据导入到 SQL 分区表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
5. 浏览数据，根据需要创建功能。 请注意，这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
6. 如果需要和/或想要，请确定数据示例大小。
7. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
8. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 粘贴需要的查询，根据需要，这会直接在[导入数据][import-data]查询中提取字段、创建功能和示例数据。
9. 开始使用上传的数据集对 Azure 机器学习实验流进行采样。

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>另一个方法是将完整的数据库从本地 SQL Server 复制到 Azure SQL 数据库
![分离本地 DB，并附加到 Azure 中的 SQL DB][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
要复制 SQL Server VM 中的完整 SQL Server 数据库，假定数据库可暂时处于脱机状态，应将数据库从一个位置/服务器复制到另一个。 可在 SQL Server Management Studio 中执行此操作，或使用等效的 Transact-SQL 命令。

1. 在源位置分离数据库。 有关详细信息，请参阅[分离数据库](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)。
2. 在 Windows Explorer 或“Windows 命令提示”窗口中，将分离的一个或多个数据库文件和日志文件复制到 Azure 中 SQL Server VM 上的目标位置。
3. 将复制的文件附加到目标 SQL Server 实例。 有关详细信息，请参阅[附加数据库](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)。

[使用分离和附加移动数据库 (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>场景 \#7：本地文件中针对 Azure HDInsight Hadoop 群集中 Hive 数据库的大数据
![本地目标 Hive 中的大数据][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure HDInsight Hadoop 群集和Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机服务器。
2. 创建 Azure HDInsight Hadoop 群集。
3. （可选）预处理并清理数据。
   
   a.  预处理并清理 IPython Notebook 中的数据，从 Azure 访问数据
   
       blobs.
   
   b.  如果需要，可将数据转换为已清除的表格格式。
   
   c.  将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
4. 将数据上传到步骤 2 中选择的 Hadoop 群集的默认容器。
5. 将数据加载到 Azure HDInsight Hadoop 群集中的 Hive 数据库。
   
   a.  登录到 Hadoop 群集的头节点
   
   b.  打开 Hadoop 命令行。
   
   c.  按照 Hadoop 命令行中的命令 `cd %hive_home%\bin` 输入 Hive 根目录。
   
   d.  运行 Hive 查询以创建数据库和表，并将数据从 Blob 存储加载到 Hive 表。
   
   > [!NOTE]
   > 如果数据太大，用户可以使用分区创建 Hive 表。 然后，用户可以使用头节点上 Hadoop 命令行中的 `for` 循环，将数据加载到由分区进行分区的 Hive 表中。
   > 
   > 
6. 浏览数据，并根据需要在 Hadoop 命令行中创建功能。 请注意，这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
   
   a.  登录到 Hadoop 群集的头节点
   
   b.  打开 Hadoop 命令行。
   
   c.  按照 Hadoop 命令行中的命令 `cd %hive_home%\bin` 输入 Hive 根目录。
   
   d.  在 Hadoop 群集的头节点上的 Hadoop 命令行中运行 Hive 查询，可浏览数据并根据需要创建功能。
7. 如果需要和/或想要，对数据进行采样以满足 Azure 机器学习工作室。
8. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
9. 使用[导入数据][import-data]模块直接从 `Hive Queries` 中读取数据。 粘贴需要的查询，根据需要，这会直接在[导入数据][import-data]查询中提取字段、创建功能和示例数据。
10. 开始使用上传的数据集对 Azure 机器学习实验流进行采样。

## <a name="decisiontree"></a>用于方案选择的决策树
- - -
下面的图表总结了上述的方案以及让你采用每个详细方案的高级分析流程和技术选择。 请注意，数据处理、浏览、特征工程和采样可能会出现在一个或多个方法/环境中（在源、中间和/或目标环境中），并且可能会根据需要以迭代方式进行。 此图只用于列举某些可能流，并不提供详尽的枚举。

![示例 DS 流程演练方案][8]

### <a name="advanced-analytics-in-action-examples"></a>操作示例中的高级分析
关于利用使用公共数据集的高级分析流程和技术的端到端 Azure 机器学习演练，请参阅：

* [运行中的团队数据科学过程：使用 SQL Server](sql-walkthrough.md)。
* [运行中的团队数据科学过程：使用 HDInsight Hadoop 群集](hive-walkthrough.md)。

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
