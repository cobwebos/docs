---
title: "使用 Azure 数据工厂从 SAP BW 复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP Business Warehouse 复制到支持的接收器数据存储。"
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
ms.openlocfilehash: 03841e08d071eb5f846b6a3a9e5a08edaa895611
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP Business Warehouse 复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-sap-business-warehouse-connector.md)
> * [版本 2 - 预览版](connector-sap-business-warehouse.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 SAP Business Warehouse (BW) 复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的 SAP BW 连接器](v1/data-factory-sap-business-warehouse-connector.md)。

## <a name="supported-scenarios"></a>支持的方案

可以将数据从 SAP Business Warehouse 复制到任何受支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP Business Warehouse 连接器支持：

- SAP Business Warehouse 版本 7.x。
- 使用 MDX 查询从 InfoCubes 和 QueryCubes（包括 BEx 查询）复制数据。
- 使用基本身份验证复制数据。

## <a name="prerequisites"></a>先决条件

若要使用此 SAP Business Warehouse 连接器，需要：

- 设置自我托管集成运行时。 有关详细信息，请参阅[自我托管集成运行时](create-self-hosted-integration-runtime.md)一文。
- 在集成运行时计算机上安装 SAP NetWeaver 库。 可以从 SAP 管理员处或直接从 [SAP 软件下载中心](https://support.sap.com/swdc)获取 SAP Netweaver 库。 搜索“SAP Note #1025361”获取最新版本的下载位置。 请确保选取与集成运行时安装匹配的 64 位 SAP NetWeaver 库。 然后，按照 SAP 说明安装 SAP NetWeaver RFC SDK 中包含的所有文件。 SAP NetWeaver 库也包括在 SAP 客户端工具安装中。

> [!TIP]
> 将从 NetWeaver RFC SDK 中提取的 dll 放入 system32 文件夹。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 SAP Business Warehouse 连接器的数据工厂实体，以下部分提供了有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP Business Warehouse (BW) 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：SapBw | 是 |
| server | SAP BW 实例所驻留的服务器的名称。 | 是 |
| systemNumber | SAP BW 系统的系统编号。<br/>允许值：用字符串表示的两位十进制数。 | 是 |
| clientId | SAP W 系统中的客户端的客户端 ID。<br/>允许值：用字符串表示的三位十进制数。 | 是 |
| userName | 有权访问 SAP 服务器的用户名。 | 是 |
| password | 用户密码。 将此字段标记为 SecureString。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例：**

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
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

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 SAP BW 数据集支持的属性列表。

要从 SAP BW 复制数据，请将数据集的 type 属性设置为“RelationalTable”。 RelationalTable 类型的 SAP BW 数据集不支持任何类型特定的属性。

**示例：**

```json
{
    "name": "SAPBWDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 SAP BW 源支持的属性列表。

### <a name="sap-bw-as-source"></a>SAP BW 作为源

要从 SAP BW 复制数据，请将复制活动中的源类型设置为“RelationalSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：RelationalSource | 是 |
| query | 指定要从 SAP BW 实例读取数据的 MDX 查询。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW 的数据类型映射

从 SAP BW 复制数据时，以下映射用于从 SAP BW 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP BW 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | 小数 |
| CUKY | String |
| DEC | 小数 |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | 小数 |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| 单位 | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。