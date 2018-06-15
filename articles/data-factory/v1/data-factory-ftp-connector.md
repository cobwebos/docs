---
title: 使用 Azure 数据工厂从 FTP 服务器移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从 FTP 服务器移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fe8982c9c4995cd9ddd6faa9a28fae1f321a2988
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623241"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 FTP 服务器移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-ftp-connector.md)
> * [版本 2 - 预览版](../connector-ftp.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 FTP 连接器](../connector-ftp.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动从 FTP 服务器移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，该文章总体概述了如何使用复制活动移动数据。

可以将数据从 FTP 服务器复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 FTP 服务器移至其他数据存储，但不支持将数据从其他数据存储移至 FTP 服务器。 它支持本地和云 FTP 服务器。

> [!NOTE]
> 复制活动在源文件成功复制到目标位置后不会删除该文件。 如果需要在成功复制后删除源文件，请创建自定义活动以删除该文件，并在管道中使用该活动。 

## <a name="enable-connectivity"></a>启用连接
如果要将数据从**本地** FTP 服务器移至云数据存储（例如移至 Azure Blob 存储），请安装并使用数据管理网关。 数据管理网关是安装在本地计算机上的客户端代理，支持将云服务连接到本地资源。 有关详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)。 有关设置和使用该网关的分步说明，请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。 即使 FTP 服务器位于 Azure 基础结构即服务 (IaaS) 的虚拟机 (VM) 上，也可使用网关连接到该服务器。

可将网关作为 FTP 服务器安装在同一本地计算机或 IaaS VM 上。 但是，我们建议将网关安装在单独的计算机或 IaaS VM 上，以避免资源争用，并获得更好的性能。 在单独的计算机上安装网关时，计算机应能够访问 FTP 服务器。

## <a name="get-started"></a>入门
可以使用不同的工具或 API 创建包含复制活动的管道（用于从 FTP 源移动数据）。

创建管道的最简单方法是使用**数据工厂复制向导**。 有关快速演练，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建将数据从源数据存储移至接收器数据存储的管道：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具或 API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。 有关用于从 FTP 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 FTP 服务器复制到 Azure blob](#json-example-copy-data-from-ftp-server-to-azure-blob) 部分。

> [!NOTE]
> 有关要使用的受支持文件和压缩格式的详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)。

以下部分提供了有关 JSON 属性的详细信息，这些属性用于定义特定于 FTP 的数据工厂实体。

## <a name="linked-service-properties"></a>链接服务属性
下表描述了特定于 FTP 链接服务的 JSON 元素。

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| type |将此类型设置为 FtpServer。 |是 |&nbsp; |
| host |指定 FTP 服务器的名称或 IP 地址。 |是 |&nbsp; |
| authenticationType |指定身份验证类型。 |是 |基本、匿名 |
| username |指定有权访问 FTP 服务器的用户。 |否 |&nbsp; |
| password |指定用户（用户名）的密码。 |否 |&nbsp; |
| encryptedCredential |指定访问 FTP 服务器的加密凭据。 |否 |&nbsp; |
| gatewayName |指定数据管理网关中用于连接到本地 FTP 服务器的网关名称。 |否 |&nbsp; |
| 端口 |指定 FTP 服务器侦听的端口。 |否 |21 |
| enableSsl |指定是否通过 SSL/TLS 通道使用 FTP。 |否 |是 |
| enableServerCertificateValidation |指定在通过 SSL/TLS 通道使用 FTP 时，是否启用服务器 SSL 证书验证。 |否 |是 |

>[!NOTE]
>FTP 连接器支持在不使用加密或使用显式 SSL/TLS 加密的情况下访问 FTP 服务器；不支持隐式 SSL/TLS 加密。

### <a name="use-anonymous-authentication"></a>使用匿名身份验证

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>使用纯文本用户名和密码进行基本身份验证

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>使用端口、enableSsl、enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>将 encryptedCredential 用于身份验证和网关

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的各个部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每个数据集类型的 **typeProperties** 节都不同。 它提供特定于数据集类型的信息。 **FileShare** 类型的数据集的 **typeProperties** 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 组合在一起，基于切片开始和结束的日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 **fileName**，生成文件的名称会采用以下格式： <br/><br/>Data.<Guid>.txt（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 **folderPath** 中选择一部分文件而不是所有文件时要使用的筛选器。<br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1：`"fileFilter": "*.log"`<br/>示例 2：`"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** 适用于 FileShare 输入数据集。 Hadoop 分布式文件系统 (HDFS) 不支持此属性。 |否 |
| partitionedBy |用于指定时序数据的动态 **folderPath** 和 **fileName**。 例如，可以指定每小时参数化以形成数据的 **folderPath**。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间按原样复制文件（二进制副本），可以在输入和输出数据集定义中跳过格式部分。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**；支持的级别为：**Optimal** 和 **Fastest**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |
| useBinaryTransfer |指定是否使用二进制传输模式。 这些值在二进制模式下为 true（这是默认值），在 ASCII 模式下为 false。 仅当关联的链接服务类型为 FtpServer 时，才可以使用此属性。 |否 |

> [!NOTE]
> **fileName** 和 **fileFilter** 不能同时使用。

### <a name="use-the-partionedby-property"></a>使用 partionedBy 属性
如上一部分所述，可以使用 **partitionedBy** 属性指定时序数据的动态 **folderPath** 和 **fileName**。

若要了解有关时序数据集、计划和切片的信息，请参阅[创建数据集](data-factory-create-datasets.md)、[计划和执行](data-factory-scheduling-and-execution.md)以及[创建管道](data-factory-create-pipelines.md)。

#### <a name="sample-1"></a>示例 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值，格式为指定的格式 (YYYYMMDDHH)。 SliceStart 指切片开始时间。 每个切片的文件夹路径不同。 （例如 wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。）

#### <a name="sample-2"></a>示例 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
在本示例中，SliceStart 的年、月、日和时间已提取到 **folderPath** 和 **fileName** 属性使用的各个变量。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各个部分和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于活动的 **typeProperties** 部分的属性因每个活动类型而异。 对于复制活动，类型属性因源和接收器类型而异。

在复制活动中，如果源的类型为 **FileSystemSource**，则 **typeProperties** 部分将具有以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON 示例：将数据从 FTP 服务器复制到 Azure Blob
此示例演示如何将数据从 FTP 服务器复制到 Azure Blob 存储。 但是，使用数据工厂中的复制活动，可以直接将数据复制到[支持的数据存储和格式](data-factory-data-movement-activities.md#supported-data-stores-and-formats)中所述的任何接收器。  

以下示例提供示例 JSON 定义，可用于通过使用 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道：

* [FtpServer](#linked-service-properties) 类型的链接服务
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务
* [FileShare](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)
* 包含使用 [FileSystemSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 的复制活动的[管道](data-factory-create-pipelines.md)

此示例每小时将数据从 FTP 服务器复制到 Azure blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

### <a name="ftp-linked-service"></a>FTP 链接服务

此示例使用通过纯文本用户名和密码进行的基本身份验证。 也可使用以下任一方式：

* 匿名身份验证
* 使用加密凭据的基本身份验证
* FTP over SSL/TLS (FTPS)

请参阅 [FTP 链接服务](#linked-service-properties)部分，了解可使用的各种身份验证。

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
    }
  }
}
```
### <a name="azure-storage-linked-service"></a>Azure 存储链接服务

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>FTP 输入数据集

此数据集是指 FTP 文件夹 `mysharedfolder` 和文件 `test.csv`。 该管道将文件复制到目标。

将“external”设置为“true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob 输出数据集

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据正在处理的切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>在管道中具有文件系统源和 blob 接收器的复制活动

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* 若要了解影响数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

* 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
