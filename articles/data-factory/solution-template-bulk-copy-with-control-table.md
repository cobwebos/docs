---
title: 从大容量复制数据库与 Azure 数据工厂中使用控制表 |Microsoft Docs
description: 了解如何使用解决方案模板将大容量数据从数据库复制使用外部控件表来存储使用 Azure 数据工厂源数据表的分区列表。
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
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530563"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>从具有控制表的数据库大容量复制

若要将数据从 Oracle Server、 Netezza、 Teradata 或 SQL Server 中的数据仓库复制到 Azure SQL 数据仓库，必须从多个表加载大量数据。 通常情况下，数据必须分区中每个表，以便可以从单个表加载多个线程并行的行。 本指南介绍了在这些方案中使用的模板。

 >!请注意是否你想要将数据从少量的具有相对较小数据量的表复制到 SQL 数据仓库，它是使用更加高效[Azure 数据工厂复制数据工具](copy-data-tool.md)。 本文中所述的模板是多个所需的这种情况。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板中检索源数据库分区，以从外部控件表中复制的列表。 然后，它循环访问源数据库中每个分区，并将数据复制到目标。

该模板包含三个活动：
- **查找**从外部控件表中检索确保数据库分区的列表。
- **ForEach**获取分区列表中查找活动，并在复制活动的每个分区。
- **复制**源数据库存储的每个分区复制到目标存储区。

模板定义了五个参数：
- *Control_Table_Name*是在外部控件表，该表可存储源数据库的分区列表。
- *Control_Table_Schema_PartitionID*中存储每个分区 id。 在外部控制表的列名称的名称 请确保分区 ID 是唯一的源数据库中每个分区。
- *Control_Table_Schema_SourceTableName*是外部控件表存储源数据库中的每个表名。
- *Control_Table_Schema_FilterQuery*是将筛选器查询来获取源数据库中每个分区中的数据存储在外部控制表中的列的名称。 例如，如果按年份分区数据，每行中存储的查询可能类似于选择 * 从数据源位置 LastModifytime > = '2015年-01-01 00:00:00 和 LastModifytime < =' 2015年-12-31 23:59:59.999 '。
- *Data_Destination_Folder_Path*是将数据复制到目标存储区的位置的路径。 此参数时才会显示您选择的目标是基于文件的存储。 如果您选择 SQL 数据仓库用作目标存储，此参数不是必需的。 但表名称和 SQL 数据仓库中的架构必须与源数据库中的相同。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 在 SQL Server 或 Azure SQL 数据库来存储大容量复制的源数据库分区列表中创建控制表。 在以下示例中，源数据库中有五个分区。 适用于三个分区*datasource_table*，另两个用于*project_table*。 该列*LastModifytime*使用表中的数据进行分区*datasource_table*源数据库中。 用于读取的第一个分区的查询是选择 * 从 datasource_table 其中 LastModifytime > = '2015年-01-01 00:00:00 和 LastModifytime < =' 2015年-12-31 23:59:59.999 '。 可以使用一个类似的查询从其他分区读取数据。

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

2. 转到**从数据库的大容量复制**模板。 创建**新建**对步骤 1 中创建的外部控制表的连接。

    ![与控制表建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 创建**新建**到从中复制数据的源数据库的连接。

     ![与源数据库建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 创建**新建**连接到目标数据存储，要将复制到的数据。

    ![与目标存储建立新的连接](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 选择**使用此模板**。

    ![使用此模板](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 您看到管道中，如下面的示例中所示：

    ![查看管道](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 选择**调试**，输入**参数**，然后选择**完成**。

    ![单击 * * 调试 * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 将看到类似于下面的示例的结果：

    ![查看结果](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. （可选）如果你选择作为数据目标的 SQL 数据仓库，必须为所需的 SQL 数据仓库 Polybase 过渡到 Azure Blob 存储输入连接。 请确保已创建 Blob 存储中的容器。
    
    ![Polybase 设置](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
