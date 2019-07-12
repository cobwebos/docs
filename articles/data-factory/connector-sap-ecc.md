---
title: 使用 Azure 数据工厂从 SAP ECC 复制数据 |Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP ECC 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827772"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP ECC 复制数据

本文概述了如何在 Azure 数据工厂中使用复制活动将数据从 SAP Enterprise Central Component (ECC)。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 SAP ECC 复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP ECC 连接器支持：

- 从 SAP NetWeaver 版本 7.0 和更高版本上的 SAP ECC 复制数据。
- 将数据从 SAP ECC OData 服务，例如公开的任何对象的复制：

  - SAP 表或视图。
  - 业务应用程序编程接口 [BAPI] 对象。
  - 数据提取器。
  - 数据或发送到 SAP 进程集成 (PI)，可以通过相关适配器接收为 OData 的中间文档 (Idoc)。

- 使用基本身份验证复制数据。

>[!TIP]
>若要通过 SAP 表或视图从 SAP ECC 复制数据，请使用[SAP 表](connector-sap-table.md)连接器，这是更快、 更具可伸缩性。

## <a name="prerequisites"></a>先决条件

通常，SAP ECC 通过 SAP 网关经由 OData 服务来公开实体。 若要使用此 SAP ECC 连接器，需要：

- **设置 SAP 网关**。 对于服务器使用的 SAP NetWeaver 版本高于 7.4，SAP 网关已安装。 对于早期版本，必须公开 SAP ECC 数据通过 OData 服务之前安装嵌入式的 SAP 网关或 SAP 网关中心系统。 若要设置 SAP 网关，请参阅[安装指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **激活并配置 SAP OData 服务**。 可以在数秒内激活通过 TCODE SICF OData 服务。 此外可以配置哪些对象需要公开。 有关详细信息，请参阅[分步指南](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分提供有关用于向 SAP ECC 连接器定义特定的数据工厂实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP ECC 链接服务支持以下属性：

| 属性 | 说明 | 需要 |
|:--- |:--- |:--- |
| `type` | `type`属性必须设置为`SapEcc`。 | 是 |
| `url` | SAP ECC OData 服务的 URL。 | 是 |
| `username` | 用于连接到 SAP ECC 的用户名。 | 否 |
| `password` | 用于连接到 SAP ECC 的明文密码。 | 否 |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 （如果数据存储区是可公开访问），可以使用自承载的集成运行时或 Azure 集成运行时。 如果未指定运行时，`connectVia`使用默认 Azure 集成运行时。 | 否 |

### <a name="example"></a>示例

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关各节和属性可用于定义数据集的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供了 SAP ECC 数据集支持的属性的列表。

若要从 SAP ECC 复制数据，设置`type`到数据集属性`SapEccResource`。

支持以下属性：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData 实体的路径。 | 是 |

### <a name="example"></a>示例

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关各节和属性可用于定义活动的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供了 SAP ECC 源支持的属性的列表。

### <a name="sap-ecc-as-a-source"></a>SAP ECC 作为源

若要从 SAP ECC 复制数据，设置`type`中的属性`source`部分中将复制活动的`SapEccSource`。

复制活动中支持以下属性`source`部分：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `type` | `type`的复制活动的属性`source`部分必须设置为`SapEccSource`。 | 是 |
| `query` | 要筛选的数据的 OData 查询选项。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 连接器将数据从组合 URL 复制：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>有关详细信息，请参阅 [OData URL 组件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

### <a name="example"></a>示例

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

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC 的数据类型映射

当从 SAP ECC 复制数据时，以下映射用于从 OData 数据类型转换 SAP ECC 数据到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| OData 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 目前不支持复杂数据类型。

## <a name="next-steps"></a>后续步骤

Azure 数据工厂中的复制活动支持作为源和接收器数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
