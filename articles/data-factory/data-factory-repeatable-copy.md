---
title: "Azure 数据工厂中的可重复复制 | Microsoft Docs"
description: "了解如何避免重复项（即使多次运行复制数据的切片）。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: d87946b864eeb506f76dc7160d5795b9c4c4ac7d
ms.lasthandoff: 03/30/2017


---

# <a name="repeatable-copy-in-azure-data-factory"></a>Azure 数据工厂中的可重复复制

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。  
 
> [!NOTE]
> 以下示例适用于 Azure SQL，但同样适用于任何支持矩形数据集的数据存储。 可能需要针对数据存储调整源的**类型**和 **query** 属性（例如，使用 query 而不是 sqlReaderQuery）。   

从关系存储读取数据时，我们通常只希望读取对应于该切片的数据。 使用 Azure 数据工厂中提供的 WindowStart 和 WindowEnd 系统变量可实现此目的。 有关 Azure 数据工厂中的变量和函数，请阅读 [Azure 数据工厂 - 函数和系统变量](data-factory-functions-variables.md)一文。 示例： 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
此查询从表 MyTable 中读取的切片持续时间范围 (WindowStart -> WindowEnd) 中的数据。 重新运行此切片的也始终将确保读取相同的数据。 

在其他情况下，你可能想要读取整个表，并按如下所示定义 sqlReaderQuery：

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>可重复写入 SqlSink
从其他数据存储中的数据复制到 **Azure SQL/SQL Server** 时，需要记住可重复性，以免发生意外的结果。 

将数据复制到 Azure SQL/SQL Server 数据库时，默认情况下复制活动将数据追加到接收器表后面。 假设你要将数据复制从包含到 Azure SQL/SQL Server 数据库中的以下表的两条记录的 CSV（逗号分隔值）文件。 切片运行时，两条记录被复制到的 SQL 表。 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

假设在源文件中发现错误，然后将 Down Tube 数量从 2 更新为 4。 如果手动重新运行该时间段的数据切片，会发现有两条新记录已追加到 Azure SQL/SQL Server 数据库。 此示例假设表中的列都没有主键约束。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

若要避免此行为，需要使用以下两个机制之一指定 UPSERT 语义：

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>机制 1：使用 sqlWriterCleanupScript
可以使用 **sqlWriterCleanupScript** 属性以进行切片运行时插入数据之前清除接收器表中的数据。 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

如果切片运行时，会首先运行清理脚本以删除对应于 SQL 表切片的数据。 然后，复制活动将数据插入到 SQL 表。 如果重新运行切片，更新的数量根据需要。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

假设 Flat Washer 已从原始 csv 中删除。 重新运行切片会生成以下结果： 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

复制活动已运行清除脚本来删除该切片的相应数据。 然后，它从 csv（只包含一条记录）中读取了输入并将其插入表中。 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>机制 2：使用 sliceIdentifierColumnName
> [!IMPORTANT]
> Azure SQL 数据仓库目前不支持 sliceIdentifierColumnName。 

实现可重复性的第二种机制是在目标表中使用专用列 (sliceIdentifierColumnName)。 Azure 数据工厂使用此列来确保源与目标保持同步。 如果可以灵活更改或定义目标 SQL 表架构，则很适合使用这种方法。 

出于可重复性的目的，Azure 数据工厂将使用此列，在此过程中，Azure 数据工厂不会对表做出任何架构更改。 如何使用此方法：

1. 在目标 SQL 表中定义**二进制 (32)** 类型的列。 此列不应有任何约束。 在本示例中，我们将此列命名为 AdfSliceIdentifier。


    源表：

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    目标表： 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. 如下所示，在复制活动中使用该列：
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure 数据工厂会根据此列的需求填充数据，确保源与目标保持同步。 不应在此上下文以外使用此列的值。 

类似于机制 1，复制活动会自动清理目标 SQL 表中给定切片的数据。 然后，将源中的数据插入目标表。 

## <a name="next-steps"></a>后续步骤
有关完整的 JSON 示例，请查看以下连接器文章： 

- [Azure SQL 数据库](data-factory-azure-sql-connector.md)
- [Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
