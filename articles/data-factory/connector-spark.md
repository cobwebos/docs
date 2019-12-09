---
title: 从 Spark 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Spark 复制到支持的接收器数据存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 63e352ce47c5934171594ae87ee307603fff4c35
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931024"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>使用 Azure 数据工厂从 Spark 复制数据 

本文概述了如何使用 Azure 数据工厂中的复制活动从 Spark 复制数据。 是基于总体介绍复制活动的[复制活动概述](copy-activity-overview.md)一文进行扩展的。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Spark 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Spark 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Spark 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Spark 链接服务支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Spark** | 是 |
| host | Spark 服务器的 IP 地址或主机名  | 是 |
| port | Spark 服务器用来侦听客户端连接的 TCP 端口。 如果连接到 Azure HDInsights，请指定端口 443。 | 是 |
| serverType | Spark 服务器的类型。 <br/>允许的值为：**SharkServer**、**SharkServer2**、**SparkThriftServer** | No |
| thriftTransportProtocol | Thrift 层中要使用的传输协议。 <br/>允许的值为：**Binary**、**SASL**、**HTTP** | No |
| authenticationType | 用于访问 Spark 服务器的身份验证方法。 <br/>允许的值为：**Anonymous**、**Username**、**UsernameAndPassword**、**WindowsAzureHDInsightService** | 是 |
| username | 用于访问 Spark 服务器的用户名。  | No |
| password | 用户所对应的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | No |
| httpPath | 对应于 Spark 服务器的部分 URL。  | No |
| enableSsl | 指定是否使用 SSL 加密到服务器的连接。 默认值为 false。  | No |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 只有在自托管 IR 上使用 SSL 时才能设置此属性。 默认值是随 IR 一起安装的 cacerts.pem 文件。  | No |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 false。  | No |
| allowHostNameCNMismatch | 指定通过 SSL 进行连接时是否要求 CA 颁发的 SSL 证书的名称与服务器的主机名相匹配。 默认值为 false。  | No |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 false。  | No |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |No |

**示例：**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Spark 数据集支持的属性列表。

要从 Spark 复制数据，请将数据集的 type 属性设置为“SparkObject”。 支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **SparkObject** | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 为新的工作负荷使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Spark 数据源支持的属性列表。

### <a name="spark-as-source"></a>Spark 作为源

要从 Spark 复制数据，请将复制活动中的源类型设置为“SparkSource”。 复制活动源部分支持以下属性：

| properties | 描述 | 需要 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：SparkSource | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
