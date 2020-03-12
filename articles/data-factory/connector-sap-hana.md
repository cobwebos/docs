---
title: 从 SAP HANA 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP HANA 复制到支持的接收器数据存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125987"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP HANA 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-sap-hana-connector.md)
> * [当前版本](connector-sap-hana.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 SAP HANA 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!TIP]
>若要了解 ADF 全面支持 SAP 数据集成方案，请参阅[使用 Azure 数据工厂的 SAP 数据集成白皮书](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，并提供详细的简介、comparsion 和指南。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SAP HANA 连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 SAP HANA 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP HANA 连接器支持：

- 从任何版本的 SAP HANA 数据库复制数据。
- 从**HANA 信息模型**（如分析和计算视图）和**行/列表**复制数据。
- 使用**基本**或 **Windows** 身份验证复制数据。
- 从 SAP HANA 源进行并行复制。 有关详细信息，请参阅[SAP HANA 部分中的并行复制](#parallel-copy-from-sap-hana)。

> [!TIP]
> 要将数据复制到 SAP HANA 数据存储，请使用泛型 ODBC 连接器。 有关详细信息，请参阅 [SAP HANA 接收器](connector-odbc.md#sap-hana-sink)。 注意：适用于 SAP HANA 连接器和 ODBC 连接器的链接服务采用不同的类型，因此不能重用。

## <a name="prerequisites"></a>先决条件

要使用此 SAP HANA 连接器，需要：

- 设置自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。
- 在集成运行时计算机上安装 SAP HANA ODBC 驱动程序。 可以从 [SAP 软件下载中心](https://support.sap.com/swdc)下载 SAP HANA ODBC 驱动程序。 使用关键字 **SAP HANA CLIENT for Windows** 进行搜索。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SAP HANA 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP HANA 链接的服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapHana** | 是 |
| connectionString | 使用**基本身份验证**或**Windows 身份验证**来指定连接到 SAP HANA 所需的信息。 请参阅以下示例。<br>在 "连接字符串" 中，服务器/端口是必需的（默认端口为30015），使用基本身份验证时，用户名和密码是必需的。 有关其他高级设置，请参阅[SAP HANA ODBC 连接属性](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>你还可以将密码放在 Azure Key Vault 中，并从连接字符串中提取密码配置。 有关更多详细信息，请参阅[Azure Key Vault 文章中的存储凭据](store-credentials-in-key-vault.md)。 | 是 |
| userName 下方 | 使用 Windows 身份验证时，指定用户名。 示例： `user@domain.com` | 是 |
| password | 指定用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例：使用基本身份验证**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：使用 Windows 身份验证**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
            "password": { 
                "type": "SecureString", 
                "value": "<password>" 
            } 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

如果你使用的是具有以下有效负载的 SAP HANA 链接服务，则仍将支持它，但建议使用新的服务。

**示例：**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 SAP HANA 数据集支持的属性列表。

若要复制 SAP HANA 的数据，支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **SapHanaTable** | 是 |
| 架构 | SAP HANA 数据库中的架构的名称。 | 否（如果指定了活动源中的“query”） |
| table | SAP HANA 数据库中的表的名称。 | 否（如果指定了活动源中的“query”） |

**示例：**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果使用的是 `RelationalTable` 类型化数据集，则仍支持原样，但建议使用新的数据集。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SAP HANA 源支持的属性列表。

### <a name="sap-hana-as-source"></a>以 SAP HANA 作为源

>[!TIP]
>若要使用数据分区有效地从 SAP HANA 引入数据，请从[SAP HANA 部分的并行复制](#parallel-copy-from-sap-hana)中了解详细信息。

若要从 SAP HANA 复制数据，复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为： **SapHanaSource** | 是 |
| query | 指定要从 SAP HANA 实例读取数据的 SQL 查询。 | 是 |
| partitionOptions | 指定用于从 SAP HANA 引入数据的数据分区选项。 从 SAP HANA 部分的[并行复制中](#parallel-copy-from-sap-hana)了解详细信息。<br>允许值为： **None** （默认值）、 **PhysicalPartitionsOfTable**、 **SapHanaDynamicRange**。 从 SAP HANA 部分的[并行复制中](#parallel-copy-from-sap-hana)了解详细信息。 仅当从表（而不是查询）复制数据时，才能使用 `PhysicalPartitionsOfTable`。 <br>启用分区选项（即，不 `None`）时，从 SAP HANA 并行加载数据的并行度由复制活动上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 | False |
| partitionSettings | 指定数据分区设置的组。<br>`SapHanaDynamicRange`分区选项时应用。 | False |
| partitionColumnName | 指定分区将用于并行复制的源列的名称。 如果未指定此参数，将自动检测表的索引或主键，并将其用作分区列。<br> `SapHanaDynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfHanaDynamicRangePartitionCondition`。 请参阅[并行复制 SAP HANA](#parallel-copy-from-sap-hana)部分中的示例。 | 如果使用 `SapHanaDynamicRange` 分区，则为 "是"。 |
| packetSize | 指定将数据拆分为多个块的网络数据包大小（Kb）。 如果要复制大量数据，则在大多数情况下，增加数据包大小可能会提高 SAP HANA 的读取速度。 调整数据包大小时，建议执行性能测试。 | No。<br>默认值为2048（2MB）。 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用 `RelationalSource` 类型的复制源，则仍支持原样，但建议使用新的复制源。

## <a name="parallel-copy-from-sap-hana"></a>从 SAP HANA 并行复制

数据工厂 SAP HANA 连接器提供内置数据分区，用于并行复制 SAP HANA 中的数据。 可以在复制活动的**源表**上查找数据分区选项。

![分区选项的屏幕截图](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

启用分区副本时，数据工厂对 SAP HANA 源运行并行查询以按分区检索数据。 并行度由复制活动上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 例如，如果你将 `parallelCopies` 设置为4，则数据工厂会同时生成并运行基于指定分区选项和设置的四个查询，每个查询将从你的 SAP HANA 中检索部分数据。

建议使用数据分区启用并行复制，尤其是从 SAP HANA 中引入大量数据时。 下面是针对不同方案的建议配置。 将数据复制到基于文件的数据存储时，建议以多个文件（仅指定文件夹名称）的形式写入文件夹，在这种情况下，性能比写入单一文件更好。

| 场景                                           | 建议的设置                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 大表的完全加载。                        | **分区选项**：表的物理分区。 <br><br/>在执行期间，数据工厂会自动检测指定 SAP HANA 表的物理分区类型，并选择相应的分区策略：<br>- **范围分区**：获取为表定义的分区列和分区范围，然后按范围复制数据。 <br>- **哈希**分区：使用哈希分区键作为分区列，然后基于 ADF 计算范围对数据进行分区和复制。 <br>- **轮循区分区**或**无分区**：使用主密钥作为分区列，然后基于 ADF 计算范围对数据进行分区和复制。 |
| 使用自定义查询加载大量数据。 | **Partition 选项**：动态范围分区。<br>**查询**： `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`。<br>**分区列**：指定用于应用动态范围分区的列。 <br><br>在执行期间，数据工厂首先计算指定分区列的值范围，方法是根据非重复分区列值和 ADF 并行复制设置的数量，将 `?AdfHanaDynamicRangePartitionCondition` 替换为筛选每个分区的分区列值范围，并将其发送到 SAP HANA。<br><br>如果要使用多个列作为分区列，可以在查询中将每列的值连接为一个列，并将其指定为 ADF 中的分区列，如 `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`。 |

**示例：使用表的物理分区进行查询**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**示例：包含动态范围分区的查询**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA 的数据类型映射

从 SAP HANA 复制数据时，以下映射用于从 SAP HANA 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP HANA 数据类型 | 数据工厂临时数据类型 |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
