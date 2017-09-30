## <a name="repeatability-during-copy"></a>复制期间的可重复性
从/向关系存储复制数据时，需要注意可重复性，以免发生意外结果。 

可以根据指定的重试策略在 Azure 数据工厂中自动重新运行切片。 我们建议设置重试策略，防止出现暂时性故障。 可重复性是移动数据时的重要考虑因素。 

**源：**

> [!NOTE]
> 以下示例适用于 Azure SQL，但同样适用于任何支持矩形数据集的数据存储。 可能需要针对数据存储调整源的**类型**和 **query** 属性（例如，使用 query 而不是 sqlReaderQuery）。   
> 
> 

从关系存储读取数据时，我们通常只希望读取对应于该切片的数据。 使用 Azure 数据工厂中提供的 WindowStart 和 WindowEnd 变量可实现此目的。 有关 Azure 数据工厂中的变量和函数，请阅读[计划和执行](../articles/data-factory/v1/data-factory-scheduling-and-execution.md)一文。 示例： 

```json
"source": {
"type": "SqlSource",
"sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
此查询从“MyTable”中读取处于切片持续时间范围内的数据。 重新运行此切片也始终能够确保发生此行为。 

在其他情况下，我们可能需要读取整个表（假设仅进行一次性移动），并可按如下所示定义 sqlReaderQuery：

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```
