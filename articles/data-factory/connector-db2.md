---
title: 使用 Azure 数据工厂从 DB2 复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 DB2 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: e72e6c112913d646b6dc1479a9b80acc6d4ec7b1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280756"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 DB2 复制数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [第 1 版](v1/data-factory-onprem-db2-connector.md)
> * [当前版本](connector-db2.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 DB2 数据库复制数据。 本文基于说明复制活动总体概述的[复制活动概述](copy-activity-overview.md)一文构建。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 DB2 数据库连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 DB2 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 DB2 连接器支持以下 IBM DB2 平台和版本，以及分布式关系数据库结构 (DRDA) SQL 访问管理器 (SQLAM) 版本 9、版本 10 和版本 11：

* IBM DB2 for z/OS 12.1
* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.3
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

> [!TIP]
> 如果看到错误消息“找不到与 SQL 语句执行请求对应的包。 SQLSTATE=51002 SQLCODE=-805”，这是因为没有为此类 OS 上的普通用户创建所需的包。 请按照以下说明操作，具体视 DB2 服务器类型而异：
> - 用于 i (AS400) 的 DB2：让 Power User 在使用复制活动前，先为登录用户创建集合。 命令：`create collection <username>`
> - 用于 z/OS 或 LUW 的 DB2：使用高权限帐户（即拥有包权限和 BIND、BINDADD、GRANT EXECUTE TO PUBLIC 权限的超级用户或管理员）运行复制活动一次，然后所需的包便会在复制期间自动创建。 之后，可以切换回正常用户，继续运行后续复制。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

集成运行时提供内置 DB2 驱动程序，因此从 DB2 复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 DB2 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

DB2 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：Db2 | 是 |
| server |DB2 服务器的名称。 可以在冒号分隔的服务器名称后面指定端口号，例如 `server:port`。 |是 |
| database |DB2 数据库的名称。 |是 |
| authenticationType |用于连接 DB2 数据库的身份验证类型。<br/>允许的值为：Basic。 |是 |
| username |指定用于连接到 DB2 数据库的用户名。 |是 |
| password |指定为用户名指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是 |
| packageCollection | 指定在查询数据库时 ADF 自动创建所需包的位置 | 否 |
| certificateCommonName | 使用安全套接字层（SSL）或传输层安全性（TLS）加密时，必须为 "证书公用名" 输入一个值。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[先决条件](#prerequisites)部分了解更多信息。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

**示例：**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 DB2 数据集支持的属性列表。

若要从 DB2 复制数据，需要支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为： **Db2Table** | 是 |
| schema | 架构的名称。 |否（如果指定了活动源中的“query”）  |
| 表 | 表名称。 |否（如果指定了活动源中的“query”）  |
| tableName | 具有架构的表的名称。 此属性支持后向兼容性。 对于新的工作负荷，请使用 `schema` 和 `table`。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果使用 `RelationalTable` 类型数据集，该数据集仍按原样受支持，但我们建议今后使用新数据集。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 DB2 源支持的属性列表。

### <a name="db2-as-source"></a>DB2 作为源

若要从 DB2 复制数据，复制活动的 **source** 节需要支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为： **Db2Source** | 是 |
| 查询 | 使用自定义 SQL 查询读取数据。 例如：`"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用 `RelationalSource` 类型源，该源仍按原样受支持，但我们建议今后使用新源。

## <a name="data-type-mapping-for-db2"></a>DB2 的数据类型映射

从 DB2 复制数据时，以下映射用于从 DB2 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| DB2 数据库类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| BigInt |Int64 |
| 二进制 |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| 日期 |Datetime |
| DB2DynArray |String |
| DbClob |String |
| 小数 |小数 |
| DecimalFloat |小数 |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| 数字 |小数 |
| Real |Single |
| SmallInt |Int16 |
| 时间 |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
