---
title: 使用 Azure 数据工厂通过控制表从数据库进行增量复制 | Microsoft Docs
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966368"
---
# <a name="delta-copy-from-database-with-control-table"></a>使用控件表从数据库进行增量复制

希望使用存储高水印值的外部控件表将更改（新行或更新后的行）仅从数据库中的表增量加载到 Azure 时。  本模板专为该情况而设计。 

此模板要求源数据库的架构必须包含时间戳列或递增键，以标识新行或更新后的行。

如果源数据库中确实有 timestamp 列可以标识新行或更新后的行，但不希望创建外部控件表来启用增量复制，则可以使用“复制数据”工具获取管道。该管道使用触发器计划的时间作为变量，仅从源数据库读取新行。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板总是先检索旧的水印值，然后再将其与当前水印值进行比较。 之后，此模板根据 2 个水印值之间的比较仅从源数据库复制所做的更改。  完成后，此模板会将新的高水印值存储到外部控件表中，以便下次加载增量数据。

此模板包含四个活动：
-   一个“查找”活动，用于检索存储在外部控件表中的旧的高水印值。
-   另一个“查找”活动，用于从源数据库中检索当前的高水印值。
-   一个“复制”活动，用于仅将源数据库中的更改复制到目标存储。 用于在复制活动中标识源数据库的更改的查询类似于 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” 和 TIMESTAMP_Column <= “current high-watermark”'。
-   一个“SqlServerStoredProcedure”活动，用于将当前高水印值写入外部控件表，以便下次进行增量复制。

模板定义了五个参数：
-   参数 Data_Source_Table_Name，是要从中加载数据的源数据库的表名。
-   参数 Data_Source_WaterMarkColumn，是可用于标识新行或更新后的行的源表中的列名。 通常，此列的类型可以是日期/时间或 INT 等。
-   参数 Data_Destination_Folder_Path 或 Data_Destination_Table_Name，是将数据复制到目标存储的位置。
-   参数 Control_Table_Table_Name，是用于存储高水印值的外部控件表的名称。
-   参数 Control_Table_Column_Name，是外部控件表中用于存储高水印值的列名。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 浏览要加载的源表，并定义可用于切片新行或更新后的行的高水印列。 通常，此列的类型可以是日期/时间或 INT 等，并且在添加新行时保持增加其数据。  从下面的示例源表（表名：data_source_table），可以使用列 LastModifytime 作为高水印列。

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
    
2. 在 SQL Server 或 SQL Azure 中创建控件表，用于存储增量数据加载的高水印值。 从以下示例中，可以看到控件表的名称为“watermarktable”。 其中，用于存储高水印值的列名称为“WatermarkValue”，并且其类型为日期/时间。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 在用于创建控件表的同一 SQL server 或 SQL Azure 中创建存储过程。 存储过程用于将新的高水印值写入外部控件表，以便下次加载增量数据。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 转到模板“从数据库增量复制”，并创建与从中复制数据的源数据库的“新连接”。

    ![创建与源表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 创建与将数据复制到的目标数据存储的“新连接”。

    ![创建与目标表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 创建与外部控件表和存储过程的“新连接”。  此连接将连接到在步骤 #2 和 #3 中创建了控件表和存储过程的数据库。

    ![创建与控件表数据存储的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 单击“使用此模板”。

     ![使用此模板](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. 可以在面板中看到可用的管道，如以下示例所示：

     ![查看管道](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 单击存储过程活动，选择“存储过程名称”，单击“导入参数”，然后单击“添加动态内容”。  

     ![设置存储过程活动](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. 写入内容“@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}”，然后单击“完成”。  

     ![为存储过程写入参数的内容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. 单击“调试”，输入参数，然后单击“完成”

    ![单击“调试”](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 可以在面板中看到可用的结果，如以下示例所示：

    ![查看结果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 可以在源表中创建新行。  用于创建新行的示例 sql 如下所示：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. 单击“调试”再次运行管道，输入参数，然后单击“完成”。

    ![单击“调试”](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. 你会看到仅新行已复制到目标中。

15. （可选）如果选择 SQL 数据仓库作为数据目标，则还需要输入 Azure Blob 存储的连接作为暂存，这是 SQL 数据仓库 Polybase 所必需的。  请确保已在 blob 存储中创建容器。  
    
    ![配置 PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)