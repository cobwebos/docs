---
title: 使用 HBase .NET SDK - Azure HDInsight | Microsoft Docs
description: 使用 HBase .NET SDK 创建和删除表，以及读取和写入数据。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: a1b9cd3fd9dbad5d8f27027a97c284b1bc3ba783
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="use-the-hbase-net-sdk"></a>使用 HBase .NET SDK

[HBase](apache-hbase-overview.md) 提供两种主要的数据处理选择：[Hive 查询，以及调用 HBase 的 RESTful API](apache-hbase-tutorial-get-started-linux.md)。 可以通过 `curl` 命令或类似的实用工具直接使用 REST API。

对于 C# 和 .NET 应用程序，[用于 .NET 的 Microsoft HBase REST 客户端库](https://www.nuget.org/packages/Microsoft.HBase.Client/)提供基于 HBase REST API 的客户端库。

## <a name="install-the-sdk"></a>安装 SDK

HBase .NET SDK 以 NuGet 包的形式提供，可以使用以下命令通过 Visual Studio **NuGet 包管理器控制台**进行安装：

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>实例化新的 HBaseClient 对象

若要使用 SDK，请实例化新的 `HBaseClient` 对象，将包含 `Uri` 的 `ClusterCredentials` 传递到群集，并传递 Hadoop 用户名和密码。

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

将 CLUSTERNAME 替换为 HDInsight HBase 群集名称，将 USERNAME 和 PASSWORD 替换为在创建群集时指定的 Hadoop 凭据。 默认的 Hadoop 用户名为 **admin**。

## <a name="create-a-new-table"></a>创建新表

HBase 在表中存储数据。 表包含 *Rowkey*、主键以及一个或多个名为“列系列”的列组。 每个表中的数据按 Rowkey 范围水平分布到多个区域。 每个区域都有一个开始键和结束键。 一个表可以有一个或多个区域。 随着表中数据的增长，HBase 会将大区域拆分为较小的区域。 区域存储在区域服务器中，一个区域服务器可以存储多个区域。

数据以物理方式存储在 *HFile* 中。 单个 HFile 包含的数据适用于一个表、一个区域和一个列系列。 HFile 中的行在存储时按 Rowkey 排序。 每个 HFile 都有一个 B+ 树索引，用于快速检索行。

若要创建新表，请指定 `TableSchema` 和列。 以下代码检查“RestSDKTable”表是否已存在 - 如果不存在，则会创建该表。

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

此新表有两个列系列：t1 和 t2。 由于列系列单独存储在不同的 HFile 中，因此对于频繁查询的数据，应该有一个单独的列系列。 在下面的[插入数据](#insert-data)示例中，列添加到 t1 列系列中。

## <a name="delete-a-table"></a>删除表

若要删除表，请执行以下操作：

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>插入数据

若要插入数据，请指定唯一行键作为行标识符。 所有数据存储在 `byte[]` 数组中。 以下代码定义 `title`、`director` 和 `release_date` 列并将其添加到 t1 列系列，因为这些列是访问最频繁的。 `description` 和 `tagline` 列添加到 t2 列系列。 可以根据需要将数据分区，分为多个列系列。

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase 可实现 BigTable，因此数据格式如下所示：

![具有“群集用户”角色的用户](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>选择数据

若要从 HBase 表中读取数据，请将表名称和行键传递到 `GetCellsAsync` 方法，以便返回 `CellSet`。

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

在这种情况下，代码只返回第一个匹配行，因为一个唯一键只应有一个行。 返回的值从 `byte[]` 数组更改成 `string` 格式。 也可将值转换为其他类型，例如表示电影发布日期的整数：

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>对行进行扫描

HBase 使用 `scan` 来检索一个或多个行。 此示例请求多个行（10 个一批），并检索其键值在 25 到 35 之间的数据。 在检索所有行之后，请删除扫描仪，对资源进行清理。

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>后续步骤

* [HDInsight 中的 Apache HBase 入门示例](apache-hbase-tutorial-get-started-linux.md)
* 根据[使用 HBase 分析实时 Twitter 观点](../hdinsight-hbase-analyze-twitter-sentiment.md)中的说明，生成端到端应用程序
