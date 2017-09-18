现在可以在 Azure 门户中使用数据资源管理器工具来创建图形数据库。 

1. 在 Azure 门户的左侧菜单中，选择“数据资源管理器(预览)”。

2. 在“数据资源管理器(预览)”下，选择“新建图”。 然后使用以下信息填写该页面：

    ![Azure 门户中的数据资源管理器](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    设置|建议的值|说明
    ---|---|---
    数据库 ID|sample-database|新数据库的 ID。 数据库名称的长度必须为 1 到 255 个字符，不能包含 `/ \ # ?` 或尾随空格。
    图形 ID|sample-graph|新图形的 ID。 图形名称与数据库 ID 的字符要求相同。
    存储容量| 10 GB|保留默认值。 这是数据库的存储容量。
    吞吐量|400 RU|保留默认值。 如果想要减少延迟，以后可以增加吞吐量。
    分区键|/userid|一个分区键，用于将数据均匀分配到每个分区。 选择正确的分区键对于创建高性能图形而言很重要。 有关详细信息，请参阅[设计分区](../articles/cosmos-db/partition-data.md#designing-for-partitioning)。

3. 填写表单后，请选择“确定”。