<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>管理表实体

至此，你已创建了一个表，接下来让我们了解如何管理表中的实体或行。 

一个实体最多可以有 255 个属性，其中包括 3 个系统属性：**PartitionKey**、**RowKey** 和 **Timestamp**。 需要负责插入和更新 **PartitionKey** 与 **RowKey** 的值。 服务器将管理 **Timestamp** 的值，该值不可修改。 **PartitionKey** 和 **RowKey** 共同唯一标识表中的每个实体。

* **PartitionKey**：确定实体存储在其中的分区。
* **RowKey**：唯一标识分区内的实体。

最多可为一个实体定义 252 个自定义属性。 

### <a name="add-table-entities"></a>添加表实体

使用 Add-StorageTableRow 向表中添加实体。 这些示例使用值为“partition1”和“partition2”的分区键，且行键等于状态缩写。 每个实体中的属性为 username 和 userid。 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>查询表实体

在表中查询实体有多种不同的方法。

#### <a name="retrieve-all-entities"></a>检索所有实体

若要检索所有实体，请使用 Get-AzureStorageTableRowAll。

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

此命令得到的结果类似于下表所示：

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| #N/A | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>检索特定分区的实体

若要检索特定分区中的所有实体，请使用 Get AzureStorageTableRowByPartitionKey。

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
结果类似于下表所示：

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>在特定列中检索具有特定值的实体

若要在特定列中检索值等于特定值的实体，请使用 Get-AzureStorageTableRowByColumnName。

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

此查询将检索一条记录。

|字段|值|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>使用自定义筛选器检索实体 

若要使用自定义筛选器检索实体，请使用 Get AzureStorageTableRowByCustomFilter。

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

此查询将检索一条记录。

|字段|值|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>更新实体 

更新实体有三个步骤。 第一步，检索要更改的实体。 第二步，进行更改。 第三步，使用 Update-AzureStorageTableRow 提交更改。

将 username = 'Jessie' 的实体更改为 username = 'Jessie2'。 此示例还介绍了使用 .NET 类型创建自定义筛选器的另一种方法。 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

结果将显示 Jessie2 记录。

|字段|值|
|----|----|
| userid | #N/A |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>删除表实体

可以删除表中的一个实体或所有实体。

#### <a name="deleting-one-entity"></a>删除一个实体

若要删除单个实体，请获取对该实体的引用并将其传递到 Remove-AzureStorageTableRow。

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>删除表中的所有实体 

若要删除表中的所有实体，请检索所有实体，并将结果通过管道传递到删除 cmdlet。 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```