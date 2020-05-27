---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173417"
---
## <a name="repeatability-during-copy"></a>复制期间的可重复性
从其他数据存储中的数据复制到 Azure SQL/SQL Server 时，需要记住可重复性，以免发生意外的结果。 

将数据复制到 Azure SQL/SQL Server 数据库时，复制活动默认会将数据集追加到接收器表。 例如，将数据从包含两条记录的 CSV（逗号分隔值）文件源复制到 Azure SQL/SQL Server 数据库时，表的外观如下所示：

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

假设在源文件中发现错误，然后将源文件中的 Down Tube 数量从 2 更新为 4。 如果重新运行该时间段的数据切片，会发现有两条新记录已追加到 Azure SQL/SQL Server 数据库。 下面假设表中的列都没有主键约束。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

若要避免此问题，必须利用下述 2 种机制之一指定 UPSERT 语义。

> [!NOTE]
> 可以根据指定的重试策略在 Azure 数据工厂中自动重新运行切片。
> 
> 

### <a name="mechanism-1"></a>机制 1
可以利用 **sqlWriterCleanupScript** 属性在运行切片时先执行清理操作。 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

在复制给定的切片期间会先执行清除脚本，这会删除 SQL 表中对应于该切片的数据。 然后，活动会将数据插入 SQL 表。 

如果此时重新运行切片，则会发现数量已根据需要更新。

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
不需要执行其他操作。 复制活动已运行清除脚本来删除该切片的相应数据。 然后，它从 csv（只包含 1 条记录）中读取了输入并将其插入表中。 

### <a name="mechanism-2"></a>机制 2
> [!IMPORTANT]
> Azure SQL 数据仓库目前不支持 sliceIdentifierColumnName。 

实现可重复性的另一种机制是在目标表中使用专用列 (**sliceIdentifierColumnName**)。 Azure 数据工厂使用此列来确保源与目标保持同步。 如果可以灵活更改或定义目标 SQL 表架构，则很适合使用这种方法。 

出于可重复性的目的，Azure 数据工厂将使用此列，在此过程中，Azure 数据工厂不会对表做出任何架构更改。 如何使用此方法：

1. 在目标 SQL 表中定义二进制 (32) 类型的列。 此列不应有任何约束。 在本示例中，我们将此列命名为“ColumnForADFuseOnly”。
2. 如下所示，在复制活动中使用该列：
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure 数据工厂会根据此列的需求填充数据，确保源与目标保持同步。 用户不应在此上下文以外使用此列的值。 

类似于机制 1，复制活动首先会自动从目标 SQL 表中清除给定切片的数据，然后正常运行复制活动，将数据从源插入该切片的目标。 

