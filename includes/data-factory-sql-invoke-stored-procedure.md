## <a name="invoking-stored-procedure-for-sql-sink"></a>调用 SQL 接收器的存储过程
将数据复制到 SQL Server 或 Azure SQL/SQL Server 数据库时，可以配置指定的存储过程并配合其他参数来调用。 

内置的复制机制无法使用时，可使用存储过程。 需要在最后一次将源数据插入目标表前完成额外处理（合并列、查找其他值、插入到多个表等）时，通常使用此过程。 

可以调用所选的存储过程。 以下示例演示如何使用存储过程，方便地在数据库中的表内插入数据。 

**输出数据集**

在本示例中，type 设置为：SqlServerTable。 请将它设置为 AzureSqlTable 以配合 Azure SQL 数据库使用。 

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

如下所示，在复制活动 JSON 中定义 SqlSink 节。 若要在插入数据时调用存储过程，需要 SqlWriterStoredProcedureName 和 SqlWriterTableType 属性。

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

在数据库中，使用与 SqlWriterStoredProcedureName 相同的名称定义存储过程。 它处理来自指定源的输入数据，并插入到输出表。 请注意，存储过程的参数名称应与表 JSON 文件中定义的 tableName 相同。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```
在数据库中，使用与 SqlWriterTableType 相同的名称定义表类型。 请注意，表类型的架构应与输入数据返回的架构相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```
存储过程功能利用[表值参数](https://msdn.microsoft.com/library/bb675163.aspx)。



<!--HONumber=Nov16_HO3-->


