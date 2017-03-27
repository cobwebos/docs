### <a name="type-conversion-sample"></a>类型转换示例
以下示例适用于使用类型转换将数据从 Blob 复制到 Azure SQL。

假设 Blob 数据集采用 CSV 格式且包含 3 列。 其中一列是采用自定义日期时间格式（星期日期的法语名称已缩写）的日期时间列。

我们将按如下所示定义 Blob 源数据集并指定列的类型定义。

```json
{
    "name": "AzureBlobTypeSystemInput",
    "properties":
    {
         "structure": 
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
对于上述从 SQL 类型到 .NET 类型的类型映射表，可以使用以下架构来定义 Azure SQL 表。

| 列名 | SQL 类型 |
| --- | --- |
| userid |bigint |
| 名称 |text |
| lastlogindate |datetime |

接下来，按如下所示定义 Azure SQL 数据集。 

> [!NOTE]
> 不需要指定包含类型信息的“结构”部分，因为类型信息已在基础数据存储中指定。

```json
{
    "name": "AzureSQLOutput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

在此情况下，将数据从 Blob 移到 Azure SQL 时，数据工厂将自动执行类型转换，包括对使用自定义日期时间格式（使用 fr-fr 区域性）的 Datetime 字段的类型转换。



<!--HONumber=Dec16_HO3-->


