## <a name="repeatability-during-copy"></a>在复制期间可重复性
当其他数据从数据复制到 Azure SQL/SQL Server 将存储一个需要记住可重复性以避免意外的结果。 

当将数据复制到 Azure SQL/SQL Server 数据库，复制活动将按默认追加到接收器表数据集默认情况下。 例如，在从包含到 Azure SQL/SQL Server 数据库的两条记录的 CSV （逗号分隔值数据） 文件源复制数据，这是表的外观：

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

假设你在源文件中发现错误，并且更新的从 2 到 4 的源文件中向下管的数量。 如果该时间段重新运行数据切片，你将找到追加到 Azure SQL/SQL Server 数据库的两条新记录。 下面假定没有任何表中的列具有主键约束。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

若要避免此问题，你将需要通过利用之一指定 UPSERT 语义下面 2 下面所述的机制。

> [!NOTE]
> 切片可以重新运行自动在 Azure 数据工厂中根据指定的重试策略。
> 
> 

### <a name="mechanism-1"></a>机制 1
你可以利用**sqlWriterCleanupScript**属性在运行切片时首先执行清理操作。 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

清理脚本将执行第一个期间给定切片，这将删除与该切片对应的 SQL 表中的数据的副本。 活动随后将插入 SQL 表的数据。 

如果切片是现在重新运行，则你将找到的数量更新为所需。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

假设从原始 csv 删除垫圈记录。 然后重新运行切片后会生成以下结果： 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
什么新鲜事物不得不进行。 复制活动运行清理脚本，才能删除该切片的相应数据。 然后它从 （然后包含只有 1 记录） 的 csv 读取输入并将其插入到表。 

### <a name="mechanism-2"></a>机制 2
> [!IMPORTANT]
> 使用 sliceIdentifierColumnName 不支持为 Azure SQL 数据仓库在这一次。 

若要实现可重复性的另一种机制是使用专用的列 (**sliceIdentifierColumnName**) 目标表中。 此列将由 Azure 数据工厂，用于确保源和目标保持同步。 灵活地更改或定义目标 SQL 表架构时，此方法适用。 

此列将用于通过 Azure 数据工厂可重复性目的，在过程中 Azure 数据工厂不会进行任何架构更改到表。 若要使用此方法的方式：

1. 在目标 SQL 表中定义的二进制类型 (32) 的列。 不应在此列上的任何约束。 让我们对于此示例名称作为 ColumnForADFuseOnly 的此列。
2. 它在中使用复制活动，如下所示：
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure 数据工厂将填充此列根据其需要确保源和目标保持同步。 此列的值不应在此上下文外部用户使用。 

类似于机制 1，复制活动将自动首先清除目标 SQL 表中给定的切片的数据，然后运行通常要将数据从源插入到该切片的目标的复制活动。 

