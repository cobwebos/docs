---
title: 使用 Azure 数据工厂（预览版）从 Presto 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Presto 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: aeb1e8b2bd4491a8124d95907308f014b7a36656
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680393"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>使用 Azure 数据工厂（预览版）从 Presto 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 Presto 复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!IMPORTANT]
> 此连接器目前提供预览版。 欢迎试用并提供反馈。 若要在解决方案中使用预览版连接器的依赖项，请联系 [Azure 支持部门](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Presto 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 Presto 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Presto 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Presto 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Presto** | 是 |
| host | Presto 服务器的 IP 地址或主机名。 （即 192.168.222.160）  | 是 |
| serverVersion | Presto 服务器的版本。 （即 0.148-t）  | 是 |
| 目录 | 针对服务器的所有请求的目录上下文。  | 是 |
| port | Presto 服务器用来侦听客户端连接的 TCP 端口。 默认值为 8080。  | 否 |
| authenticationType | 用于连接到 Presto 服务器的身份验证机制。 <br/>允许的值为：Anonymous、LDAP | 是 |
| username | 用于连接到 Presto 服务器的用户名。  | 否 |
| password | 用户名所对应的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| enableSsl | 指定是否使用 SSL 加密服务器连接。 默认值为 false。  | 否 |
| trustedCertPath | 包含受信任 CA 证书（通过 SSL 进行连接时用于验证服务器）的 .pem 文件的完整路径。 只有在自托管 IR 上使用 SSL 时才能设置此属性。 默认值是随 IR 一起安装的 cacerts.pem 文件。  | 否 |
| useSystemTrustStore | 指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 默认值为 false。  | 否 |
| allowHostNameCNMismatch | 指定通过 SSL 进行连接时是否要求 CA 颁发的 SSL 证书的名称与服务器的主机名相匹配。 默认值为 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 false。  | 否 |
| timeZoneID | 连接使用的本地时区。 IANA 时区数据库中指定了此选项的有效值。 默认值为系统时区。  | 否 |

**示例：**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Presto 数据集支持的属性列表。

要从 Presto 复制数据，请将数据集的 type 属性设置为“PrestoObject”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **PrestoObject** | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 支持此属性是为了向后兼容。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各节和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Presto 数据源支持的属性列表。

### <a name="presto-as-source"></a>以 Presto 作为源

要从 Presto 复制数据，请将复制活动中的源类型设置为“PrestoSource”。 复制活动**source**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：PrestoSource | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
