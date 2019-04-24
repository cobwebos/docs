---
title: 数据库中的 Azure 数据工厂中使用控制表的增量复制 |Microsoft Docs
description: 了解如何使用解决方案模板通过 Azure 数据工厂仅从数据库中逐步复制新行或更新后的行。
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
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312379"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>从具有控制表的数据库的增量复制

本文介绍可用于以增量方式加载新的或更新行从数据库表到 Azure 使用外部控制表，用于存储高水印值的模板。

此模板要求源数据库的架构包含时间戳列或递增键来标识新的或更新行。

>[!NOTE]
> 如果源数据库来标识新的或更新行中有时间戳列，但不想创建一个外部控件表以用于增量复制，则可以使用[Azure 数据工厂复制数据工具](copy-data-tool.md)获取管道。 该工具使用触发器按计划时间作为变量从源数据库读取新行。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板第一次检索旧的水印值，并将其与当前的水印值进行比较。 之后，它只更改将从复制源数据库，基于两个水印值之间的比较。 最后，它将存储到外部控制表的增量数据加载下一次新的高水印值。

此模板包含四个活动：
- **查找**检索旧的高水印值存储在外部控件表。
- 另一个**查找**活动将源数据库中检索当前的高水印值。
- **复制**将仅更改从源数据库复制到目标存储区。 标识源数据库中的更改的查询是类似于选择 * 从 Data_Source_Table 其中 TIMESTAMP_Column >"最后一个高水位"和 TIMESTAMP_Column < ="当前高水印"。
- **SqlServerStoredProcedure**当前的高水印值写入外部控件表用于增量复制下一次。

模板定义了五个参数：
- *Data_Source_Table_Name*是你想要将数据从加载的源数据库中的表。
- *Data_Source_WaterMarkColumn*是用来标识新或更新的行的源表中列的名称。 此列的类型通常是*datetime*， *INT*，或类似。
- *Data_Destination_Folder_Path*或*Data_Destination_Table_Name*是其中将数据复制到目标存储区中的位置。
- *Control_Table_Table_Name*是外部控制表，用于存储高水印值。
- *Control_Table_Column_Name*是用于存储高水印值的外部控件表中的列。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 浏览源表要加载，并定义可用于标识新的或更新行的高水印列。 此列的类型可能*datetime*， *INT*，或类似。 添加新行时，会增加此列的值。 在以下示例源表 (data_source_table)，我们可以使用*LastModifytime*列作为高水印列。

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. 在 SQL Server 或 Azure SQL 数据库用于存储高水印值的增量数据加载中创建控制表。 以下示例中，在控制表的名称是*watermarktable*。 在此表中， *WatermarkValue*为存储高水印值的列且其类型为*datetime*。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 用于创建控制表的相同 SQL Server 或 Azure SQL 数据库实例中创建的存储的过程。 存储的过程用于加载下一次的增量数据的外部控件表中写入新的高水印值。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 转到**从数据库的增量复制**模板。 创建**新建**到您想要从数据复制的源数据库的连接。

    ![创建与源表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 创建**新建**连接到你想要向其中复制数据的目标数据存储区。

    ![创建与目标表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 创建**新建**连接到外部控制表和步骤 2 和 3 中创建的存储的过程。

    ![创建与控件表数据存储的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 选择**使用此模板**。

     ![使用此模板](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 你将看到可用的管道，如下面的示例中所示：

     ![查看管道](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 选择**存储过程**。 有关**存储过程名称**，选择 **[update_watermark]**。 选择**导入参数**，然后选择**添加动态内容**。  

     ![设置存储的过程活动](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 编写的内容 **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**，然后选择**完成**。  

     ![编写存储过程的参数的内容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. 选择**调试**，输入**参数**，然后选择**完成**。

    ![选择 * * 调试 * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 显示输出结果类似于下面的示例：

    ![查看结果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 可以在源表中创建新行。 下面是示例 SQL 语言创建新行：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. 若要再次运行管道，请选择**调试**，输入**参数**，然后选择**完成**。

    ![选择 * * 调试 * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    您看到唯一的新行复制到目标。

15. （可选：）如果您选择 SQL 数据仓库作为数据目标，您还必须为所需的 SQL 数据仓库 Polybase 的暂存提供连接到 Azure Blob 存储。 请确保该容器已创建 Blob 存储中。
    
    ![配置 PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>后续步骤

- [数据库中的 Azure 数据工厂中使用控制表的大容量复制](solution-template-bulk-copy-with-control-table.md)
- [将多个容器使用 Azure 数据工厂中的文件](solution-template-copy-files-multiple-containers.md)
