## <a name="column-mapping-with-translator-rules"></a>使用转换器规则进行列映射
使用列映射可以指定如何将源表“结构”中指定的列映射到接收器表“结构”中指定的列。 可以在复制活动的 **typeProperties** 节中使用 **ColumnMapping** 属性。

列映射支持以下方案：

* 源表“结构”中的所有列映射到接收器表“结构”中的所有列。
* 源表“结构”中的列子集映射到接收器表“结构”中的所有列。

以下状态是错误状态，会导致异常：

* 接收器表“结构”中的列数量多于或少于映射中指定的数量。
* 重复的映射。
* SQL 查询结果中没有映射中指定的列名。

## <a name="column-mapping-samples"></a>列映射示例
> [!NOTE]
> 以下示例适用于 Azure SQL 和 Azure Blob，但也适用于任何支持矩形数据集的数据存储。 必须调整以下示例中的数据集与链接服务定义，使其指向相关数据源中的数据。 
> 
> 

### <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>示例 1 – 从 Azure SQL 到 Azure Blob 的列映射
在本示例中，输入表有一个结构，并指向 Azure SQL 数据库中的 SQL 表。

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

在本示例中，输出表有一个结构，并指向 Azure Blob 存储中的 Blob。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
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
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

活动的 JSON 如下所示。 源中的列使用 **Translator** 属性映射到接收器中的列 (**columnMappings**)。

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**列映射流：**

![列映射流](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>示例 2 – 使用 SQL 查询从 Azure SQL 到 Azure Blob 的列映射
本示例使用 SQL 查询从 Azure SQL 提取数据，而不是只在“结构”部分中指定表名和列名。 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
在此情况下，查询结果先映射到源“结构”所指定的列。 接下来，源“结构”中的列使用 columnMappings 中指定的规则映射到接收器“结构”中的列。  假设该查询依次返回 5 个列、2 个附加列和源“结构”中指定的列。

**列映射流**

![列映射流-2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)



<!--HONumber=Nov16_HO3-->


