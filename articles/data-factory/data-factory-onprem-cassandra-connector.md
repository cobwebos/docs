---
title: "使用数据工厂从 Cassandra 移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从本地 Cassandra 数据库移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 6a48c11508bdc7f6f6fbfe4a504172f9944c93c0
ms.lasthandoff: 03/30/2017


---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>使用 Azure 数据工厂从本地 Cassandra 数据库移动数据
本文介绍如何使用 Azure 数据工厂中的复制活动从本地 Cassandra 数据库移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从本地 Cassandra 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 Cassandra 数据存储移至其他数据存储，而不支持将数据从其他数据存储移至 Cassandra 数据存储。 

## <a name="supported-versions"></a>支持的版本
Cassandra 连接器支持以下版本的 Cassandra：2.X。

## <a name="prerequisites"></a>先决条件
要使 Azure 数据工厂服务能够连接到本地 Cassandra 数据库，必须在托管数据库的同一计算机上或在单独的计算机上安装数据管理网关，以避免与数据库争用资源。 数据管理网关是一个以安全和托管的方式将本地数据源连接到云服务的组件。 有关数据管理网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。 有关设置网关以便数据管道移动数据的分步说明，请参阅[将数据从本地移到云](data-factory-move-data-between-onprem-and-cloud.md)一文。

必须使用网关连接到 Cassandra 数据库，即使数据库在云中托管（例如，在 Azure IaaS VM 上），也是如此。 只要网关能连接数据库，就可在托管数据库的同一 VM 上或单独的 VM 上安装网关。  

安装网关时，会自动安装用于连接到 Cassandra 数据库的 Microsoft Cassandra ODBC 驱动程序。 因此，从 Cassandra 数据库复制数据时，不需要手动在网关计算机上安装任何驱动程序。 

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 Cassandra 数据存储移动数据。 

- 创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。 
- 也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，将自动为你创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从本地 Cassandra 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 Cassandra 复制到 Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) 部分。 

对于特定于 Cassandra 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供特定于 Cassandra 链接服务的 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |“type”属性必须设置为：**OnPremisesCassandra** |是 |
| 主机 |Cassandra 服务器的一个或多个 IP 地址或主机名。<br/><br/>指定以逗号分隔的 IP 地址或主机名列表，以同时连接到所有服务器。 |是 |
| 端口 |Cassandra 服务器用来侦听客户端连接的 TCP 端口。 |否，默认值：9042 |
| authenticationType |Basic 或 Anonymous |是 |
| username |为用户帐户指定用户名。 |是（如果 authenticationType 设置为 Basic）。 |
| password |指定用户帐户的密码。 |是（如果 authenticationType 设置为 Basic）。 |
| gatewayName |用于连接到本地 Cassandra 数据库的网关的名称。 |是 |
| encryptedCredential |网关加密的凭据。 |否 |

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **CassandraTable** 数据集类型的 typeProperties 节具有以下属性

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| keyspace |Cassandra 数据库中密钥空间或架构的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |
| tableName |Cassandra 数据库中表的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

源类型为 **CassandraSource** 时，以下属性在 typeProperties 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL-92 查询或 CQL 查询。 请参阅 [CQL reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)（CQL 参考）。 <br/><br/>使用 SQL 查询时，请指定 **keyspace name.table name** 来表示要查询的表。 |否（如果定义了数据集上的 tableName 和 keyspace）。 |
| consistencyLevel |一致性级别指定在将数据返回到客户端应用程序之前必须响应读取请求的副本的数量。 Cassandra 会检查指定数量的副本，以使数据满足读取请求。 |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE。 有关详细信息，请参阅 [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)（配置数据一致性）。 |不会。 默认值为 ONE。 |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON 示例：将数据从 Cassandra 复制到 Azure Blob
此示例提供示例 JSON 定义，可使用这些定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 其中演示如何将数据从本地 Cassandra 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

此示例具有以下数据工厂实体：

* [OnPremisesCassandra](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [CassandraTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [CassandraSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

**Cassandra 链接服务：**

此示例使用 **Cassandra** 链接服务。 有关此链接服务支持的属性，请参阅 [Cassandra 链接服务](#linked-service-properties)部分。  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure 存储链接服务：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra 输入数据集：**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
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

将“external”设置为“true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**管道中使用 Cassandra 源和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，“源”类型设置为 **CassandraSource**，“接收器”类型设置为 **BlobSink**。

有关 RelationalSource 支持的属性的列表，请参阅 [RelationalSource 类型属性](#copy-activity-properties)。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]    
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Cassandra 的类型映射
| Cassandra 类型 | 基于 .Net 的类型 |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |布尔 |
| DECIMAL |小数 |
| DOUBLE |Double |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |小数 |

> [!NOTE]
> 对于集合类型（映射、集、列表等），请参阅[通过虚拟表使用 Cassandra 集合类型](#work-with-collections-using-virtual-table)部分。
>
> 不支持用户定义的类型。
>
> 二进制列和字符串列的长度不能大于 4000。
>
>

## <a name="work-with-collections-using-virtual-table"></a>通过虚拟表使用集合
Azure 数据工厂使用内置的 ODBC 驱动程序连接到 Cassandra 数据库，并从其中复制数据。 对于包括映射、集和列表在内的集合类型，该驱动程序会将数据重新规范化到相应的虚拟表中。 具体而言，如果表中包含任何集合列，则该驱动程序会生成以下虚拟表：

* **基表**，其中包含与实际表相同的数据（集合列除外）。 基表使用与其所表示的实际表相同的名称。
* 对于每个集合列都会生成一个**虚拟表**，这会扩展嵌套数据。 使用实际表名称、分隔符“*vt*”和列名称命名表示集合的虚拟表。

虚拟表引用实际表中的数据，以使驱动程序能访问非规范化的数据。 有关详细信息，请参阅示例部分。 通过查询和联接虚拟表，可访问 Cassandra 集合的内容。

可使用[复制向导](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity)直观地查看 Cassandra 数据库中的表的列表（包括虚拟表），并预览表中的数据。 还可在复制向导中构造查询，并进行验证以查看结果。

### <a name="example"></a>示例
例如，下面的“ExampleTable”是一个 Cassandra 数据库表，其中包含名为“pk_int”的整数主键列、文本列命名值、列表列、映射列和名为“StringSet”的集列。

| pk_int | 值 | 列出 | 映射 | StringSet |
| --- | --- | --- | --- | --- |
| 1 |“示例值 1” |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |“示例值 3” |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

该驱动程序会生成多个虚拟表来表示此单个表。 虚拟表中的外键列会引用实际表中的主键列，并指示该虚拟表行对应哪一个实际表行。

第一个虚拟表是名为“ExampleTable”的基表，如下表所示。 除了将在此表中被省略但在其他虚拟表中展开的集合外，该基表包含与原始数据库表相同的数据。

| pk_int | 值 |
| --- | --- |
| 1 |“示例值 1” |
| 3 |“示例值 3” |

下表中显示的虚拟表会重新规范化列表、映射和 StringSet 列中的数据。 名称以“_index”或“_key”结尾的列指示数据在原始列表或映射中的位置。 名称以“_value”结尾的列包含从集合中展开的数据。

#### <a name="table-exampletablevtlist"></a>表“ExampleTable_vt_List”：
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>表“ExampleTable_vt_Map”：
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>表“ExampleTable_vt_StringSet”：
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
若要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

