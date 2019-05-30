---
title: 从 SAP 表使用 Azure 数据工厂复制数据 |Microsoft Docs
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
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247164"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>从 SAP 表使用 Azure 数据工厂复制数据

本文概述了如何使用 Azure 数据工厂中复制活动从 SAP 表复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

您可以将数据从 SAP 表复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP 表连接器支持：

- 将数据从 SAP 表中复制**7.01 或更高版本与 SAP Business Suite** （在最新 SAP 支持包堆栈在 2015 年之后发布） 或**S/4HANA**。
- 将数据复制从这两个**SAP 透明表**并**视图**。
- 使用来复制数据**基本身份验证**或**SNC** （安全网络通信） 如果 SNC 配置。
- 连接到**应用程序服务器**或**消息服务器**。

## <a name="prerequisites"></a>必备组件

若要使用此 SAP 表连接器，需要：

- 设置自承载集成运行时版本 3.17 或更高版本。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。

- 从 SAP 的网站下载 **64 位 [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** ，将其安装在自承载 IR 计算机上。 请确保您选择安装，在可选安装程序步骤窗口**程序集安装到 GAC**选项。 

    ![安装 SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在数据工厂 SAP 表连接器中使用的 SAP 用户需要具有以下权限： 

    - RFC 的授权。 
    - "执行"活动的授权对象"S_SDSAUTH"权限。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于定义于 SAP 表连接器特定的数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP Business Warehouse Open Hub 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapTable** | 是 |
| server | SAP 实例所驻留的服务器的名称。<br/>如果你想要连接到适用**SAP 应用程序服务器**。 | 否 |
| systemNumber | SAP 系统的系统编号。<br/>如果你想要连接到适用**SAP 应用程序服务器**。<br/>允许值：用字符串表示的两位十进制数。 | 否 |
| messageServer | 将 SAP 消息服务器的主机名。<br/>如果你想要连接到适用**SAP 消息服务器**。 | 否 |
| messageServerService | 服务名称或端口号的消息服务器。<br/>如果你想要连接到适用**SAP 消息服务器**。 | 否 |
| systemId | SystemID 的 SAP 系统表所在的位置。<br/>如果你想要连接到适用**SAP 消息服务器**。 | 否 |
| logonGroup | SAP 系统的登录组中。<br/>如果你想要连接到适用**SAP 消息服务器**。 | 否 |
| clientId | SAP 系统中的客户端的客户端 ID。<br/>允许值：用字符串表示的三位十进制数。 | 是 |
| language | SAP 系统使用的语言。 | 否（默认值为 **EN**）|
| userName | 有权访问 SAP 服务器的用户名。 | 是 |
| password | 用户密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| sncMode | SNC 激活指示器来访问 SAP 服务器表所在的位置。<br/>如果你想要使用 SNC 连接到 SAP 服务器才适用。<br/>允许值包括：**0** （关闭、 默认值） 或**1** （开）。 | 否 |
| sncMyName | 发起方的 SNC 名称来访问 SAP 服务器表所在的位置。<br/>可用`sncMode`上。 | 否 |
| sncPartnerName | 通信合作伙伴的 SNC 名称来访问 SAP 服务器表所在的位置。<br/>可用`sncMode`上。 | 否 |
| sncLibraryPath | 外部安全产品库，用于访问 SAP 服务器表所在的位置。<br/>可用`sncMode`上。 | 否 |
| sncQop | SNC 质量的保护。<br/>可用`sncMode`上。 <br/>允许值包括：**1** （身份验证）， **2** （完整性） **3** （隐私） **8** （默认值）， **9** （最大）。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例 1： 连接到 SAP 应用程序服务器**

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

**示例 2： 连接到 SAP 消息服务器**

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

**示例 3： 连接使用 SNC**

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

若要从 / 向 SAP BW Open Hub 复制数据，支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为**SapTableResource**。 | 是 |
| tableName | 要复制的数据的 SAP 表的名称。 | 是 |

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

若要从 SAP 表复制数据，支持以下属性。

| 属性                         | 说明                                                  | 必选 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Type 属性必须设置为**SapTableSource**。       | 是      |
| rowCount                         | 要检索的行数。                              | 否       |
| rfcTableFields                   | 若要从 SAP 表中复制的字段。 例如，`column0, column1`。 | 否       |
| rfcTableOptions                  | 若要筛选 SAP 表中的行的选项。 例如，`COLUMN0 EQ 'SOMEVALUE'`。 | 否       |
| customRfcReadTableFunctionModule | 可用于从 SAP 表读取数据的自定义 RFC 函数模块。 | 否       |
| partitionOption                  | 若要从 SAP 表中读取分区机制。 受支持的选项包括： <br/>- **无**<br/>- **PartitionOnInt** （正常整数或整数值与在左侧，如 0000012345 零填充）<br/>- **PartitionOnCalendarYear** (4 digits in format "YYYY")<br/>- **PartitionOnCalendarMonth** （采用格式"YYYYMM"的 6 位数）<br/>- **PartitionOnCalendarDate** （格式为"YYYYMMDD"的 8 位） | 否       |
| partitionColumnName              | 要对数据进行分区的列的名称。 | 否       |
| partitionUpperBound              | 中指定的列的最大值`partitionColumnName`，将用于继续下一步分区。 | 否       |
| partitionLowerBound              | 中指定的列的最小值`partitionColumnName`，将用于继续下一步分区。 | 否       |
| maxPartitionsNumber              | 要将数据拆分为分区的数目上限。 | 否       |

>[!TIP]
>- 如果 SAP 表有大量的数据，例如多个为数十亿行，使用`partitionOption`和`partitionSetting`若要将数据拆分为较小的分区，在这种情况下通过分区，每个数据分区读取数据从服务器中检索你 SAP 通过一个单一RFC 调用。<br/>
>- 采取`partitionOption`作为`partitionOnInt`为例，每个分区中的行数计算的 (总行数介于*partitionUpperBound*并*partitionLowerBound*) /*maxPartitionsNumber*。<br/>
>- 如果你想要进一步分区并行运行以提高复制的速度，强烈建议使`maxPartitionsNumber`的值的倍数`parallelCopies`(了解详细信息[并行复制](copy-activity-performance.md#parallel-copy))。

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

复制时数据从 SAP 表，以下映射用于从 SAP 表数据类型到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| C（字符串） | String |
| 我 （整数） | Int32 |
| F（浮点数） | Double |
| D（日期） | String |
| T（时间） | String |
| P（BCD 打包，货币，小数，Qty） | Decimal |
| N （数值） | String |
| X（二进制，原始） | String |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
