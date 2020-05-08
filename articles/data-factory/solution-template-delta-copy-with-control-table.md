---
title: 使用控制表从数据库执行增量复制
description: 了解如何使用解决方案模板通过 Azure 数据工厂仅从数据库中逐步复制新行或更新后的行。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 4da54318bea21daf9ec363be61bea18adaa2ce63
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629025"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>使用控制表从数据库执行增量复制

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍一个模板。借助该模板，可以使用一个存储高水印值的外部控制表，以增量方式将数据库表中的新行或已更新的行加载到 Azure。

此模板要求源数据库的架构包含时间戳列或递增键，以标识新行或更新后的行。

>[!NOTE]
> 如果你使用源数据库中的时间戳列标识新行或已更新的行，但不想要创建一个外部控制表用于增量复制，可以改用 [Azure 数据工厂复制数据工具](copy-data-tool.md)来获取管道。 该工具使用触发器计划的时间作为变量来从源数据库读取新行。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板先检索旧水印值，并将其与当前水印值进行比较。 然后，此模板根据两个水印值之间的比较仅复制源数据库中的更改。 最后，此模板会将新的高水印值存储到外部控制表中，以便下次加载增量数据。

此模板包含四个活动：
- 一个 **Lookup** 检索存储在外部控制表中的旧高水印值。
- 另一个 **Lookup** 活动从源数据库中检索当前的高水印值。
- **Copy** 仅将源数据库中的更改复制到目标存储。 用于在源数据库中识别更改的查询类似于 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” 和 TIMESTAMP_Column <= “current high-watermark”'。
- **SqlServerStoredProcedure** 活动将当前高水印值写入外部控制表，以便下次进行增量复制。

模板定义以下参数：
- *Data_Source_Table_Name* 是源数据库中的要从中加载数据的表。
- *Data_Source_WaterMarkColumn* 是源表中的用于识别新行或已更新行的列名。 此列的类型通常为 *datetime*、*INT* 等。
- *Data_Destination_Container* 是在目标存储中将数据复制到的位置的根路径。
- *Data_Destination_Directory* 是在目标存储中将数据复制到的位置的根下的目录路径。
- *Data_Destination_Table_Name* 是目标存储中要将数据复制到的位置（在选择“Azure Synapse Analytics (以前称为 SQL DW)”作为“数据目标”时适用）。
- *Data_Destination_Folder_Path*是将数据复制到目标存储中的位置（如果选择 "文件系统" 或 "Azure Data Lake Storage Gen1" 作为数据目标，则适用）。
- *Control_Table_Table_Name* 是用于存储高水印值的外部控制表。
- *Control_Table_Table_Name* 是外部控制表中要将高水印值存储到的列。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 浏览要加载的源表，并定义可用于识别新行或已更新行的高水印列。 此列的类型可能为 *datetime*、*INT* 等。 添加新行时，此列的值会增大。 在以下示例源表 (data_source_table) 中，我们可以使用 *LastModifytime* 列作为高水印列。

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
    
2. 在 SQL Server 或 Azure SQL 数据库中创建一个控制表，用于存储增量数据加载的高水印值。 在以下示例中，控制表的名称为 *watermarktable*。 在此表中，*WatermarkValue* 是用于存储高水印值的列，其类型为 *datetime*。

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 在用于创建控制表的同一 SQL Server 或 Azure SQL 数据库实例中创建一个存储过程。 该存储过程用于将新的高水印值写入外部控制表，以便下次进行增量数据加载。

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. 转到“从数据库执行增量复制”模板。  与要从中复制数据的源数据库建立**新的**连接。

    ![创建与源表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 与要将数据复制到的目标数据存储建立**新的**连接。

    ![创建与目标表的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 与在步骤 2 和 3 中创建的外部控制表与存储过程建立**新的**连接。

    ![创建与控件表数据存储的新连接](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. 选择“使用此模板”  。
    
8. 你将看到可用的管道，如以下示例所示：
  
    ![查看管道](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. 选择“存储过程”。  对于“存储过程名称”，请选择“[dbo].[update_watermark]”。   依次选择“导入参数”、“添加动态内容”。    

    ![设置存储过程活动](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. 写入内容 **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** ，然后选择“完成”。   

    ![写入存储过程参数的内容](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. 选择“调试”，输入**参数**，然后选择“完成”。  

    ![选择“调试”](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 随后会显示类似于以下示例的结果：

    ![查看结果](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 可以在源表中创建新行。 下面是用于创建新行的示例 SQL 语言：

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 若要再次运行管道，请选择“调试”，输入**参数**，然后选择“完成”。  

    你会看到，只有新行已复制到目标。

15. （可选：）如果选择“Azure Synapse Analytics（以前为 SQL DW）”作为数据目标，则还需按 SQL 数据仓库 Polybase 的要求，提供用于暂存的 Azure Blob 存储的连接。 模板会为你生成容器路径。 管道运行后，检查是否已在 Blob 存储中创建容器。
    
    ![配置 PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>后续步骤

- [在 Azure 数据工厂中使用控制表从数据库执行批量复制](solution-template-bulk-copy-with-control-table.md)
- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)
