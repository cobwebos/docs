---
title: 使用 Azure 数据工厂从 Hive 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Hive 复制到支持的接收器数据存储。
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
ms.date: 04/19/2018
ms.author: jingwang
ms.openlocfilehash: 379cc5412d317680afa9b03f0eea60c7f1a3b60d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051072"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>使用 Azure 数据工厂从 Hive 复制数据 

本文概述了如何使用 Azure 数据工厂中的复制活动从 Hive 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Hive 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Hive 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Hive 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Hive** | 是 |
| host | Hive 服务器的 IP 地址或主机名；对于多台主机，将以“;”分隔（仅限启用了 serviceDiscoveryMode 时）。  | 是 |
| port | Hive 服务器用来侦听客户端连接的 TCP 端口。 如果连接到 Azure HDInsights，请指定端口 443。 | 是 |
| serverType | Hive 服务器的类型。 <br/>允许的值为：**HiveServer1**、**HiveServer2**、**HiveThriftServer** | 否 |
| thriftTransportProtocol | Thrift 层中要使用的传输协议。 <br/>允许的值为：**Binary**、**SASL**、**HTTP** | 否 |
| authenticationType | 用于访问 Hive 服务器的身份验证方法。 <br/>允许的值为：**Anonymous**、**Username**、**UsernameAndPassword**、**WindowsAzureHDInsightService** | 是 |
| serviceDiscoveryMode | true 指示使用 ZooKeeper 服务，false 指示不使用。  | 否 |
| zooKeeperNameSpace | ZooKeeper 上要将 Hive Server 2 节点添加到其下的命名空间。  | 否 |
| useNativeQuery | 指定驱动程序是使用本机 HiveQL 查询，还是将其转换为 HiveQL 中的等效形式。  | 否 |
| username | 用于访问 Hive 服务器的用户名。  | 否 |
| password | 用户所对应的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| httpPath | 对应于 Hive 服务器的部分 URL。  | 否 |
| enableSsl | 指定是否使用 SSL 加密到服务器的连接。 默认值为 false。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 只有在自托管 IR 上使用 SSL 时才能设置此属性。 默认值是随 IR 一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 false。  | 否 |
| allowHostNameCNMismatch | 指定通过 SSL 进行连接时是否要求 CA 颁发的 SSL 证书的名称与服务器的主机名相匹配。 默认值为 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 false。  | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果可以公开访问数据存储，则可以使用自承载集成运行时或 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供了 Hive 数据集支持的属性列表。

要从 Hive 复制数据，请将数据集的 type 属性设置为 **HiveObject**。 此类型的数据集中没有任何其他特定于类型的属性。

**示例**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供了 Hive 源支持的属性列表。

### <a name="hivesource-as-source"></a>HiveSource 作为源

要从 Hive 复制数据，请将复制活动中的源类型设置为 **HiveSource**。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**HiveSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 是 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
