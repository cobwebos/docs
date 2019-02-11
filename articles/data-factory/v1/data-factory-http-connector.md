---
title: 从 HTTP 源移动数据 - Azure | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从本地或云 HTTP 源移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f7e070788d2fc11addcafc30d9f232f194f44782
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017253"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 HTTP 源移动数据

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1](data-factory-http-connector.md)
> * [版本 2（当前版本）](../connector-http.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本的 Azure 数据工厂服务，请参阅 [V2 中的 HTTP 连接器](../connector-http.md)。


本文概述如何使用 Azure 数据工厂中的复制活动将数据从本地或云 HTTP 终结点移至受支持的接收器数据存储。 本文基于[使用复制活动移动数据](data-factory-data-movement-activities.md)一文，后者总体概述了如何使用复制活动移动数据。 本文还列出了复制活动支持用作源和接收器的数据存储。

数据工厂当前仅支持将数据从 HTTP 源移至其他数据存储。 它不支持将数据从其他数据存储移至 HTTP 目标。

## <a name="supported-scenarios-and-authentication-types"></a>支持的方案和身份验证类型

可以使用此 HTTP 连接器通过 HTTP **GET** 或 **POST** 方法从*云和本地 HTTP/S 终结点*中检索数据。 支持以下身份验证类型：**Anonymous**、**Basic**、**Digest**、**Windows** 和 **ClientCertificate**。 请注意此连接器与 [Web 表连接器](data-factory-web-table-connector.md)之间的区别。 Web 表连接器用于从 HTML 网页中提取表内容。

从本地 HTTP 终结点复制数据时，必须在本地环境或 Azure VM 中安装数据管理网关。 若要了解数据管理网关以及有关如何设置网关的分步说明，请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。

## <a name="get-started"></a>入门

可以使用各种工具或 API 创建包含复制活动的管道，用于从 HTTP 源移动数据：

- 创建管道最简单的方法是使用复制数据向导。 有关使用复制数据向导创建管道的快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

- 也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 或 **REST API**。 有关如何创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 有关将数据从 HTTP 源复制到Azure Blob 存储的 JSON 示例，请参阅 [JSON 示例](#json-examples)。

## <a name="linked-service-properties"></a>链接服务属性

下表描述了特定于 HTTP 链接服务的 JSON 元素：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type | **type** 属性必须设置为 **Http**。 | 是 |
| url | Web 服务器的基 URL。 | 是 |
| authenticationType | 指定身份验证类型。 允许的值为：Anonymous、Basic、Digest、Windows 和 ClientCertificate。 <br><br> 有关这些身份验证类型的更多属性和 JSON 示例，请参阅本文后续部分。 | 是 |
| enableServerCertificateValidation | 指定当源为 HTTPS Web 服务器时，是否启用服务器 SSL 证书验证。 当 HTTPS 服务器使用自签名证书时，将其设置为 **false**。 | 否<br /> （默认值为 true） |
| gatewayName | 用于连接本地 HTTP 源的数据管理网关实例的名称。 | 是（如果从本地 HTTP 源复制数据） |
| encryptedCredential | 用于访问 HTTP 终结点的已加密凭据。 在复制向导中或使用 **ClickOnce** 对话框配置身份验证信息时，将自动生成该值。 | 否<br /> （仅当从本地 HTTP 服务器复制数据时才适用） |

有关设置本地 HTTP 连接器数据源凭据的详细信息，请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。

### <a name="using-basic-digest-or-windows-authentication"></a>使用基本、摘要或 Windows 身份验证

将“authenticationType”设置为“基本”、“摘要式”或“Windows”。 除了前面部分所述的通用 HTTP 连接器属性，还设置以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| username | 用于访问 HTTP 终结点的用户名。 | 是 |
| password | 用户 (**username**) 的密码。 | 是 |

示例：**使用基本、摘要或 Windows 身份验证**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>使用 ClientCertificate 身份验证

若要使用基本身份验证，请将“authenticationType”设置为“ClientCertificate”。 除了前面部分所述的通用 HTTP 连接器属性，还设置以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| embeddedCertData | PFX 文件的二进制数据的 Base64 编码内容。 | 指定是 **embeddedCertData**，还是 **certThumbprint** |
| certThumbprint | 在网关计算机的证书存储中安装的证书的指纹。 仅当从本地 HTTP 源复制数据时才适用。 | 指定是 **embeddedCertData**，还是 **certThumbprint** |
| password | 与证书关联的密码。 | 否 |

如果使用 **certThumbprint** 进行身份验证，并在本地计算机的个人存储中安装了证书，则需要向网关服务授予读取权限：

1. 打开 Microsoft 管理控制台 (MMC)。 添加面向“本地计算机”的“证书”管理单元。
2. 展开“证书” > “个人”，然后选择“证书”。
3. 右键单击个人存储中的证书，并选择“所有任务” >“管理私钥”。
3. 在“安全性”选项卡上，添加运行数据管理网关主机服务并对证书具有读取访问权限的用户帐户。  

示例：**使用客户端证书**

此链接服务将数据工厂链接到本地 HTTP Web 服务器。 它使用装有数据管理网关的计算机上安装的客户端证书。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

示例：**使用文件中的客户端证书**

此链接服务将数据工厂链接到本地 HTTP Web 服务器。 它使用装有数据管理网关的计算机上的客户端证书文件。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

在数据集 JSON 文件中，所有数据集类型（Azure SQL 数据库、Azure Blob 存储、Azure 表存储）的 structure、availability 和 policy 等节均类似。

有关可用于定义数据集的各部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)。

每个数据集类型的 **typeProperties** 节都不同。 **typeProperties** 节提供有关数据在数据存储中的位置的信息。 **Http** 类型的数据集的 **typeProperties** 节具有以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 **type** 必须设置为 **Http**。 | 是 |
| relativeUrl | 包含数据的资源的相对 URL。 未指定路径时，仅使用链接服务定义中指定的 URL。 <br><br> 若要构造动态 URL，可以使用[数据工厂函数和系统变量](data-factory-functions-variables.md)。 示例：**relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**。 | 否 |
| requestMethod | HTTP 方法。 允许的值为 **GET** 和 **POST**。 | 否 <br />（默认值为 **GET**） |
| additionalHeaders | 附加的 HTTP 请求标头。 | 否 |
| requestBody | HTTP 请求的正文。 | 否 |
| 格式 | 如果要*从 HTTP 终结点按原样检索数据*而不分析它，请跳过 **format** 设置。 <br><br> 如果要在复制期间分析 HTTP 响应内容，则支持以下格式类型：TextFormat、JsonFormat、AvroFormat、OrcFormat 和 ParquetFormat。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[JSON 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别：“最佳”和“最快”。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

示例：**使用 GET（默认）方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

示例：**使用 POST 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)。 

可在活动的 **typeProperties** 节中使用的属性因每个活动的类型而异。 对于复制活动，属性因源和接收器类型而异。

目前，如果复制活动中的源为 **HttpSource** 类型，则支持以下属性：

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| httpRequestTimeout | 用于获取响应的 HTTP 请求的超时 （TimeSpan 值）。 这是获取响应而不是读取响应数据的超时。 | 否<br />（默认值：**00:01:40**） |

## <a name="supported-file-and-compression-formats"></a>支持的文件和压缩格式

有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)。

## <a name="json-examples"></a>JSON 示例

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 这些示例展示如何将数据从 HTTP 源复制到 Azure Blob 存储。 但是，使用 Azure 数据工厂中的复制活动，可以将数据*直接*从任何源复制到任何[受支持](data-factory-data-movement-activities.md#supported-data-stores-and-formats)的接收器。

示例：**将数据从 HTTP 源复制到 Azure Blob 存储**

此示例的数据工厂解决方案包含以下数据工厂实体：

*   [HTTP](#linked-service-properties) 类型的链接服务。
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
*   [Http](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
*   包含复制活动的[管道](data-factory-create-pipelines.md)，该活动使用 [HttpSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 HTTP 源复制到 Azure Blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

### <a name="http-linked-service"></a>HTTP 链接服务

此示例使用采取匿名身份验证的 HTTP 链接服务。 请参阅 [HTTP 链接服务](#linked-service-properties)，了解各种可使用的身份验证。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP 输入数据集

将 **external** 设置为 **true** 将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集

数据每小时向新的 blob 写入一次（**frequency**：**hour**，**interval**：**1**）。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>使用复制活动的管道

管道包含配置为使用输入和输出数据集的复制活动。 该复制活动计划每小时运行一次。 在管道 JSON 定义中，将 **source** 类型设置为 **HttpSource**，将 **sink** 类型设置为 **BlobSink**。

有关 **HttpSource** 支持的属性的列表，请参阅 [HttpSource](#copy-activity-properties)。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化

若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。