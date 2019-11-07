---
title: 使用 Azure 数据工厂向/从 Oracle 复制数据
description: 了解如何使用数据工厂将数据从支持的源存储复制到 Oracle 数据库，或从 Oracle 复制到支持的接收器存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: d8cbc7410f2b2bd525148cee9dc5b8ddbb756dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680506"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Oracle 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-oracle-connector.md)
> * [当前版本](connector-oracle.md)

本文概述如何在 Azure 数据工厂中使用复制活动从/向 Oracle 数据库复制数据。 本文是在[复制活动概述](copy-activity-overview.md)的基础上编写的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Oracle 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Oracle 数据库复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Oracle 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Oracle 连接器支持：

- 以下版本的 Oracle 数据库：
    - Oracle 18c R1 (18.1) 和更高版本
    - Oracle 12c R1 (12.1) 和更高版本
    - Oracle 11g R1 (11.1) 和更高版本
    - Oracle 10g R1 (10.1) 和更高版本
    - Oracle 9i R2 (9.2) 和更高版本
    - Oracle 8i R3 (8.1.7) 和更高版本
    - Oracle Database Cloud Exadata Service
- 使用“基本”或“OID”身份验证复制数据。
- 从 Oracle 源进行并行复制。 有关详细信息，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。

> [!Note]
> 不支持 Oracle 代理服务器。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

集成运行时提供内置的 Oracle 驱动程序。 因此，在从/向 Oracle 复制数据时不需要手动安装驱动程序。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Oracle 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Oracle 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **Oracle**。 | 是 |
| connectionString | 指定连接到 Oracle 数据库实例所需的信息。 <br/>将此字段标记为 `SecureString`，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure Key Vault 中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure Key Vault 中存储凭据](store-credentials-in-key-vault.md)。 <br><br>**支持的连接类型**：可以使用 **Oracle SID** 或 **Oracle 服务名称**来标识数据库：<br>- 如果使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果使用服务名称：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!TIP]
>如果收到错误 "TNSNAMES.ORA-01025： UPI 参数超出范围"，并且 Oracle 版本为8i，请将 `WireProtocolMode=1` 添加到连接字符串。 然后重试。

可以在连接字符串中根据情况设置更多的连接属性：

| 属性 | 说明 | 允许的值 |
|:--- |:--- |:--- |
| 数组大小 |连接器可在单个网络往返过程中提取的字节数。 例如，`ArraySize=‭10485760‬`。<br/><br/>较大的值可通过减少在网络中提取数据的次数来提高吞吐量。 值越小，响应时间就会增加，因为等待服务器传输数据的延迟较少。 | 1到4294967296（4 GB）之间的一个整数。 默认值为 `60000`。 值1不定义字节数，但指示只为一行数据分配空间。 |

若要在 Oracle 连接上启用加密，你有两种选择：

-   若要使用**三重 DES 加密 (3DES) 和高级加密标准 (AES)** ，请在 Oracle 服务器端，转到“Oracle 高级安全性 (OAS)”并配置加密设置。 有关详细信息，请参阅 [Oracle 文档](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)。 Oracle 应用程序开发框架 (ADF) 连接器会自动协商加密方法，以便在建立与 Oracle 的连接时使用在 OAS 中配置的加密方法。

-   若要使用 **SSL**：

    1.  获取 SSL 证书信息。 获取 SSL 证书的可辨别编码规则 (DER) 编码证书信息，并将输出 (----- Begin Certificate … End Certificate -----) 另存为文本文件。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **示例：** 从 DERcert 提取证书信息，然后将输出保存到 cert。

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  生成 `keystore` 或 `truststore`。 以下命令使用或不使用 PKCS-12 格式的密码来创建 `truststore` 文件。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **示例：** 创建一个名为 MyTrustStoreFile 的 PKCS12 `truststore` 文件，其中包含一个密码。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  将 `truststore` 文件放在自承载 IR 计算机上。 例如，将该文件放在 C:\MyTrustStoreFile。
    4.  在 Azure 数据工厂中，使用 `EncryptionMethod=1` 和相应的 `TrustStore`/`TrustStorePassword` 值配置 Oracle 连接字符串。 例如，`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`。

**示例：**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

本部分提供 Oracle 数据集支持的属性列表。 有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 

若要从/向 Oracle 复制数据，请将数据集的 type 属性设置为 `OracleTable`。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 `OracleTable`。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

**示例：**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

本部分提供 Oracle 源和接收器支持的属性列表。 有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 

### <a name="oracle-as-source"></a>Oracle 作为源

>[!TIP]
>有关如何使用数据分区从 Oracle 有效加载数据的详细信息，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。

要从 Oracle 复制数据，请将复制活动中的源类型设置为 `OracleSource`。 复制活动**源**部分支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动 source 的 type 属性必须设置为 `OracleSource`。 | 是 |
| oracleReaderQuery | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何相应的内置分区参数。 有关示例，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。 | 否 |
| partitionOptions | 指定用于从 Oracle 加载数据的数据分区选项。 <br>允许的值为： **None** （默认值）、 **PhysicalPartitionsOfTable**和**DynamicRange**。<br>启用分区选项（即，该选项不为 `None`）时，用于从 Oracle 数据库并行加载数据的并行度由复制活动上的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制。 | 否 |
| partitionSettings | 指定数据分区的设置组。 <br>当分区选项不是 `None` 时适用。 | 否 |
| partitionNames | 需要复制的物理分区的列表。 <br>当分区选项是 `PhysicalPartitionsOfTable` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfTabularPartitionName`。 有关示例，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。 | 否 |
| partitionColumnName | 指定并行复制范围分区使用的源列（**整数类型**）的名称。 如果未指定，系统会自动检测表的主键并将其用作分区列。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionColumnName`。 有关示例，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。 | 否 |
| partitionUpperBound | 要从中复制数据的分区列的最大值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 有关示例，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。 | 否 |
| partitionLowerBound | 要从中复制数据的分区列的最小值。 <br>当分区选项是 `DynamicRange` 时适用。 如果使用查询来检索源数据，请在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 有关示例，请参阅[从 Oracle 进行并行复制](#parallel-copy-from-oracle)部分。 | 否 |

**示例：使用基本查询但不使用分区复制数据**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle 作为接收器

若要向 Oracle 复制数据，请将复制活动中的接收器类型设置为 `OracleSink`。 复制活动接收器部分中支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 `OracleSink`。 | 是 |
| writeBatchSize | 缓冲区大小达到 `writeBatchSize` 时将数据插入 SQL 表。<br/>允许的值为 Integer（行数）。 |否（默认值为 10,000） |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | 否 |
| preCopyScript | 指定每次运行时，复制活动将数据写入到 Oracle 之前要运行的 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>从 Oracle 进行并行复制

数据工厂 Oracle 连接器提供内置的数据分区，用于从 Oracle 并行复制数据。 可以在复制活动的“源”表中找到数据分区选项。

![分区选项的屏幕截图](./media/connector-oracle/connector-oracle-partition-options.png)

启用分区复制时，数据工厂将对 Oracle 源运行并行查询，以按分区加载数据。 可通过复制活动中的 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 设置控制并行度。 例如，如果将 `parallelCopies` 设置为 4，则数据工厂会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 Oracle 数据库检索一部分数据。

建议同时启用并行复制和数据分区，尤其是从 Oracle 数据库加载大量数据时。 下面是适用于不同方案的建议配置。 将数据复制到基于文件的数据存储中时，建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

| 方案                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从包含物理分区的大型表进行完整加载。          | **分区选项**：表的物理分区。 <br><br/>在执行期间，数据工厂将自动检测物理分区并按分区复制数据。 |
| 从不包含物理分区但包含用于数据分区的整数列的大型表进行完整加载。 | **分区选项**：动态范围分区。<br>**分区列**：指定用于对数据进行分区的列。 如果未指定，将使用主键列。 |
| 使用自定义查询从包含物理分区的表加载大量数据。 | **分区选项**：表的物理分区。<br>**查询**：`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**分区名称**：指定要从中复制数据的分区名称。 如果未指定，数据工厂将自动检测在 Oracle 数据集中指定的表的物理分区。<br><br>在执行期间，数据工厂会将 `?AdfTabularPartitionName` 替换为实际分区名称并发送到 Oracle。 |
| 使用自定义查询从不包含物理分区但包含用于数据分区的整数列的表加载大量数据。 | **分区选项**：动态范围分区。<br>**查询**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以针对整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否希望根据分区列进行筛选，以便只检索下限和上限之间的数据。<br><br>在执行期间，数据工厂会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound` 和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名和值范围，并将其发送到 Oracle。 <br>例如，如果为分区列“ID”设置了下限 1、上限 80，并将并行复制设置为 4，则数据工厂会按 4 个分区检索数据。 其 ID 分别介于 [1, 20]、[21, 40]、[41, 60] 和 [61, 80] 之间。 |

**示例：使用物理分区进行查询**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**示例：使用动态范围分区进行查询**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle 的数据类型映射

从/向 Oracle 复制数据时，将应用以下映射。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Oracle 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>（仅支持 Oracle 10g 和更高版本） |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |十进制、字符串（如果精度 > 28） |
| INTEGER |十进制、字符串（如果精度 > 28） |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |十进制、字符串（如果精度 > 28） |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 不支持数据类型 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND。

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
