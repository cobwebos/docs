---
title: 使用 Azure 数据工厂从 SAP ECC 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP ECC 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: jingwang
ms.openlocfilehash: f9f6d2e43fff9a3e57145f39863f66eed64869b2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048577"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP ECC 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 SAP ECC (SAP Enterprise Central Component) 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 SAP ECC 复制到任何受支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP ECC 连接器支持：

- 在 SAP NetWeaver 版本 7.0 和更高版本上从 SAP ECC 复制数据。 
- 复制由 SAP ECC OData 服务（例如 SAP 表/视图、BAPI、数据提取器，等等）公开的任何对象中的数据，或复制发送到 SAP PI 的可以通过相关适配器接收为 OData 的数据/IDOC。
- 使用基本身份验证复制数据。

## <a name="prerequisites"></a>先决条件

通常，SAP ECC 通过 SAP 网关经由 OData 服务来公开实体。 若要使用此 SAP ECC 连接器，需要：

- **设置 SAP 网关**。 对于采用高于 7.4 版的 SAP NetWeaver 版本的服务器，SAP 网关已安装。 否则，需要在通过 OData 服务公开 SAP ECC 数据之前安装嵌入式网关或网关中心。 在[安装指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)中了解如何设置 SAP 网关。

- **激活并配置 SAP OData 服务**。 可以通过 TCODE SICF 在数秒内激活 OData 服务。 还可以配置需要公开哪些对象。 下面是一个示例[分步指南](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="getting-started"></a>入门

可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 SAP ECC 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP ECC 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**SapEcc** | 是 |
| url | SAP ECC OData 服务的 URL。 | 是 |
| username | 用于连接到 SAP ECC 的用户名。 | 否 |
| password | 用于连接到 SAP ECC 的明文密码。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供了 SAP ECC 数据集支持的属性列表。

若要从 SAP ECC 复制数据，请将数据集的 type 属性设置为 **SapEccResource**。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| 路径 | SAP ECC OData 实体的路径。 | 是 |

**示例**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 SAP ECC 源支持的属性列表。

### <a name="sap-ecc-as-source"></a>以 SAP ECC 作为源

若要从 SAP ECC 复制数据，请将复制活动中的源类型设置为 **SapEccSource**。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**SapEccSource** | 是 |
| query | 用于筛选数据的 OData 查询选项。 示例："$select=Name,Description&$top=10"。<br/><br/>SAP ECC 连接器复制以下组合 URL 中的数据：（在链接服务中指定的 RUL）/（在数据集中指定的路径）?（在复制活动源中指定的查询）。 请参阅 [OData URL 组件](http://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>SAP ECC 的数据类型映射

从 SAP ECC 复制数据时，可以使用以下映射将 SAP ECC 数据的 OData 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| OData 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |:--- |
| Edm.Binary | String |
| Edm.Boolean | Bool |
| Edm.Byte | String |
| Edm.DateTime | DateTime |
| Edm.Decimal | 小数 |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | String |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 目前不支持复杂数据类型。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
