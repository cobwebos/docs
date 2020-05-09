---
title: 使用控制表从数据库进行大容量复制
description: 了解如何使用解决方案模板通过外部控制表从数据库大容量复制数据，以使用 Azure 数据工厂存储源表的分区列表。
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 46e81242c1fba463f547015a244650ae6e574580
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629076"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>使用控制表从数据库进行大容量复制

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

若要将数据从 Oracle 服务器、Netezza、Teradata 或 SQL Server 中的数据仓库复制到 Azure SQL 数据仓库，必须从多个表加载大量数据。 通常情况下，必须在每个表中对数据分区，以便使用多个线程从单个表并行加载行。 本文介绍一个可以在这些情况下使用的模板。

 >!NOTE 如果要从数据量相对较小的少数几个表中将数据复制到 SQL 数据仓库，则使用 [Azure 数据工厂复制数据工具](copy-data-tool.md)会更高效。 本文描述的模板超出你对该场景的需求。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板可从外部控制表检索用于复制的源数据库分区的列表。 然后，它会循环访问源数据库中的每个分区，并将数据复制到目标。

该模板包含三个活动：
- **查找**可从外部控制表检索源数据库分区的列表。
- **ForEach** 可从查找活动中获取分区列表，然后针对复制活动循环访问每个分区。
- **复制**可将每个分区从源数据库存储复制到目标存储。

模板定义以下参数：
- *Control_Table_Name* 是外部控制表，用于存储源数据库的分区列表。
- *Control_Table_Schema_PartitionID* 是外部控制表中用于存储每个分区 ID 的列名。 请确保源数据库中每个分区的分区 ID 都是独一无二的。
- *Control_Table_Schema_SourceTableName* 是外部控制表，用于存储源数据库中的每个表名。
- *Control_Table_Schema_FilterQuery* 是外部控制表中的列名，用于存储筛选器查询以从源数据库中的每个分区获取数据。 例如，如果按年份对数据进行分区，则存储在每一行中的查询可能类似于“select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''”。
- *Data_Destination_Folder_Path*是将数据复制到目标存储中的路径（适用于所选目标是 "文件系统" 或 "Azure Data Lake Storage Gen1"）。 
- *Data_Destination_Container* 是将数据复制到目标存储时使用的根文件夹路径。 
- *Data_Destination_Directory* 是将数据复制到目标存储中时使用的根下的目录路径。 

仅当所选目标是基于文件的存储时，最后三个参数（定义目标存储中的路径）才可见。 如果选择“Azure Synapse Analytics（以前称为 SQL DW）”作为目标存储，则不需要这些参数。 但 SQL 数据仓库中的表名和模式必须与源数据库中的表名和模式相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 在 SQL Server 或 Azure SQL 数据库中创建控制表，以存储源数据库分区列表来进行大容量复制。 在以下示例中，源数据库有五个分区。 三个分区用于 *datasource_table*，两个分区用于 *project_table*。 列 *LastModifytime* 用于对源数据库中表 *datasource_table* 中的数据进行分区。 用于读取第一个分区的查询是“select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''”。 可以使用类似查询从其他分区读取数据。

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. 转到“从数据库大容量复制”模板。  创建与在步骤 1 中创建的外部控制表的**新**连接。

    ![与控制表建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 创建与源数据库（要从其复制数据）的**新**连接。

    ![与源数据库建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 创建与目标数据存储（要将数据复制到其中）的**新**连接。

    ![与目标存储建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 选择“使用此模板”  。

6. 可以看到管道，如以下示例所示：

    ![查看管道](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 选择“调试”，输入**参数**，然后选择“完成”。  

    ![单击“调试”****](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 看到的结果类似于以下示例：

    ![查看结果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. （可选）如果选择“Azure Synapse Analytics（以前为 SQL DW）”作为数据目标，需要按 SQL 数据仓库 Polybase 的要求，输入用于暂存的 Azure Blob 存储的连接。 模板会自动为 Blob 存储生成容器路径。 检查是否在管道运行后创建了容器。
    
    ![Polybase 设置](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
