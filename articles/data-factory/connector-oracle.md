---
title: 使用 Azure 数据工厂向/从 Oracle 复制数据
description: 了解如何使用数据工厂将数据从支持的源存储复制到 Oracle 数据库，或从 Oracle 复制到支持的接收器存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: b43ec36f6f3a9111656892c65af2592fce6eaed2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931754"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Oracle 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-oracle-connector.md)
> * [当前版本](connector-oracle.md)

本文概述如何使用 Azure 数据工厂中的复制活动将数据从和复制到 Oracle 数据库。 它基于[复制活动概述](copy-activity-overview.md)进行构建。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Oracle 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Oracle 数据库复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Oracle 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Oracle 连接器支持：

- 以下版本的 Oracle 数据库：
    - Oracle 18c R1 （18.1）及更高版本
    - Oracle 12c R1 （12.1）及更高版本
    - Oracle 11g R1 （11.1）及更高版本
    - Oracle 10g R1 （10.1）及更高版本
    - Oracle 9i R2 （9.2）及更高版本
    - Oracle 8i R3 （8.1.7）及更高版本
    - Oracle Database Cloud Exadata Service
- 使用基本或 OID 身份验证复制数据。
- 从 Oracle 源进行并行复制。 有关详细信息，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。

> [!Note]
> 不支持 Oracle 代理服务器。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

集成运行时提供内置的 Oracle 驱动程序。 因此，在从/向 Oracle 复制数据时不需要手动安装驱动程序。

## <a name="get-started"></a>开始体验

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Oracle 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Oracle 链接服务支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **Oracle**。 | 是 |
| connectionString | 指定连接到 Oracle 数据库实例所需的信息。 <br/>将此字段标记为 `SecureString`，以将其安全地存储在数据工厂中。 你还可以将密码放在 Azure Key Vault 中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例并[将凭据存储在 Azure Key Vault 中](store-credentials-in-key-vault.md)。 <br><br>**支持的连接类型**：可以使用 **Oracle SID** 或 **Oracle 服务名称**来标识数据库：<br>- 如果使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果使用服务名称：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |No |

>[!TIP]
>如果收到错误 "TNSNAMES.ORA-01025： UPI 参数超出范围"，并且 Oracle 版本为8i，请将 `WireProtocolMode=1` 添加到连接字符串。 然后重试。

可以在连接字符串中根据情况设置更多的连接属性：

| properties | 描述 | 允许的值 |
|:--- |:--- |:--- |
| 数组大小 |连接器可在单个网络往返过程中提取的字节数。 例如，`ArraySize=‭10485760‬`。<br/><br/>较大的值可通过减少在网络中提取数据的次数来提高吞吐量。 值越小，响应时间就会增加，因为等待服务器传输数据的延迟较少。 | 1到4294967296（4 GB）之间的一个整数。 默认值为 `60000`。 值1不定义字节数，但指示只为一行数据分配空间。 |

若要在 Oracle 连接上启用加密，你有两种选择：

-   若要使用**三重 DES （3des）和高级加密标准（AES）** ，请在 oracle 服务器端中转到 "Oracle 高级安全（OAS）" 并配置加密设置。 有关详细信息，请参阅此[Oracle 文档](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)。 Oracle 应用程序开发框架（ADF）连接器会自动协商加密方法，以使用在建立与 Oracle 的连接时在 OAS 中配置的方法。

-   使用**SSL**：

    1.  获取 SSL 证书信息。 获取 SSL 证书的可辨别编码规则（DER）编码的证书信息，并保存输出（-----"开始证书 ..." End Certificate -----) 另存为文本文件。

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
    
    2.  生成 `keystore` 或 `truststore`。 以下命令将创建带或不带密码的 `truststore` 文件，格式为 PKCS-12。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **示例：** 创建一个名为 MyTrustStoreFile 的 PKCS12 `truststore` 文件，其中包含一个密码。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  将 `truststore` 文件放在自承载 IR 计算机上。 例如，将文件放在 C:\MyTrustStoreFile。
    4.  在 Azure 数据工厂中，配置包含 `EncryptionMethod=1` 的 Oracle 连接字符串，并将相应的 `TrustStore`/`TrustStorePassword`值。 例如，`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>` 。

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

本部分提供 Oracle 数据集支持的属性列表。 有关可用于定义数据集的各节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 

若要从和向 Oracle 复制数据，请将数据集的 type 属性设置为 `OracleTable`。 支持以下属性。

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 `OracleTable`。 | 是 |
| schema | 架构的名称。 |对于源为“No”，对于接收器为“Yes”  |
| 表 | 表/视图的名称。 |对于源为“No”，对于接收器为“Yes”  |
| tableName | 具有架构的表/视图的名称。 支持此属性是为了向后兼容。 对于新工作负荷，请使用 `schema` 和 `table`。 | 对于源为“No”，对于接收器为“Yes” |

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
>若要使用数据分区有效地从 Oracle 加载数据，请从[oracle 的并行复制](#parallel-copy-from-oracle)中了解详细信息。

若要从 Oracle 复制数据，请将复制活动中的源类型设置为 "`OracleSource`"。 复制活动的 **source** 节支持以下属性。

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 `OracleSource`。 | 是 |
| oracleReaderQuery | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区加载时，需要在查询中挂接任何对应的内置分区参数。 有关示例，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。 | No |
| partitionOptions | 指定用于从 Oracle 加载数据的数据分区选项。 <br>允许的值为： **None** （默认值）、 **PhysicalPartitionsOfTable**和**DynamicRange**。<br>启用分区选项（即，不 `None`）时，从 Oracle 数据库并发加载数据的并行度由复制活动上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 | No |
| partitionSettings | 指定数据分区设置的组。 <br>当 partition 选项不 `None`时应用。 | No |
| partitionNames | 需要复制的物理分区的列表。 <br>`PhysicalPartitionsOfTable`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfTabularPartitionName`。 有关示例，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。 | No |
| partitionColumnName | 指定**整数类型**的源列名称，将由范围分区用于并行复制。 如果未指定此参数，则将自动检测该表的主键，并将其用作分区列。 <br>`DynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionColumnName`。 有关示例，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。 | No |
| partitionUpperBound | 要向其复制数据的分区列的最大值。 <br>`DynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionUpbound`。 有关示例，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。 | No |
| partitionLowerBound | 要向其复制数据的分区列的最小值。 <br>`DynamicRange`分区选项时应用。 如果使用查询来检索源数据，则在 WHERE 子句中挂接 `?AdfRangePartitionLowbound`。 有关示例，请参阅[从 Oracle 并行复制](#parallel-copy-from-oracle)部分。 | No |

**示例：使用不带分区的基本查询复制数据**

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

若要向 Oracle 复制数据，请将复制活动中的接收器类型设置为 "`OracleSink`"。 复制活动 **sink** 节支持以下属性。

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 `OracleSink`。 | 是 |
| writeBatchSize | 当缓冲区大小达到 `writeBatchSize`时，将数据插入 SQL 表中。<br/>允许的值为 Integer（行数）。 |否（默认值为 10,000） |
| writeBatchTimeout | 超时前等待批插入操作完成的时间。<br/>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | No |
| preCopyScript | 指定一个 SQL 查询，以便在每次运行将数据写入 Oracle 之前运行复制活动。 可以使用此属性清除预加载的数据。 | No |

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

## <a name="parallel-copy-from-oracle"></a>从 Oracle 并行复制

数据工厂 Oracle 连接器提供内置数据分区，用于并行复制 Oracle 数据。 可以在复制活动的 "**源**" 选项卡上找到数据分区选项。

![分区选项的屏幕截图](./media/connector-oracle/connector-oracle-partition-options.png)

启用分区副本时，数据工厂对 Oracle 源运行并行查询以按分区加载数据。 并行度由复制活动上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 例如，如果将 `parallelCopies` 设置为4，则数据工厂会根据指定的分区选项和设置同时生成并运行四个查询，每个查询将从 Oracle 数据库中检索部分数据。

建议你在使用数据分区时启用并行复制，尤其是在从 Oracle 数据库加载大量数据时。 下面是针对不同方案的建议配置。 将数据复制到基于文件的数据存储时，将 recommanded 写入文件夹作为多个文件（仅指定文件夹名称），在这种情况下，性能比写入单个文件更好。

| 场景                                                     | 建议设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 包含物理分区的大型表的完全加载。          | **分区选项**：表的物理分区。 <br><br/>在执行期间，数据工厂会自动检测物理分区，并按分区复制数据。 |
| 完全加载大型表，没有物理分区，而使用整数列进行数据分区。 | **分区选项**：动态范围分区。<br>**分区列**：指定用于对数据进行分区的列。 如果未指定，则使用主键列。 |
| 使用带有物理分区的自定义查询加载大量数据。 | **分区选项**：表的物理分区。<br>**查询**： `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**分区名称**：指定要从中复制数据的分区名称。 如果未指定，数据工厂会自动检测您在 Oracle 数据集中指定的表的物理分区。<br><br>在执行期间，数据工厂会将 `?AdfTabularPartitionName` 替换为实际分区名称，并将其发送到 Oracle。 |
| 使用自定义查询而不使用物理分区加载大量数据，而使用整数列进行数据分区。 | **分区选项**：动态范围分区。<br>**查询**： `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**：指定用于对数据进行分区的列。 可以对具有整数数据类型的列进行分区。<br>**分区上限**和**分区下限**：指定是否希望根据分区列进行筛选，以便只检索下限和上限之间的数据。<br><br>在执行期间，数据工厂会将 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`和 `?AdfRangePartitionLowbound` 替换为每个分区的实际列名称和值范围，并发送到 Oracle。 <br>例如，如果将分区列 "ID" 设置为下限为1，上限为80，并将 "并行副本" 设置为 "4"，则数据工厂将按4个分区检索数据。 它们的 Id 分别介于 [1，20]、[21，40]、[41、60] 和 [61，80] 之间。 |

**示例：具有物理分区的查询**

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

**示例：包含动态范围分区的查询**

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

从和向 Oracle 复制数据时，将应用以下映射。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Oracle 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>（仅支持 Oracle 10g 和更高版本） |
| CHAR |字符串 |
| CLOB |字符串 |
| DATE |日期/时间 |
| FLOAT |十进制、字符串（如果精度 > 28） |
| INTEGER |十进制、字符串（如果精度 > 28） |
| LONG |字符串 |
| LONG RAW |Byte[] |
| NCHAR |字符串 |
| NCLOB |字符串 |
| NUMBER |十进制、字符串（如果精度 > 28） |
| NVARCHAR2 |字符串 |
| RAW |Byte[] |
| ROWID |字符串 |
| TIMESTAMP |日期/时间 |
| TIMESTAMP WITH LOCAL TIME ZONE |字符串 |
| TIMESTAMP WITH TIME ZONE |字符串 |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |字符串 |
| XML |字符串 |

> [!NOTE]
> 不支持数据类型 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
