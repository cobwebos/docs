---
title: 使用 Azure 数据工厂从 SAP 表复制数据 |Microsoft Docs
description: 了解如何将数据从 SAP 表复制到支持的接收器数据存储，在 Azure 数据工厂管道中使用复制活动。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827754"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP 表复制数据

本文概述了如何使用 Azure 数据工厂中复制活动从 SAP 表复制数据。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支持的功能

您可以将数据从 SAP 表复制到任何支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP 表连接器支持：

- 从 SAP 表中的复制数据：

  - SAP ERP 中心组件 (SAP ECC) 版本 7.01 或更高版本 （在最新 SAP 支持包堆栈在 2015年之后发布）。
  - SAP Business Warehouse (SAP BW) 7.01 或更高版本。
  - SAP S/4HANA。
  - 在 SAP Business Suite 7.01 或更高版本中的其他产品。

- 从 SAP 透明表、 共用的表、 聚集的表和视图复制数据。
- 如果配置 SNC 使用基本身份验证或安全网络通信 (SNC) 复制数据。
- 连接到 SAP 应用程序服务器或 SAP 消息服务器。

## <a name="prerequisites"></a>系统必备

若要使用此 SAP 表连接器，需要：

- 设置自承载的集成运行时 （3.17 或更高版本）。 有关详细信息，请参阅[创建和配置自承载的集成运行时](create-self-hosted-integration-runtime.md)。

- 下载 64 位[SAP Connector for Microsoft.NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)从 SAP 的网站，并将其安装自承载的集成运行时计算机上。 在安装期间，请确保选择**程序集安装到 GAC**选项**可选设置步骤**窗口。

  ![安装用于.NET 的 SAP 连接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在数据工厂 SAP 表连接器中正在使用的 SAP 用户必须具有以下权限：

  - 使用远程函数调用 (RFC) 目标的授权。
  - 执行活动的 S_SDSAUTH 授权对象的权限。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于向 SAP 表连接器定义特定的数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP BW 开放中心链接服务支持以下属性：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `type` | `type`属性必须设置为`SapTable`。 | 是 |
| `server` | SAP 实例所在的服务器的名称。<br/>用于连接到 SAP 应用程序服务器。 | 否 |
| `systemNumber` | SAP 系统的系统数。<br/>用于连接到 SAP 应用程序服务器。<br/>允许的值：两位十进制数表示为字符串。 | 否 |
| `messageServer` | 将 SAP 消息服务器主机名。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `messageServerService` | 服务名称或端口号的消息服务器。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `systemId` | SAP 系统表所在的位置的 ID。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `logonGroup` | SAP 系统的登录组中。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `clientId` | SAP 系统中的客户端 ID。<br/>允许的值：三位十进制数表示为字符串。 | 是 |
| `language` | SAP 系统使用的语言。<br/>默认值为 `EN`。| 否 |
| `userName` | 有权访问 SAP 服务器的用户的名称。 | 是 |
| `password` | 用户的密码。 将使用此字段标记`SecureString`类型，以将其安全地存储在数据工厂，或[引用 Azure Key Vault 中存储的机密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | SNC 激活指示符来访问 SAP 服务器表所在的位置。<br/>如果你想要使用 SNC 连接到 SAP 服务器，使用。<br/>允许的值为`0`（关闭、 默认值） 或`1`（开）。 | 否 |
| `sncMyName` | 发起方的 SNC 名称来访问 SAP 服务器表所在的位置。<br/>适用时`sncMode`上。 | 否 |
| `sncPartnerName` | 在通信合作伙伴的 SNC 名称来访问 SAP 服务器表所在的位置。<br/>适用时`sncMode`上。 | 否 |
| `sncLibraryPath` | 外部安全产品库，用于访问 SAP 服务器表所在的位置。<br/>适用时`sncMode`上。 | 否 |
| `sncQop` | 要应用的 SNC 高质量的保护级别。<br/>适用时`sncMode`上。 <br/>允许的值为`1`（身份验证）， `2` （完整性） `3` （隐私） `8` （默认值）， `9` （最大）。 | 否 |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 自承载的集成运行时是必需的如中所述[先决条件](#prerequisites)。 |是 |

**示例 1：连接到 SAP 应用程序服务器**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>示例 2：连接到 SAP 消息服务器

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

### <a name="example-3-connect-by-using-snc"></a>示例 3：使用 SNC 连接

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

有关的各部分和用于定义数据集的属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供 SAP 表数据集支持的属性的列表。

若要从 / 向 SAP BW 开放中心链接服务，请复制数据，支持以下属性：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `type` | `type`属性必须设置为`SapTableResource`。 | 是 |
| `tableName` | 要复制的数据的 SAP 表的名称。 | 是 |

### <a name="example"></a>示例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关的各部分和用于定义活动的属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP 表源支持的属性的列表。

### <a name="sap-table-as-a-source"></a>SAP 表作为源

若要从 SAP 表复制数据，支持以下属性：

| 属性                         | 说明                                                  | 必填 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type`属性必须设置为`SapTableSource`。         | 是      |
| `rowCount`                         | 要检索的行数。                              | 否       |
| `rfcTableFields`                   | 字段 （列） 将从 SAP 表复制。 例如， `column0, column1` 。 | 否       |
| `rfcTableOptions`                  | 用于筛选 SAP 表中的行的选项。 例如， `COLUMN0 EQ 'SOMEVALUE'` 。 另请参阅本文后面的 SAP 查询运算符表。 | 否       |
| `customRfcReadTableFunctionModule` | 自定义 RFC 函数模块，它可用于从 SAP 表读取数据。<br>自定义的 RFC 函数模块可用于定义如何从 SAP 系统中检索数据并返回到数据工厂。 自定义函数模块必须具有一个类似的接口实现 （导入、 导出、 表） `/SAPDS/RFC_READ_TABLE2`，这是数据工厂使用的默认接口。 | 否       |
| `partitionOption`                  | 若要从 SAP 表中读取分区机制。 支持的选项包括： <ul><li>`None`</li><li>`PartitionOnInt` (正常整数或整数值与零填充左侧，如`0000012345`)</li><li>`PartitionOnCalendarYear` （"YYYY"格式 4 位数字）</li><li>`PartitionOnCalendarMonth` （采用格式"YYYYMM"的 6 位数）</li><li>`PartitionOnCalendarDate` （"YYYYMMDD"格式的 8 位）</li></ul> | 否       |
| `partitionColumnName`              | 用于对数据进行分区的列的名称。                | 否       |
| `partitionUpperBound`              | 中指定的列的最大值`partitionColumnName`将用于继续进行分区。 | 否       |
| `partitionLowerBound`              | 中指定的列的最小值`partitionColumnName`将用于继续进行分区。 | 否       |
| `maxPartitionsNumber`              | 要将数据拆分成的最大分区数。     | 否       |

>[!TIP]
>如果您的 SAP 表具有大量数据，例如多个的 100 亿行，使用`partitionOption`和`partitionSetting`若要将数据拆分为较小的分区。 在这种情况下，每个分区，读取数据，并从 SAP 服务器通过单个 RFC 调用检索每个数据分区。<br/>
<br/>
>采取`partitionOption`作为`partitionOnInt`为例，每个分区中的行数使用以下公式计算: (总行数介于`partitionUpperBound`并`partitionLowerBound`) /`maxPartitionsNumber`。<br/>
<br/>
>若要运行分区并行复制提高速度，我们强烈建议制定`maxPartitionsNumber`的值的倍数`parallelCopies`属性。 有关详细信息，请参阅[并行复制](copy-activity-performance.md#parallel-copy)。

在`rfcTableOptions`，可以使用以下常见 SAP 查询运算符来筛选的行：

| 运算符 | 描述 |
| :------- | :------- |
| `EQ` | 等于 |
| `NE` | 不等于 |
| `LT` | 小于 |
| `LE` | 小于等于 |
| `GT` | 大于 |
| `GE` | 大于或等于 |
| `LIKE` | 如 `LIKE 'Emma%'` |

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
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 表的数据类型映射

当从 SAP 表复制数据时，以下映射用来从 SAP 表数据类型为 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `C` （字符串） | `String` |
| `I` （整数） | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` （时间） | `String` |
| `P` （BCD 打包，货币、 Decimal、 Qty） | `Decimal` |
| `N` （数值） | `String` |
| `X` （二进制和原始） | `String` |

## <a name="next-steps"></a>后续步骤

Azure 数据工厂中的复制活动支持作为源和接收器数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
