---
title: 使用 Azure 数据工厂从 SAP 表复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP 表复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2018
ms.author: jingwang
ms.openlocfilehash: 49f07b4aaadfd45e9743bde58dc715230e5bc983
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074061"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP 表复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从 SAP 表复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 SAP 表复制到任何受支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP 表连接器支持：

- 从 SAP 表中的复制数据：

    - **SAP ECC**版本 7.01 或更高版本 （在最新 SAP 支持包堆栈在 2015 年之后发布）
    - **SAP BW** 7.01 或更高版本
    - **SAP S/4HANA**
    - **在 SAP Business Suite 中的其他产品**7.01 或更高版本 

- 从 **SAP 透明表**和**视图**复制数据。
- 使用**基本身份验证**或 **SNC**（安全网络通信）（如果已配置 SNC）复制数据。
- 连接到**应用程序服务器**或**消息服务器**。

## <a name="prerequisites"></a>必备组件

若要使用此 SAP 表连接器，需要：

- 设置 3.17 或更高版本的自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。

- 从 SAP 的网站下载 **64 位 [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** ，将其安装在自承载 IR 计算机上。 安装时，请在可选的安装步骤窗口中确保选择“将程序集安装到 GAC”选项。  

    ![安装 SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在数据工厂 SAP 表连接器中使用的 SAP 用户需要有以下权限： 

    - RFC 的授权。 
    - “执行”授权对象“S_SDSAUTH”的活动的权限。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SAP 表连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP Business Warehouse Open Hub 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapTable** | 是 |
| server | SAP 实例所在的服务器的名称。<br/>连接到 **SAP 应用程序服务器**时适用。 | 否 |
| systemNumber | SAP 系统的系统编号。<br/>连接到 **SAP 应用程序服务器**时适用。<br/>允许值：用字符串表示的两位十进制数。 | 否 |
| messageServer | SAP 消息服务器的主机名。<br/>连接到 **SAP 消息服务器**时适用。 | 否 |
| messageServerService | 消息服务器的服务名称或端口号。<br/>连接到 **SAP 消息服务器**时适用。 | 否 |
| systemId | 表所在的 SAP 系统的 SystemID。<br/>连接到 **SAP 消息服务器**时适用。 | 否 |
| logonGroup | SAP 系统的登录组。<br/>连接到 **SAP 消息服务器**时适用。 | 否 |
| clientId | SAP 系统中客户端的客户端 ID。<br/>允许值：用字符串表示的三位十进制数。 | 是 |
| language | SAP 系统使用的语言。 | 否（默认值为 **EN**）|
| userName | 有权访问 SAP 服务器的用户名。 | 是 |
| password | 用户密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| sncMode | 用于访问表所在的 SAP 服务器的 SNC 激活指示符。<br/>使用 SNC 连接到 SAP 服务器时适用。<br/>允许值包括：**0**（关闭，默认值）或 **1**（打开）。 | 否 |
| sncMyName | 用于访问表所在的 SAP 服务器的发起方 SNC 名称。<br/>当 `sncMode` 为 On（打开）时适用。 | 否 |
| sncPartnerName | 用于访问表所在的 SAP 服务器的通信合作伙伴 SNC 名称。<br/>当 `sncMode` 为 On（打开）时适用。 | 否 |
| sncLibraryPath | 用于访问表所在的 SAP 服务器的外部安全产品库。<br/>当 `sncMode` 为 On（打开）时适用。 | 否 |
| sncQop | SNC 保护质量。<br/>当 `sncMode` 为 On（打开）时适用。 <br/>允许值包括：**1**（身份验证）、**2**（完整性）、**3**（隐私）、**8**（默认值）、**9**（最大值）。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例 1：连接到 SAP 应用程序服务器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**示例 2：连接到 SAP 消息服务器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**示例 3：使用 SNC 进行连接**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 SAP 表数据集支持的属性列表。

支持使用以下属性从/向 SAP BW Open Hub 复制数据。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **SapTableResource**。 | 是 |
| tableName | 要从中复制数据的 SAP 表的名称。 | 是 |

**示例：**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SAP 表源支持的属性列表。

### <a name="sap-table-as-source"></a>SAP 表作为源

支持使用以下属性从 SAP 表复制数据。

| 属性                         | 说明                                                  | 必选 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | type 属性必须设置为 **SapTableSource**。       | 是      |
| rowCount                         | 要检索的行数。                              | 否       |
| rfcTableFields                   | 要从 SAP 表中复制的字段。 例如，`column0, column1`。 | 否       |
| rfcTableOptions                  | 用于筛选 SAP 表中的行的选项。 例如，`COLUMN0 EQ 'SOMEVALUE'`。 请参阅此表下面的详细说明。 | 否       |
| customRfcReadTableFunctionModule | 可用于从 SAP 表读取数据的自定义 RFC 函数模块。 | 否       |
| partitionOption                  | 要从 SAP 表中读取的分区机制。 支持的选项包括： <br/>- **None**<br/>- **PartitionOnInt**（在左侧用零填充正常整数或整数值，例如 0000012345）<br/>- **PartitionOnCalendarYear**（采用“YYYY”格式的 4 位数）<br/>- **PartitionOnCalendarMonth**（采用“YYYYMM”格式的 6 位数）<br/>- **PartitionOnCalendarDate**（采用“YYYYMMDD”格式的 8 位数） | 否       |
| partitionColumnName              | 要将其中的数据分区的列的名称。 | 否       |
| partitionUpperBound              | `partitionColumnName` 中指定的最大列值，用于继续处理分区。 | 否       |
| partitionLowerBound              | `partitionColumnName` 中指定的最小列值，用于继续处理分区。 | 否       |
| maxPartitionsNumber              | 要将数据拆分成的最大分区数。 | 否       |

>[!TIP]
>- 如果 SAP 表包含大量数据（例如几十亿行），请使用 `partitionOption` 和 `partitionSetting` 将数据拆分成小分区，在这种情况下，将按分区读取数据，并通过单个 RFC 调用从 SAP 服务器检索每个数据分区。<br/>
>- 以 `partitionOption` 和 `partitionOnInt` 为例，每个分区中的行数的计算公式为：(处于 *partitionUpperBound* 与 *partitionLowerBound* 之间的总行数)/*maxPartitionsNumber*。<br/>
>- 若要进一步并行运行分区以加速复制，我们强烈建议将 `maxPartitionsNumber` 设为 `parallelCopies` 值的倍数（详细了解[并行复制](copy-activity-performance.md#parallel-copy)）。

在`rfcTableOptions`，可以使用例如以下常见 SAP 查询运算符来筛选的行： 

| 运算符 | 描述 |
| :------- | :------- |
| EQ | 等于 |
| NE | 不等于 |
| LT | 小于 |
| LE | 小于等于 |
| GT | 大于 |
| GE | 大于或等于 |
| 类似于 | LIKE 'Emma %' 如下所示： |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>SAP 表的数据类型映射

从 SAP 表复制数据时，以下映射用于从 SAP 表数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| C（字符串） | String |
| I（整数） | Int32 |
| F（浮点数） | Double |
| D（日期） | String |
| T（时间） | String |
| P（BCD 打包，货币，小数，Qty） | Decimal |
| N（数字） | String |
| X（二进制，原始） | String |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
