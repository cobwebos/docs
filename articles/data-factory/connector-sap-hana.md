---
title: "使用 Azure 数据工厂从 SAP HANA 复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP HANA 复制到支持的接收器数据存储。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 31fce15ab0c3496e4d74e105134c29373a777c18
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP HANA 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-sap-hana-connector.md)
> * [版本 2 - 预览版](connector-sap-hana.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 SAP HANA 数据库复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 SAP HANA 连接器](v1/data-factory-sap-hana-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 SAP HANA 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP HANA 连接器支持：

- 从任何版本的 SAP HANA 数据库复制数据。
- 使用 SQL 查询从 **HANA 信息模型**（如分析和计算视图）和**行/列表**中复制数据。
- 使用**基本**或 **Windows** 身份验证复制数据。

> [!NOTE]
> 要将数据复制到 SAP HANA 数据存储，请使用泛型 ODBC 连接器。 有关详细信息，请参阅 [SAP HANA 接收器](connector-odbc.md#sap-hana-sink)。 注意：适用于 SAP HANA 连接器和 ODBC 连接器的链接服务采用不同的类型，因此不能重用。

## <a name="prerequisites"></a>先决条件

要使用此 SAP HANA 连接器，需要：

- 设置自承载集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。
- 在集成运行时计算机上安装 SAP HANA ODBC 驱动程序。 可以从 [SAP 软件下载中心](https://support.sap.com/swdc)下载 SAP HANA ODBC 驱动程序。 使用关键字 **SAP HANA CLIENT for Windows** 进行搜索。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 SAP HANA 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP HANA 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapHana** | 是 |
| server | SAP HANA 实例所驻留的服务器的名称。 如果服务器使用的是自定义端口，则指定 `server:port`。 | 是 |
| authenticationType | 用于连接 SAP HANA 数据库的身份验证类型。<br/>允许的值为：**Basic** 和 **Windows** | 是 |
| userName | 有权访问 SAP 服务器的用户名。 | 是 |
| password | 用户密码。 将此字段标记为 SecureString。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 SAP HANA 数据集支持的属性列表。

要从 SAP HANA 复制数据，请将数据集的 type 属性设置为“RelationalTable”。 RelationalTable 类型的 SAP HANA 数据集不支持任何类型特定的属性时。

**示例：**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SAP HANA 源支持的属性列表。

### <a name="sap-hana-as-source"></a>以 SAP HANA 作为源

要从 SAP HANA 复制数据，请将复制活动中的源类型设置为“RelationalSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：RelationalSource | 是 |
| query | 指定要从 SAP HANA 实例读取数据的 SQL 查询。 | 是 |

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
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA 的数据类型映射

从 SAP HANA 复制数据时，以下映射用于从 SAP HANA 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP HANA 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| ALPHANUM | String |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | DateTime |
| DECIMAL | 小数 |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | String |
| REAL | Single |
| SECONDDATE | DateTime |
| SMALLINT | Int16 |
| TIME | TimeSpan |
| TIMESTAMP | DateTime |
| TINYINT | Byte |
| VARCHAR | String |

## <a name="known-limitations"></a>已知限制

从 SAP HANA 复制数据时，有以下几个已知限制：

- NVARCHAR 字符串截断为最大长度为 4000 的 Unicode 字符
- 不支持 SMALLDECIMAL
- 不支持 VARBINARY
- 有效日期在 1899/12/30 和 9999/12/31 之间


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。