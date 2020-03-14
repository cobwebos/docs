---
title: 从 SAP 表复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP 表复制到支持的接收器数据存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: fd363f7b685db5e309827a0c5e635264e676b388
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281751"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP 表复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从 SAP 表复制数据。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

>[!TIP]
>若要了解 ADF 全面支持 SAP 数据集成方案，请参阅[使用 Azure 数据工厂的 SAP 数据集成白皮书](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，并提供详细的简介、comparsion 和指南。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SAP 表连接器：

- [复制活动](copy-activity-overview.md)与[支持的源/接收器矩阵](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 SAP 表复制到任何支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP 表连接器支持：

- 从以下位置的 SAP 表复制数据：

  - SAP ERP 中心组件（SAP ECC）版本7.01 或更高版本（在2015之后发布的最新 SAP 支持包堆栈中）。
  - SAP Business 仓库（SAP BW）版本7.01 或更高版本（在2015之后发布的最新 SAP 支持包堆栈中）。
  - SAP S/4HANA。
  - SAP Business Suite 版本7.01 或更高版本中的其他产品（在2015之后发布的最新 SAP 支持包堆栈中）。

- 从 SAP 透明表、池表、聚集表和视图复制数据。
- 如果配置了 SNC，请使用基本身份验证或安全网络通信（SNC）复制数据。
- 连接到 SAP 应用程序服务器或 SAP 消息服务器。

## <a name="prerequisites"></a>必备条件

若要使用此 SAP 表连接器，需执行以下操作：

- 设置自承载集成运行时（3.17 版或更高版本）。 有关详细信息，请参阅[创建和配置自承载集成运行时](create-self-hosted-integration-runtime.md)。

- 从 SAP 的网站下载[适用于 Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)的64位 SAP 连接器，并将其安装在自承载集成运行时计算机上。 在安装过程中，请确保在 "**可选安装步骤**" 窗口中选择 "将**程序集安装到 GAC** " 选项。

  ![安装适用于 .NET 的 SAP 连接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 数据工厂 SAP 表连接器中使用的 SAP 用户必须具有以下权限：

  - 授权使用远程函数调用（RFC）目标。
  - 对 S_SDSAUTH 授权对象的执行活动的权限。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SAP 表连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP BW 开放式中心链接服务支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapTable`。 | 是 |
| `server` | SAP 实例所在的服务器的名称。<br/>使用连接到 SAP 应用程序服务器。 | 否 |
| `systemNumber` | SAP 系统的系统编号。<br/>使用连接到 SAP 应用程序服务器。<br/>允许的值：用字符串表示的两位十进制数。 | 否 |
| `messageServer` | SAP 消息服务器的主机名。<br/>使用连接到 SAP 消息服务器。 | 否 |
| `messageServerService` | 消息服务器的服务名称或端口号。<br/>使用连接到 SAP 消息服务器。 | 否 |
| `systemId` | 表所在的 SAP 系统的 ID。<br/>使用连接到 SAP 消息服务器。 | 否 |
| `logonGroup` | SAP 系统的登录组。<br/>使用连接到 SAP 消息服务器。 | 否 |
| `clientId` | SAP 系统中的客户端 ID。<br/>允许的值：用字符串表示的三位十进制数。 | 是 |
| `language` | SAP 系统使用的语言。<br/>默认值为 `EN`。| 否 |
| `userName` | 有权访问 SAP 服务器的用户的名称。 | 是 |
| `password` | 用户的密码。 将此字段标记为 `SecureString` 类型，以将其安全地存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | 用于访问表所在的 SAP 服务器的 SNC 激活指示器。<br/>如果要使用 SNC 连接到 SAP 服务器，请使用。<br/>允许的值为 `0` （off、default）或 `1` （on）。 | 否 |
| `sncMyName` | 发起程序的 SNC 名称，用于访问表所在的 SAP 服务器。<br/>当 `sncMode` 为 on 时应用。 | 否 |
| `sncPartnerName` | 用于访问表所在 SAP 服务器的通信伙伴的 SNC 名称。<br/>当 `sncMode` 为 on 时应用。 | 否 |
| `sncLibraryPath` | 外部安全产品的库，用于访问表所在的 SAP 服务器。<br/>当 `sncMode` 为 on 时应用。 | 否 |
| `sncQop` | 要应用的保护级别的 SNC 质量。<br/>当 `sncMode` 为 On 时应用。 <br/>允许的值为 `1` （身份验证）、`2` （完整性）、`3` （隐私）、`8` （默认）、`9` （最大值）。 | 否 |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 需要自承载集成运行时，如[先决条件](#prerequisites)中所述。 |是 |

**示例1：连接到 SAP 应用程序服务器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>示例2：连接到 SAP 消息服务器

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>示例3：使用 SNC 进行连接

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关用于定义数据集的各节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供 SAP 表数据集支持的属性列表。

若要将数据从和复制到 SAP BW 打开中心链接服务，支持以下属性：

| properties | 说明 | 必选 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapTableResource`。 | 是 |
| `tableName` | 要从中复制数据的 SAP 表的名称。 | 是 |

### <a name="example"></a>示例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP 表源支持的属性列表。

### <a name="sap-table-as-source"></a>SAP 表作为源

若要从 SAP 表中复制数据，支持以下属性：

| properties                         | 说明                                                  | 必选 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` 属性必须设置为 `SapTableSource`。         | 是      |
| `rowCount`                         | 要检索的行数。                              | 否       |
| `rfcTableFields`                   | 要从 SAP 表复制的字段（列）。 例如，`column0, column1` 。 | 否       |
| `rfcTableOptions`                  | 用于筛选 SAP 表中的行的选项。 例如，`COLUMN0 EQ 'SOMEVALUE'` 。 另请参阅本文后面的 SAP 查询运算符表。 | 否       |
| `customRfcReadTableFunctionModule` | 自定义 RFC 函数模块，可用于从 SAP 表中读取数据。<br>你可以使用自定义 RFC 函数模块来定义从 SAP 系统检索数据并将数据返回到数据工厂的方式。 自定义函数模块必须具有实现的接口（导入、导出、表），这类似于 `/SAPDS/RFC_READ_TABLE2`，这是数据工厂使用的默认接口。 | 否       |
| `partitionOption`                  | 要从 SAP 表中读取的分区机制。 支持的选项包括： <ul><li>`None`</li><li>`PartitionOnInt` （左侧为零的整数或整数值，如 `0000012345`）</li><li>`PartitionOnCalendarYear` （格式为 "YYYY" 的4位数字）</li><li>`PartitionOnCalendarMonth` （格式为 "YYYYMM" 的6位数字）</li><li>`PartitionOnCalendarDate` （格式为 "YYYYMMDD" 的8位数字）</li></ul> | 否       |
| `partitionColumnName`              | 用于对数据进行分区的列的名称。                | 否       |
| `partitionUpperBound`              | `partitionColumnName` 中指定的列的最大值，将用于继续进行分区。 | 否       |
| `partitionLowerBound`              | `partitionColumnName` 中指定的列的最小值，将用于继续分区。 | 否       |
| `maxPartitionsNumber`              | 要将数据拆分到的分区的最大数目。     | 否       |

>[!TIP]
>如果 SAP 表有大量数据（如几亿行），请使用 `partitionOption` 和 `partitionSetting` 将数据拆分成较小的分区。 在这种情况下，将按分区读取数据，并通过单个 RFC 调用从 SAP 服务器检索每个数据分区。<br/>
<br/>
>采用 `partitionOption` `partitionOnInt` 作为示例，使用以下公式计算每个分区中的行数：（`partitionUpperBound` 和 `partitionLowerBound`之间的总行数）/`maxPartitionsNumber`。<br/>
<br/>
>若要并行加载数据分区以提高副本速度，并行度由复制活动的[`parallelCopies`](copy-activity-performance.md#parallel-copy)设置控制。 例如，如果将 `parallelCopies` 设置为4，则数据工厂会根据指定的分区选项和设置同时生成并运行四个查询，每个查询将从 SAP 表中检索部分数据。 强烈建议 `maxPartitionsNumber` `parallelCopies` 属性值的倍数。 将数据复制到基于文件的数据存储时，还 recommanded 将文件夹作为多个文件写入（仅指定文件夹名称），在这种情况下，性能比写入单个文件更好。

在 `rfcTableOptions`中，可以使用以下常见 SAP 查询运算符来筛选行：

| 操作员 | 说明 |
| :------- | :------- |
| `EQ` | 等于 |
| `NE` | 不等于 |
| `LT` | 小于 |
| `LE` | 小于或等于 |
| `GT` | 大于 |
| `GE` | 大于或等于 |
| `LIKE` | 与 `LIKE 'Emma%'` |

### <a name="example"></a>示例

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 表的数据类型映射

从 SAP 表复制数据时，以下映射用于从 SAP 表数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `C` （字符串） | `String` |
| `I` （Integer） | `Int32` |
| `F` （Float） | `Double` |
| `D` （Date） | `String` |
| `T` （时间） | `String` |
| `P` （已打包 BCD、货币、小数、数量） | `Decimal` |
| `N` （数值） | `String` |
| `X` （二进制和原始） | `String` |

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中的复制活动支持作为源和接收器的数据存储列表，请参阅支持的[数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
