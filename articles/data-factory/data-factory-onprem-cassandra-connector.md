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
ms.sourcegitcommit: af15b530dd512873e4534fb61d276c8c8c3a196a
ms.openlocfilehash: 7b7d3b87e1285993d744e74d01f5192732b70e77


---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>使用 Azure 数据工厂从本地 Cassandra 数据库移动数据
本文概括介绍了如何使用 Azure 数据工厂中的“复制活动”将数据从本地 Cassandra 数据库复制到[支持的源和接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)部分的“接收器”列下所列出的任意数据存储。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂当前仅支持将数据从 Cassandra 数据库移动到[支持的接收器数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)，而不支持将数据从其他数据存储移动到 Cassandra 数据库。

## <a name="supported-versions"></a>支持的版本
此 Cassandra 连接器支持 Cassandra 版本 2.X。

## <a name="prerequisites"></a>先决条件
若要使 Azure 数据工厂能够连接到本地 Cassandra 数据库，必须安装以下组件：

* 在托管数据库的同一计算机上（若希望避免其与数据库争用资源，则在另一台计算机上），安装数据管理网关 2.0 或更高版本。 数据管理网关是一种以安全和托管的方式将本地数据源连接到云服务的软件。 有关数据管理网关的详细信息，请参阅[在本地与云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

    安装网关时，会自动安装用于连接到 Cassandra 数据库的 Microsoft Cassandra ODBC 驱动程序。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。
>
>

## <a name="copy-data-wizard"></a>复制数据向导
若要创建将数据从 Cassandra 数据库复制到任何支持的接收器数据存储的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据从 Cassandra 数据库复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。   

## <a name="sample-copy-data-from-cassandra-to-blob"></a>示例：将数据从 Cassandra 复制到 Blob
此示例中每隔一小时会将数据从 Cassandra 数据库复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。 可使用 Azure 数据工厂中的复制活动，直接将数据复制到[数据移动活动](data-factory-data-movement-activities.md#supported-data-stores-and-formats)一文中所述的任何接收器。

* [OnPremisesCassandra](#onpremisescassandra-linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
* [CassandraTable](#cassandratable-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [CassandraSource](#cassandrasource-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

**Cassandra 链接服务**

此示例使用 **Cassandra** 链接服务。 有关此链接服务支持的属性，请参阅 [Cassandra 链接服务](#onpremisescassandra-linked-service-properties)部分。  

```JSON
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

**Azure 存储链接服务**

```JSON
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

**Cassandra 输入数据集**

```JSON
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

**Azure Blob 输出数据集**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。

```JSON
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

**包含复制活动的管道**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，“源”类型设置为 **CassandraSource**，“接收器”类型设置为 **BlobSink**。

有关 RelationalSource 支持的属性的列表，请参阅 [RelationalSource 类型属性](#cassandrasource-type-properties)。

```JSON
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

## <a name="onpremisescassandra-linked-service-properties"></a>OnPremisesCassandra 链接服务属性
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

## <a name="cassandratable-properties"></a>CassandraTable 属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **CassandraTable** 数据集类型的 typeProperties 节具有以下属性

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| keyspace |Cassandra 数据库中密钥空间或架构的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |
| tableName |Cassandra 数据库中表的名称。 |是（如果未定义 **CassandraSource** 的**查询**）。 |

## <a name="cassandrasource-type-properties"></a>CassandraSource 类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

源类型为 **CassandraSource** 时，以下属性在 typeProperties 节中可用：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL-92 查询或 CQL 查询。 请参阅 [CQL reference](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)（CQL 参考）。 <br/><br/>使用 SQL 查询时，请指定 **keyspace name.table name** 来表示要查询的表。 |否（如果定义了数据集上的 tableName 和 keyspace）。 |
| consistencyLevel |一致性级别指定在将数据返回到客户端应用程序之前必须响应读取请求的副本的数量。 Cassandra 会检查指定数量的副本，以使数据满足读取请求。 |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE。 有关详细信息，请参阅 [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)（配置数据一致性）。 |不会。 默认值为 ONE。 |

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

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。



<!--HONumber=Feb17_HO2-->


