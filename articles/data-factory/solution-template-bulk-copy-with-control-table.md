---
title: 使用 Azure 数据工厂通过控制表从数据库进行大容量复制 | Microsoft Docs
description: 了解如何使用解决方案模板通过外部控制表从数据库完整地大容量复制数据，以使用 Azure 数据工厂存储源表的分区列表。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966473"
---
# <a name="bulk-copy-from-database-with-control-table"></a>使用控制表从数据库进行大容量复制

如果要将数据从 Oracle 服务器、Netezza 服务器、Teradata 服务器或 SQL Server 等数据仓库复制到 Azure，则必须从数据源中的多个表加载大量数据。 在大多数情况下，必须在每个表中对数据进一步分区，以便使用多个线程从单个表并行加载行。 本模板专为该案例而设计。 

如果要从少量具有较小数据的表中复制数据，更有效的方式是转到“复制数据工具”，在管道中进行单个复制活动或 foreach 活动 + 复制活动。 本模板适用于比这种简单用例更复杂的用例。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从需要复制到目标存储的外部控制表中检索源数据库的分区列表，然后循环访问源数据库中的每个分区并执行数据复制操作。

此模板包含三个活动：
-   **查找**活动，用于从外部控制表中检索源数据库的分区列表。
-   **ForEach** 活动，用于从查找活动中获取分区列表，然后针对复制活动循环访问每个分区。
-   **复制**活动，用于将每个分区从源数据库存储复制到目标存储。

此模板定义了五个参数：
-   参数 *Control_Table_Name* 是外部控制表的表名。 控制表用于存储源数据库的分区列表。
-   参数 *Control_Table_Schema_PartitionID* 是外部控制表中用于存储每个分区 ID 的列名。 请确保源数据库中每个分区的分区 ID 都是唯一的。
-   参数 *Control_Table_Schema_SourceTableName* 是外部控制表中的列名，用于存储源数据库中的每个表名。
-   参数 *Control_Table_Schema_FilterQuery* 是外部控制表中的列名，用于存储筛选器查询以从源数据库中的每个分区获取数据。 例如，如果按年度对数据进行分区，则存储在每一行中的查询可能类似于“select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''”
-   参数 *Data_Destination_Folder_Path* 是将数据复制到目标存储的文件夹路径。  仅当所选目标是基于文件的存储时，此参数才可见。  如果选择 SQL 数据仓库作为目标存储，则不需要在此处输入任何参数。 但 SQL 数据仓库中的表名和模式必须与源数据库中的表名和模式相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 在 SQL Server 或 SQL Azure 中创建控制表，以存储源数据库的分区列表来进行大容量复制。  从下面的示例中，可以看到源数据库中有五个分区，其中三个分区用于一个表：*datasource_table*，两个分区用于另一个表：*project_table*。 列 *LastModifytime* 用于对源数据库中表 *datasource_table* 中的数据进行分区。 用于读取第一个分区的查询是“select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''”。  从其他分区读取数据时，还会看到类似的查询。 

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

2. 转到模板“从数据库进行大容量复制”，与外部控制表建立**新的连接**。  此连接将连接到步骤 1 中创建了控制表的数据库。

    ![与控制表建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 与从中复制数据的源数据库建立**新的连接**。

     ![与源数据库建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 与将数据复制到的目标数据存储建立**新的连接**。

    ![与目标存储建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 单击“使用此模板”。

    ![使用此模板](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 可以在面板中看到可用的管道，如以下示例所示：

    ![查看管道](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 单击“调试”，输入参数，然后单击“完成”

    ![单击“调试”](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 可以在面板中看到可用的结果，如以下示例所示：

    ![查看结果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. （可选）如果选择 SQL 数据仓库作为数据目标，则还需要输入 Azure Blob 存储的连接作为暂存，这是 SQL 数据仓库 Polybase 所必需的。  确保已在 Blob 存储中创建容器。  
    
    ![Polybase 设置](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)