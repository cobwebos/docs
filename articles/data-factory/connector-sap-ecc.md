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
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 3f51ad3d0a75ab1f0662ea0b747852fcb98e5026
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259051"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP ECC 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 SAP Enterprise Central Component (ECC) 复制数据。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

>[!TIP]
>若要了解 ADF 全面支持 SAP 数据集成方案，请参阅[使用 Azure 数据工厂的 SAP 数据集成白皮书](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，并提供详细的简介、comparsion 和指南。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 SAP ECC 复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP ECC 连接器支持：

- 在 SAP NetWeaver 7.0 及更高版本上从 SAP ECC 复制数据。
- 从 SAP ECC OData 服务公开的任何对象复制数据，例如以下对象：

  - SAP 表或视图。
  - 业务应用程序编程接口 [BAPI] 对象。
  - 数据提取程序。
  - 发送给 SAP 进程集成 (PI) 的数据或中间文档 (IDOC)，可以通过相对适配器以 OData 形式接收。

- 使用基本身份验证复制数据。

>[!TIP]
>若要通过 SAP 表或视图从 SAP ECC 复制数据，请使用速度更快且可伸缩性更强的 [SAP 表](connector-sap-table.md)连接器。

## <a name="prerequisites"></a>先决条件

通常，SAP ECC 通过 SAP 网关经由 OData 服务来公开实体。 若要使用此 SAP ECC 连接器，需要：

- **设置 SAP 网关**。 如果服务器的 SAP NetWeaver 版本高于 7.4，则表明 SAP 网关已安装。 如果版本较低，则在通过 OData 服务公开 SAP ECC 数据之前，必须安装嵌入式 SAP 网关或 SAP 网关中心系统。 若要设置 SAP 网关，请参阅[安装指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **激活并配置 SAP OData 服务**。 可以通过 TCODE SICF 在数秒内激活 OData 服务。 还可以配置需要公开哪些对象。 有关详细信息，请参阅[分步指南](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分详细介绍了用于定义特定于 SAP ECC 连接器的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

SAP ECC 链接服务支持以下属性：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapEcc`。 | 是 |
| `url` | SAP ECC OData 服务的 URL。 | 是 |
| `username` | 用于连接到 SAP ECC 的用户名。 | 否 |
| `password` | 用于连接到 SAP ECC 的明文密码。 | 否 |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定运行时，则使用默认的 Azure 集成运行时。 | 否 |

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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供 SAP ECC 数据集支持的属性的列表。

若要从 SAP ECC 复制数据，请将数据集的 `type` 属性设置为 `SapEccResource`。

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
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP ECC 源支持的属性的列表。

### <a name="sap-ecc-as-a-source"></a>以 SAP ECC 作为源

若要从 SAP ECC 复制数据，请将复制活动的 `source` 节中的 `type` 属性设置为 `SapEccSource`。

复制活动的 `source` 节支持以下属性：

| 属性 | 说明 | 必填 |
|:--- |:--- |:--- |
| `type` | 复制活动的 `source` 节的 `type` 属性必须设置为 `SapEccSource`。 | 是 |
| `query` | 用于筛选数据的 OData 查询选项。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 连接器会从以下组合 URL 复制数据：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>有关详细信息，请参阅 [OData URL 组件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

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

从 SAP ECC 复制数据时，可以使用以下映射将 SAP ECC 数据的 OData 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

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

有关可供 Azure 数据工厂中的复制活动用作源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
