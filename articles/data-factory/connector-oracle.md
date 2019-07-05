---
title: 使用 Azure 数据工厂向/从 Oracle 复制数据 | Microsoft Docs
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
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509231"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 Oracle 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-oracle-connector.md)
> * [当前版本](connector-oracle.md)

本文概述了如何在 Azure 数据工厂中使用复制活动从/向 Oracle 数据库复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Oracle 数据库复制到任何支持的接收器数据存储。 还可以将数据从任何支持的源数据存储复制到 Oracle 数据库。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Oracle 连接器支持：

- 以下版本的 Oracle 数据库：
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)
- 使用来复制数据**基本**或**OID**身份验证。
- 从 Oracle 源的并行副本。 请参阅[并行复制从 Oracle](#parallel-copy-from-oracle)部分的详细信息。

> [!Note]
> 不支持 Oracle 代理服务器。

## <a name="prerequisites"></a>必备组件

若要从/向不可公开访问的 Oracle 数据库复制数据，需要设置自承载集成运行时。 有关集成运行时的详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)。 集成运行时提供内置的 Oracle 驱动程序。 因此，在从/向 Oracle 复制数据时不需要手动安装驱动程序。

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Oracle 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Oracle 链接服务支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **Oracle**。 | 是 |
| connectionString | 指定连接到 Oracle 数据库实例所需的信息。 <br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[在 Azure 密匙保管库中存储凭据](store-credentials-in-key-vault.md)一文。 <br><br>**支持的连接类型**：可以使用 **Oracle SID** 或 **Oracle 服务名称**来标识数据库：<br>- 如果使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果使用服务名称：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure Integration Runtime 时。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

>[!TIP]
>如果遇到错误消息指出“ORA-01025: UPI 参数超出范围”，且 Oracle 版本为 8i，请将 `WireProtocolMode=1` 添加到连接字符串并重试。

若要在 Oracle 连接上启用加密，你有两种选择： 

1.  若要使用三重 DES 加密 (3DES) 和高级加密标准 (AES)，在 Oracle 服务器端，转到“Oracle 高级安全性 (OAS)”并配置加密设置，请参阅[此处](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)的详细信息  。 ADF Oracle 连接器会自动协商加密方法，以便在建立与 Oracle 的连接时使用在 OAS 中配置的加密方法。

2.  若要使用 SSL，请执行以下步骤  ：

    1.  获取 SSL 证书信息。 获取 SSL 证书的 DER 编码证书信息，并将输出 (----- Begin Certificate … End Certificate -----) 另存为文本文件。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        示例：从 DERcert.cer 提取证书信息；然后，将输出保存到 cert.txt 

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
    
    2.  生成密钥存储或信任存储。 以下命令使用或不使用 PKCS-12 格式的密码来创建信任存储文件。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **示例：** 创建名为密码 MyTrustStoreFile 的 PKCS12 truststore 文件

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  将信任存储文件放于自承载 IR 计算机上，例如，C:\MyTrustStoreFile。
    4.  在 ADF 中，使用 `EncryptionMethod=1` 和相应的 `TrustStore`/`TrustStorePassword` 值配置 Oracle 连接字符串，例如 `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`。

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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Oracle 数据集支持的属性列表。

若要从/向 OData 复制数据，请将数据集的 type 属性设置为“OracleTable”  。 支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为 OracleTable  。 | 是 |
| tableName |链接服务引用的 Oracle 数据库中表的名称。 | 是 |

**示例：**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Oracle 源和接收器支持的属性列表。

### <a name="oracle-as-a-source-type"></a>以 Oracle 作为源类型

> [!TIP]
>
> 了解详细信息[并行复制从 Oracle](#parallel-copy-from-oracle)部分，了解如何从 Oracle 有效地使用数据分区加载数据。

要从 Oracle 复制数据，请将复制活动中的源类型设置为“OracleSource”  。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 OracleSource  。 | 是 |
| oracleReaderQuery | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。<br>启用分区的负载，需要在查询中挂钩相应内置分区个参数。 请参阅中的示例[并行复制从 Oracle](#parallel-copy-from-oracle)部分。 | 否 |
| partitionOptions | 指定分区选项用于将数据加载从 Oracle 的数据。 <br>允许的值为：**无**（默认值）， **PhysicalPartitionsOfTable**并**DynamicRange**。<br>当启用分区选项 (不 None)，请配置 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 设置复制活动，例如，4，用于确定并行度以并发方式将数据加载从 Oracle数据库。 | 否 |
| partitionSettings | 指定的数据分区的设置的组。 <br>当分区选项不位于应用`None`。 | 否 |
| partitionNames | 需要复制的物理分区的列表。 <br>将应用分区选项时`PhysicalPartitionsOfTable`。 如果使用查询来检索源数据，挂钩`?AdfTabularPartitionName`WHERE 子句中。 中的示例，请参阅[并行复制从 Oracle](#parallel-copy-from-oracle)部分。 | 否 |
| partitionColumnName | 指定源列的名称**整数类型中**，将使用的并行复制分区范围。 如果未指定，表的主键将自动检测到并用作分区列。 <br>将应用分区选项时`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfRangePartitionColumnName`WHERE 子句中。 中的示例，请参阅[并行复制从 Oracle](#parallel-copy-from-oracle)部分。 | 否 |
| partitionUpperBound | 要将数据复制的分区列的最大值。 <br>将应用分区选项时`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfRangePartitionUpbound`WHERE 子句中。 中的示例，请参阅[并行复制从 Oracle](#parallel-copy-from-oracle)部分。 | 否 |
| PartitionLowerBound | 要将数据复制的分区列的最小值。 <br>将应用分区选项时`DynamicRange`。 如果使用查询来检索源数据，挂钩`?AdfRangePartitionLowbound`WHERE 子句中。 中的示例，请参阅[并行复制从 Oracle](#parallel-copy-from-oracle)部分。 | 否 |

**使用没有分区的基本查询的示例： 复制数据**

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

请参阅中的更多示例[并行复制从 Oracle](#parallel-copy-from-oracle)部分。

### <a name="oracle-as-a-sink-type"></a>以 Oracle 作为接收器类型

要向 Oracle 复制数据，请将复制活动中的接收器类型设置为“OracleSink”  。 复制活动 **sink** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为 **OracleSink**。 | 是 |
| writeBatchSize | 缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。<br/>允许的值为 Integer（行数）。 |否（默认值为 10,000） |
| writeBatchTimeout | 超时之前等待批插入操作完成时的等待时间。<br/>允许的值为 Timespan。 示例为 00:30:00（30 分钟）。 | 否 |
| preCopyScript | 指定每次运行时，复制活动将数据写入到 Oracle 之前要执行的 SQL 查询。 可以使用此属性清除预加载的数据。 | 否 |

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

数据工厂的 Oracle 连接器提供了内置的数据分区，以将数据从 Oracle 复制优异的性能与并行。 您可以找到复制活动的数据分区选项-> Oracle 源：

![分区选项](./media/connector-oracle/connector-oracle-partition-options.png)

启用分区的副本时，数据工厂对 Oracle 源将数据加载分区运行并行查询。 配置并通过控制并行度 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 上复制活动设置。 例如，如果您设置`parallelCopies`为 4，数据工厂同时生成，并根据指定的分区选项和设置，Oracle 数据库中的数据的每个检索部分运行四个查询。

建议以启用并行复制数据分区，尤其是当从 Oracle 数据库加载大量数据时使用。 以下是针对不同方案的建议的配置：

| 场景                                                     | 建议的设置                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 从具有物理分区的大型表的完全加载          | **分区选项**:物理分区的表。 <br><br/>在执行期间，数据工厂自动检测物理分区，并通过分区将数据复制。 |
| 从大型表，而无需使用整数列的数据分区的物理分区完全加载 | **分区选项**:动态范围分区。<br>**分区列**:指定用于对数据进行分区的列。 如果未使用指定主键列。 |
| 加载大量的自定义查询，其下方使用物理分区的数据 | **分区选项**:物理分区的表。<br>**查询**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**分区名称**:指定从中复制数据的分区名称。 如果未指定，ADF 将自动检测中 Oracle 数据集指定的表上的物理分区。<br><br>在执行期间，数据工厂替换`?AdfTabularPartitionName`与实际分区名称并发送给 Oracle。 |
| 加载大量数据使用自定义查询，其下方，而无需物理分区，而应用于整数列的数据分区 | **分区选项**:动态范围分区。<br>**查询**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分区列**:指定用于对数据进行分区的列。 可以针对列分区使用整数数据类型。<br>**分区上界**并**分区下限**:指定你想要对分区列，以便只检索范围下限和上限之间的数据进行筛选。<br><br>在执行期间，数据工厂替换`?AdfRangePartitionColumnName`， `?AdfRangePartitionUpbound`，和`?AdfRangePartitionLowbound`使用实际的列名称和值范围为每个分区，并将发送到 Oracle。 <br>例如，如果分区列"ID"设置为 1，上限为 80，为 4，并行复制设置的下限 ADF 检索数据的 4 个分区之间 [1，20]，id 为 [21，40] [41，60] 和 [61，80]。 |

**示例： 使用物理分区进行查询**

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

**示例： 查询与动态范围分区**

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

从/向 Oracle 复制数据时，以下映射用于从 Oracle 数据类型映射到数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| Oracle 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>（仅支持 Oracle 10g 和更高版本） |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String（如果精度 > 28） |
| INTEGER |Decimal, String（如果精度 > 28） |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String（如果精度 > 28） |
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


## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
