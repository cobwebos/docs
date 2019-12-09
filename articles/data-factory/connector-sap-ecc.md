---
title: 从 SAP ECC 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP ECC 复制到支持的接收器数据存储。
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
ms.openlocfilehash: f875d8f4603a8f51b8b8fed2438e6f3a30c87aeb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931175"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP ECC 复制数据

本文概述如何使用 Azure 数据工厂中的复制活动从 SAP Enterprise Central Component （ECC）复制数据。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

>[!TIP]
>若要了解 ADF 全面支持 SAP 数据集成方案，请参阅[使用 Azure 数据工厂的 SAP 数据集成白皮书](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，并提供详细的简介、comparsion 和指南。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SAP ECC 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 SAP ECC 复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP ECC 连接器支持：

- 在 SAP NetWeaver 版本7.0 及更高版本上从 SAP ECC 复制数据。
- 从 SAP ECC OData 服务公开的任何对象复制数据，例如：

  - SAP 表或视图。
  - 业务应用程序编程接口 [BAPI] 对象。
  - 数据提取器。
  - 发送到 SAP 进程集成（PI）的数据或中间文档（Idoc），可以通过相对适配器以 OData 的形式接收。

- 使用基本身份验证复制数据。

>[!TIP]
>若要通过 SAP 表或视图从 SAP ECC 复制数据，请使用[sap 表](connector-sap-table.md)连接器，此连接器的速度更快且更具可伸缩性。

## <a name="prerequisites"></a>必备组件

通常，SAP ECC 通过 SAP 网关经由 OData 服务来公开实体。 若要使用此 SAP ECC 连接器，需要：

- **设置 SAP 网关**。 对于 SAP NetWeaver 版本低于7.4 的服务器，已安装 SAP 网关。 对于早期版本，必须先安装 embedded SAP 网关或 SAP 网关集线器系统，然后才能通过 OData 服务公开 SAP ECC 数据。 若要设置 SAP 网关，请参阅[安装指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **激活和配置 SAP OData 服务**。 可以通过 TCODE SICF （秒）激活 OData 服务。 你还可以配置需要公开的对象。 有关详细信息，请参阅[分步指南](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>开始体验

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 SAP ECC 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

SAP ECC 链接服务支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapEcc`。 | 是 |
| `url` | SAP ECC OData 服务的 URL。 | 是 |
| `username` | 用于连接到 SAP ECC 的用户名。 | No |
| `password` | 用于连接到 SAP ECC 的明文密码。 | No |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定运行时，则使用默认的 Azure 集成运行时。 | No |

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

有关可用于定义数据集的各节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供 SAP ECC 数据集支持的属性列表。

若要从 SAP ECC 复制数据，请将数据集的 `type` 属性设置为 `SapEccResource`。

支持以下属性：

| properties | 描述 | 需要 |
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
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP ECC 源支持的属性列表。

### <a name="sap-ecc-as-a-source"></a>SAP ECC 作为源

若要从 SAP ECC 复制数据，请将复制活动的 "`source`" 部分中的 "`type` 属性设置为" `SapEccSource`"。

复制活动的 `source` 部分支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| `type` | 复制活动的 `source` 部分的 `type` 属性必须设置为 "`SapEccSource`"。 | 是 |
| `query` | 用于筛选数据的 OData 查询选项。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 连接器从组合的 URL 复制数据：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>有关详细信息，请参阅 [OData URL 组件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | No |

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

从 SAP ECC 复制数据时，以下映射用于从 SAP ECC 数据的 OData 数据类型到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

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
> 当前不支持复杂数据类型。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中的复制活动支持作为源和接收器的数据存储列表，请参阅支持的[数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
