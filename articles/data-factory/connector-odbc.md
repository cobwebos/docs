---
title: "使用 Azure 数据工厂从 ODBC 源复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 OData 源复制到支持的接收器数据存储。"
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
ms.date: 10/19/2017
ms.author: jingwang
ms.openlocfilehash: 5ef8b81b2aa4df802f67b1a9e90b6bd60dcd1168
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 ODBC 数据存储复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-odbc-connector.md)
> * [版本 2 - 预览版](connector-odbc.md)

本文概述了如何在 Azure 数据工厂中使用复制活动从/向 ODBC 数据存储复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 数据工厂。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅 [V1 中的 ODBC 连接器](v1/data-factory-odata-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可将数据从 ODBC 源复制到任何受支持的接收器数据存储，或者从任何受支持的源数据存储复制到 ODBC 接收器。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 ODBC 连接器支持使用 **Basic** 或 **Anonymous** 身份验证从/向任何与 ODBC 兼容的数据存储复制数据。

## <a name="prerequisites"></a>系统必备

要使用此 ODBC 连接器，需要：

- 设置自我托管集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。
- 在集成运行时计算机上安装数据存储的 ODBC 驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 ODBC 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

ODBC 链接服务支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：Odbc | 是 |
| connectionString | 不包括凭据部分的连接字符串。 可以使用类似 `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` 的模式指定连接字符串，也可以利用在 Integration Runtime 计算机上使用 `"DSN=<name of the DSN on IR machine>;"` 设置的系统 DSN（数据源名称）（仍需要相应地指定链接服务中的凭据部分）。| 是 |
| authenticationType | 用于连接 ODBC 数据存储的身份验证类型。<br/>允许的值是：Basic 和 Anonymous。 | 是 |
| userName | 如果使用基本身份验证，请指定用户名。 | 否 |
| password | 指定为 userName 指定的用户帐户的密码。 将此字段标记为 SecureString。 | 否 |
| credential | 连接字符串的访问凭据部分，采用特定于驱动程序的属性值格式指定。 示例：`"RefreshToken=<secret refresh token>;"`。 将此字段标记为 SecureString。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例 1：使用 Basic 身份验证**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

**示例 2：使用 Anonymous 身份验证**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 ODBC 数据集支持的属性列表。

要从/向与 ODBC 兼容的数据存储复制数据，请将数据集的 type 属性设置为“RelationalTable”。 支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：RelationalTable | 是 |
| tableName | ODBC 数据存储中表的名称。 | 源为否（如果指定了活动源中的“query”）；<br/>接收器为是 |

**示例**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 ODBC 源支持的属性列表。

### <a name="odbc-as-source"></a>ODBC 作为源

要从与 ODBC 兼容的数据存储复制数据，请将复制活动中的源类型设置为“RelationalSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：RelationalSource | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC 作为接收器

要向与 ODBC 兼容的数据存储复制数据，请将复制活动中的接收器类型设置为“OdbcSink”。 复制活动接收器部分中支持以下属性：

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | 复制活动接收器的 type 属性必须设置为：**OdbcSink** | 是 |
| writeBatchTimeout |超时之前等待批插入操作完成时的等待时间。<br/>允许的值为：timespan。 示例：“00:30:00”（30 分钟）。 |否 |
| writeBatchSize |缓冲区大小达到 writeBatchSize 时会数据插入 SQL 表。<br/>允许的值为：整数（行数）。 |否（默认值为 0 - 自动检测） |
| preCopyScript |每次运行时，将数据写入到数据存储之前，指定复制活动要执行的 SQL 查询。 此属性可用于清理预先加载的数据。 |否 |

> [!NOTE]
> 对于“writeBatchSize”，如果未设置（自动检测），则复制活动首先检测驱动程序是否支持批处理操作，如果支持，则将其设置为 10000，否则或将其设置为 1。 如果显式设置 0 以外的值，则复制活动遵循值，如果驱动程序不支持批处理操作，则会在运行时失败。

**示例：**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>IBM Informix 源

可以使用泛型 ODBC 连接器从 IBM Informix 数据库复制数据。

在一台可以访问数据存储的计算机上设置一个自我托管的集成运行时。 Integration Runtime 使用 Informix 的 ODBC 驱动程序连接到数据存储。 因此，如果尚未在相同的计算机上安装驱动程序，请先安装。 例如，可以使用驱动程序“IBM INFORMIX ODBC 驱动程序（64 位）”。 有关详细信息，请参阅[先决条件](#prerequisites)部分。

在数据工厂解决方案中使用 Informix 源之前，请参照[解决连接问题](#troubleshoot-connectivity-issues)部分中的说明验证 Integration Runtime 能否连接到数据存储。

创建一个 ODBC 链接服务，将 IBM Informix 数据存储链接到 Azure 数据工厂，如以下示例所示：

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

从头开始阅读文章，了解在复制操作中将 ODBC 数据存储用作源/接收器数据存储的详细信息。

## <a name="microsoft-access-source"></a>Microsoft Access 源

可以使用泛型 ODBC 连接器从 Microsoft Access 数据库复制数据。

在一台可以访问数据存储的计算机上设置一个自我托管的集成运行时。 Integration Runtime 使用 Microsoft Access 的 ODBC 驱动程序连接到数据存储。 因此，如果尚未在相同的计算机上安装驱动程序，请先安装。 有关详细信息，请参阅[先决条件](#prerequisites)部分。

在数据工厂解决方案中使用 Microsoft Access 源之前，请参照[解决连接问题](#troubleshoot-connectivity-issues)部分中的说明验证 Integration Runtime 能否连接到数据存储。

创建一个 ODBC 链接服务，将 Microsoft Access 数据库链接到 Azure 数据工厂，如以下示例所示：

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

从头开始阅读文章，了解在复制操作中将 ODBC 数据存储用作源/接收器数据存储的详细信息。

## <a name="ge-historian-source"></a>GE Historian 源

可以使用泛型 ODBC 连接器从 GE Historian 数据库复制数据。

在一台可以访问数据存储的计算机上设置一个自我托管的集成运行时。 集成运行时使用 GE Historian 的 ODBC 驱动程序连接到数据存储。 因此，如果尚未在相同的计算机上安装驱动程序，请先安装。 有关详细信息，请参阅[先决条件](#prerequisites)部分。

在数据工厂解决方案中使用 GE Historian 源之前，请参照[解决连接问题](#troubleshoot-connectivity-issues)部分中的说明验证 Integration Runtime 能否连接到数据存储。

创建一个 ODBC 链接服务，将 Microsoft Access 数据库链接到 Azure 数据工厂，如以下示例所示：

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

从头开始阅读文章，了解在复制操作中将 ODBC 数据存储用作源/接收器数据存储的详细信息。

## <a name="sap-hana-sink"></a>SAP HANA 接收器

>[!NOTE]
>若要从 SAP HANA 数据存储中复制数据，请参阅本机的 [SAP HANA 连接器](connector-sap-hana.md)。 若要将数据复制到 SAP HANA，请按照此说明使用 ODBC 连接器。 注意：适用于 SAP HANA 连接器和 ODBC 连接器的链接服务采用不同的类型，因此不能重用。
>

可以使用泛型 ODBC 连接器将数据复制到 SAP HANA 数据库。

在一台可以访问数据存储的计算机上设置一个自我托管的集成运行时。 集成运行时使用 SAP HANA 的 ODBC 驱动程序连接到数据存储。 因此，如果尚未在相同的计算机上安装驱动程序，请先安装。 有关详细信息，请参阅[先决条件](#prerequisites)部分。

在数据工厂解决方案中使用 SAP HANA 接收器之前，请参照[解决连接问题](#troubleshoot-connectivity-issues)部分中的说明验证 Integration Runtime 能否连接到数据存储。

创建一个 ODBC 链接服务，将 SAP HANA 数据存储链接到 Azure 数据工厂，如以下示例所示：

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

从头开始阅读文章，了解在复制操作中将 ODBC 数据存储用作源/接收器数据存储的详细信息。

## <a name="troubleshoot-connectivity-issues"></a>解决连接问题

若要解决连接问题，请使用“集成运行时配置管理器”的“诊断”选项卡。

1. 启动集成运行时配置管理器。
2. 切换到“诊断”选项卡。
3. 在“测试连接”部分下，选择数据存储的“类型”（链接服务）。
4. 指定用于连接数据存储的“连接字符串”，选择“身份验证”并输入“用户名”、“密码”和/或“凭据”。
5. 单击“测试连接”以测试数据存储的连接。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。