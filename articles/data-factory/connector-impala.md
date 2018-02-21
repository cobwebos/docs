---
title: "使用 Azure 数据工厂（Beta 版本）从 Impala 复制数据 | Microsoft Docs"
description: "了解如何使用数据工厂管道中的复制活动，将数据从 Impala 复制到支持的接收器数据存储。"
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: e1f745fc70395f06d2eb3d98644d54c314a0ef26
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>使用 Azure 数据工厂（Beta 版本）从 Impala 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Impala 复制数据。 本文基于总体概述复制活动的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用已正式发布的版本 1 的数据工厂，请参阅[版本 1 中的复制活动](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此连接器目前处于 Beta 版本。 可以进行试用并提供反馈。 请勿在生产环境中使用该版本。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Impala 复制到任何支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

 数据工厂提供内置驱动程序以启用连接。 因此，无需要手动安装驱动程序即可使用此连接器。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

对于特定于 Impala 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Impala 链接服务支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Impala**。 | 是 |
| host | Impala 服务器的 IP 地址或主机名（即，192.168.222.160）。  | 是 |
| 端口 | Impala 服务器用来侦听客户端连接的 TCP 端口。 默认值为 21050。  | 否 |
| authenticationType | 可使用的身份验证类型。 <br/>允许的值为：**Anonymous**、**SASLUsername** 和 **UsernameAndPassword**。 | 是 |
| username | 用于访问 Impala 服务器的用户名。 使用 SASLUsername 时，默认值是匿名的。  | 否 |
| password | 使用 UsernameAndPassword 时用户名所对应的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| enableSsl | 指定是否使用 SSL 加密到服务器的连接。 默认值为 **false**。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 仅当在自承载集成运行时上使用 SSL 时，才能设置此属性。 默认值是随集成运行时一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 **false**。  | 否 |
| allowHostNameCNMismatch | 指定通过 SSL 进行连接时是否要求 CA 颁发的 SSL 证书的名称与服务器的主机名相匹配。 默认值为 **false**。  | 否 |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 **false**。  | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Impala 数据集支持的属性列表。

要从 Impala 复制数据，请将数据集的 type 属性设置为“ImpalaObject”。 此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Impala 源类型支持的属性列表。

### <a name="impala-as-a-source-type"></a>以 Impala 作为源类型

要从 Impala 复制数据，请将复制活动中的源类型设置为“ImpalaSource”。 复制活动的 **source** 节支持以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为 **ImpalaSource**。 | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如 `"SELECT * FROM MyTable"`。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>后续步骤
有关数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
