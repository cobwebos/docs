---
title: "使用 Azure 数据工厂从 HTTP 源复制数据 | Microsoft Docs"
description: "了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从云或本地 HTTP 源复制到支持的接收器数据存储。"
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
ms.date: 10/12/2017
ms.author: jingwang
ms.openlocfilehash: 54afc7d993058ac2b3d2990ba131d334e9332555
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>使用 Azure 数据工厂从 HTTP 终结点复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-http-connector.md)
> * [版本 2 - 预览版](connector-http.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 HTTP 终结点复制数据。 本文基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 HTTP 连接器](v1/data-factory-http-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 HTTP 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 HTTP 连接器支持：

- 通过使用 HTTP GET 或 POST 方法，从 HTTP/s 终结点检索数据。
- 使用以下身份验证类型检索数据：**匿名**、**基本**、**摘要**、**Windows** 和 **ClientCertificate**。
- 按原样复制 HTTP 响应，或者使用[支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md)分析该响应。

此连接器与 [Web 表连接器](connector-web-table.md)之间的差异在于，后者用于从 HTML 网页提取表内容。

## <a name="getting-started"></a>入门
可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure 资源管理器模板创建包含复制活动的管道。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](quickstart-create-data-factory-dot-net.md)。

对于特定于 HTTP 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

HTTP 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 **HttpServer**。 | 是 |
| url | Web 服务器的基 URL | 是 |
| enableServerCertificateValidation | 指定连接到 HTTP 终结点时，是否启用服务器 SSL 证书验证。 | 不启用，默认值为 true |
| authenticationType | 指定身份验证类型。 允许的值为：**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate**。 <br><br> 有关这些身份验证类型的其他属性和 JSON 示例，请分别参阅此表格下面的部分。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 |否 |

### <a name="using-basic-digest-or-windows-authentication"></a>使用基本、摘要或 Windows 身份验证

将“authenticationType”属性设置为“Basic”、“Digest”或“Windows”，并指定以下属性及上节所述的泛型属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| userName | 用于访问 HTTP 终结点的用户名。 | 是 |
| password | 用户 (userName) 的密码。 将此字段标记为 SecureString。 | 是 |

**示例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>使用 ClientCertificate 身份验证

要使用 ClientCertificate 身份验证，请将“authenticationType”属性设置为“ClientCertificate”，并指定以下属性以及在上一节中所述的泛型属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 编码的证书数据。 | 指定 `embeddedCertData` 或 `certThumbprint`。 |
| certThumbprint | 自承载集成运行时计算机的证书存储中所安装证书的指纹。 仅当在 connectVia 中指定自承载类型的集成运行时时适用。 | 指定 `embeddedCertData` 或 `certThumbprint`。 |
| password | 与证书关联的密码 将此字段标记为 SecureString。 | 否 |

如果使用“certThumbprint”进行身份验证，并在本地计算机的个人存储中安装了证书，则需要授予对自承载集成运行时的读取权限：

1. 启动 Microsoft 管理控制台 (MMC)。 添加面向“本地计算机”的“证书”管理单元。
2. 展开“证书”、“个人”，并单击“证书”。
3. 右键单击个人存储中的证书，并选择“所有任务” -> “管理私钥...”
3. 在“安全性”选项卡上，添加运行集成运行时托管服务 (DIAHostService) 的、对证书具有读取访问权限的用户帐户。

**示例 1：使用 certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例 2：使用 embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 HTTP 数据集支持的属性列表。

要从 HTTP 复制数据，请将数据集的 type 属性设置为“HttpFile”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**HttpFile** | 是 |
| relativeUrl | 包含数据的资源的相对 URL。 未指定此属性时，仅使用链接服务定义中指定的 URL。 | 否 |
| requestMethod | Http 方法。<br/>允许的值为 Get（默认值）或 Post。 | 否 |
| additionalHeaders | 附加的 HTTP 请求标头。 | 否 |
| requestBody | HTTP 请求的正文。 | 否 |
| 格式 | 如果要在未经分析的情况下从 HTTP 终结点按原样检索数据，并将其复制到基于文件的存储，请跳过输入和输出数据集定义中的格式部分。<br/><br/>如果要在复制期间分析 HTTP 响应内容，下面是支持的文件格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅 [Json格式](supported-file-formats-and-compression-codecs.md#json-format)、[文本格式](supported-file-formats-and-compression-codecs.md#text-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)部分。 |否 |
| compression | 指定数据的压缩类型和级别。 有关详细信息，请参阅[受支持的文件格式和压缩编解码器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。<br/>支持的级别为：**最佳**和**最快**。 |否 |

**示例 1：使用 Get 方法（默认）**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**示例 2：使用 Post 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各个部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 HTTP 源支持的属性列表。

### <a name="http-as-source"></a>HTTP 作为源

要从 HTTP 复制数据，请将复制活动中的源类型设置为“HttpSource”。 复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**HttpSource** | 是 |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 (TimeSpan)。 这是获取响应而不是读取响应数据的超时。<br/> 默认值为：00:01:40  | 否 |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。