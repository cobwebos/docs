---
title: 使用控制表从数据库进行大容量复制
description: 了解如何使用解决方案模板通过使用外部控制表，通过 Azure 数据工厂存储源表的分区列表，从数据库复制大容量数据。
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
ms.openlocfilehash: 3f50a6067eb38e920c32079c140785f397ee6698
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896255"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>使用控制表从数据库进行大容量复制

若要将数据从 Oracle 服务器、Netezza、Teradata 或 SQL Server 中的数据仓库复制到 Azure SQL 数据仓库，则必须从多个表中加载大量数据。 通常，必须在每个表中对数据进行分区，以便可以从单个表中并行加载具有多个线程的行。 本文介绍了要在这些方案中使用的模板。

 >!注意如果要将包含相对较小数据量的少量表中的数据复制到 SQL 数据仓库，则使用[Azure 数据工厂复制数据工具](copy-data-tool.md)会更有效。 本文中所述的模板超出了该方案所需的条件。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板检索要从外部控制表复制的源数据库分区列表。 然后，它将循环访问源数据库中的每个分区，并将数据复制到目标。

该模板包含三个活动：
- **Lookup**从外部控制表中检索确定数据库分区的列表。
- **ForEach**从查找活动获取分区列表，并将每个分区循环访问复制活动。
- **将**源数据库存储中的每个分区复制到目标存储区。

模板定义了五个参数：
- *Control_Table_Name*是外部控制表，用于存储源数据库的分区列表。
- *Control_Table_Schema_PartitionID*是存储每个分区 ID 的外部控制表中列名的名称。 请确保此分区 ID 对于源数据库中的每个分区都是唯一的。
- *Control_Table_Schema_SourceTableName*是存储来自源数据库的每个表名称的外部控制表。
- *Control_Table_Schema_FilterQuery*是外部控制表中的列的名称，该名称存储筛选器查询以获取源数据库中每个分区的数据。 例如，如果按年对数据进行分区，则每个行中存储的查询可能类似于 "select * from datasource，其中 LastModifytime > = ' ' 2015-01-01 00:00:00 '" 和 LastModifytime < = ' ' 2015-12-31 23：59： 59.999 ""。
- *Data_Destination_Folder_Path*是将数据复制到目标存储中的路径。 仅当选择的目标是基于文件的存储时，此参数才可见。 如果选择 "SQL 数据仓库" 作为目标存储区，则不需要此参数。 但是，SQL 数据仓库中的表名和架构必须与源数据库中的表名和架构相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 在 SQL Server 或 Azure SQL 数据库中创建一个控制表，以便存储用于大容量复制的源数据库分区列表。 在下面的示例中，源数据库中有五个分区。 三个分区用于*datasource_table*，两个分区用于*project_table*。 列*LastModifytime*用于将表中的数据分区为源数据库中的*datasource_table* 。 用于读取第一个分区的查询是 "select * from datasource_table，其中 LastModifytime > = ' ' 2015-01-01 00:00:00 '" 和 LastModifytime < = ' ' 2015-12-31 23：59： 59.999 "'"。 您可以使用类似的查询来读取其他分区中的数据。

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

2. 请从数据库模板中转到**大容量复制**。 创建与您在步骤1中创建的外部控制表的**新**连接。

    ![与控制表建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 创建与要从中复制数据的源数据库的**新**连接。

     ![与源数据库建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 创建与要向其中复制数据的目标数据存储区的**新**连接。

    ![与目标存储建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 选择“使用此模板”。

    ![使用此模板](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 你会看到管道，如以下示例中所示：

    ![查看管道](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 选择 "**调试**"，输入**参数**，然后选择 "**完成**"。

    ![单击 "调试"。](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 你将看到类似于以下示例的结果：

    ![查看结果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. 可有可无如果选择 "SQL 数据仓库" 作为数据目标，则必须根据 SQL 数据仓库 Polybase 的要求，为过渡输入连接到 Azure Blob 存储。 请确保已创建 Blob 存储中的容器。
    
    ![Polybase 设置](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
