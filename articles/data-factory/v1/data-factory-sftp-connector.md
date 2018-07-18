---
title: 使用 Azure 数据工厂从 SFTP 服务器移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从本地或云 SFTP 服务器移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c22d2cba23e8bae965fa7c5746c9fff69ad3fa9e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054409"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>使用 Azure 数据工厂从 SFTP 服务器移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](data-factory-sftp-connector.md)
> * [版本 2（当前版本）](../connector-sftp.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果正在使用当前版本数据工厂服务，请参阅 [V2 中的 SFTP 连接器](../connector-sftp.md)。

本文概述如何在 Azure 数据工厂中使用复制活动将数据从本地/云 SFTP 服务器移至受支持的接收器数据存储。 本文建立在 [数据移动活动](data-factory-data-movement-activities.md)一文的基础上，该文提供通过复制活动和一系列支持作为源/接收器的数据存储进行数据移动的一般概述。

数据工厂当前仅支持将数据从 SFTP 服务器移至其他数据存储，但不可将数据从其他数据存储移至 SFTP 服务器。 它支持本地和云 SFTP 服务器。

> [!NOTE]
> 复制活动在将源文件成功复制到目标后不会删除该文件。 如果需要在成功复制后删除源文件，请创建一个自定义活动，以便删除该文件并在管道中使用复制活动。 

## <a name="supported-scenarios-and-authentication-types"></a>支持的方案和身份验证类型
可以使用此 SFTP 连接器从**云 SFTP 服务器和本地 SFTP 服务器**复制数据。 连接到 SFTP 服务器时，支持**基本**和 **SshPublicKey** 身份验证类型。

从本地 SFTP 服务器复制数据时，需要在本地环境/Azure VM 中安装数据管理网关。 请参阅[数据管理网关](data-factory-data-management-gateway.md)，了解有关网关的详细信息。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解设置和使用网关的分步说明。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从 SFTP 源移动数据。

- 创建管道的最简单方法是使用复制向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

- 也可以使用以下工具创建管道：Azure 门户、Visual Studio、Azure PowerShell、Azure 资源管理器模板、.NET API 和 REST API。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 有关将数据从 SFTP 服务器复制到 Azure Blob 存储的 JSON 示例，请参阅本文的 [JSON 示例：将数据从 SFTP 服务器复制到 Azure Blob](#json-example-copy-data-from-sftp-server-to-azure-blob) 部分。

## <a name="linked-service-properties"></a>链接服务属性
下表提供 FTP 链接服务专属的 JSON 元素描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| type | type 属性必须设置为 `Sftp`。 |是 |
| host | SFTP 服务器的名称或 IP 地址。 |是 |
| port |SFTP 服务器侦听的端口。 默认值为 21 |否 |
| authenticationType |指定身份验证类型。 允许的值：**Basic**、**SshPublicKey**。 <br><br> 有关其他属性和 JSON 示例，请分别参阅[使用基本身份验证](#using-basic-authentication)和[使用 SSH 公钥身份验证](#using-ssh-public-key-authentication)部分。 |是 |
| skipHostKeyValidation | 指定是否要跳过主机密钥验证。 | 不是。 默认值：false |
| hostKeyFingerprint | 指定主机密钥的指纹。 | `skipHostKeyValidation` 设置为 false 时表示 Yes。  |
| gatewayName |用于连接本地 SFTP 服务器的数据管理网关的名称。 | 如果从本地 SFTP 服务器复制数据，则值为 Yes。 |
| encryptedCredential | 访问 SFTP 服务器的加密凭据 在复制向导或 ClickOnce 弹出对话框中指定基本身份验证（用户名 + 密码）或 SshPublicKey 身份验证（用户名 + 私钥路径或内容）时自动生成。 | 不是。 仅当从本地 SFTP 服务器复制数据时才适用。 |

### <a name="using-basic-authentication"></a>使用基本身份验证

要使用基本身份验证，请将 `authenticationType` 设置为 `Basic`，并指定除上一部分所述 SFTP 连接器泛型属性以外的下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| username | 有权访问 SFTP 服务器的用户。 |是 |
| password | 用户 (username) 的密码。 | 是 |

#### <a name="example-basic-authentication"></a>示例：基本身份验证
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>示例：使用包含加密凭据的基本身份验证

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>使用 SSH 公钥身份验证

若要使用 SSH 公钥身份验证，请将 `authenticationType` 设置为`SshPublicKey`，并且除上一部分所述 SFTP 连接器泛型属性外，还需指定下列属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- | --- |
| username |有权访问 SFTP 服务器的用户 |是 |
| privateKeyPath | 指定网关可以访问的私钥文件的绝对路径。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 <br><br> 仅当从本地 SFTP 服务器复制数据时才适用。 |
| privateKeyContent | 私钥内容的序列化字符串。 复制向导可以读取私钥文件，并自动提取私钥的内容。 如果使用其他任何工具/SDK，请改用 privateKeyPath 属性。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果密钥文件受通行短语保护，请指定用于解密私钥的通行短语/密码。 | 如果私钥文件受通行短语的保护，则为 Yes。 |

> [!NOTE]
> SFTP 连接器支持 RSA/DSA OpenSSH 密钥。 请确保密钥文件内容以“-----BEGIN [RSA/DSA] PRIVATE KEY-----”开头。 如果私钥文件是 ppk 格式文件，请使用 Putty 工具从 .ppk 转换为 OpenSSH 格式。

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>示例：使用私钥 filePath 进行 SshPublicKey 身份验证

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>示例：使用私钥内容进行 SshPublicKey 身份验证

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每个数据集类型的 **typeProperties** 节都不同。 它提供特定于数据集类型的信息。 **FileShare** 数据集类型的 typeProperties 节具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式： <br/><br/>Data.<Guid>.txt（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。<br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1：`"fileFilter": "*.log"`<br/>示例 2：`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter 适用于 FileShare 输入数据集。 HDFS 不支持此属性。 |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |
| useBinaryTransfer |指定是否使用二进制传输模式。 使用二进制模式时为 true，使用 ASCII 时为 false。 默认值：True。 仅当关联的链接服务类型为 FtpServer 时，才可以使用此属性。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。

### <a name="using-partionedby-property"></a>使用 partionedBy 属性
如上一部分所述，可以使用 partitionedBy 指计时序数据的动态 folderPath 和 filename。 为此，可以使用数据工厂宏和系统变量 SliceStart 与 SliceEnd（表示给定数据切片的逻辑时间段）。

若要了解有关时序数据集、计划和切片的信息，请参阅文章 [Creating Datasets](data-factory-create-datasets.md)（创建数据集）、[Scheduling & Execution](data-factory-scheduling-and-execution.md)（计划和执行）及 [Creating Pipelines](data-factory-create-pipelines.md)（创建管道）。

#### <a name="sample-1"></a>示例 1：

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值（使用指定的格式 (YYYYMMDDHH)）。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 示例：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>示例 2：

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
在本示例中，SliceStart 的年、月、日和时间已提取到 folderPath 和 fileName 属性使用的各个变量。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，其类型属性因源和接收器类型而异。

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>支持的文件和压缩格式
请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)一文了解详细信息。

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON 示例：将数据从 SFTP 服务器复制到 Azure Blob
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示了如何将数据从 SFTP 源复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

> [!IMPORTANT]
> 此示例提供 JSON 代码段。 它不包括创建数据工厂的分步说明。 请参阅文章[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)以获取分步说明。

此示例具有以下数据工厂实体：

* [sftp](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [FileShare](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 SFTP 服务器复制到 Azure Blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**SFTP 链接服务**

此示例使用通过纯文本用户名和密码进行的基本身份验证。 也可使用以下任一方式：

* 使用加密凭据的基本身份验证
* SSH 公钥身份验证

请参阅 [FTP 链接服务](#linked-service-properties)一节，了解各种可使用的身份验证。

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure 存储链接服务**

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
**SFTP 输入数据集**

此数据集引用 SFTP 文件夹 `mysharedfolder` 和文件 `test.csv`。 该管道将文件复制到目标。

设置 "external": "true" 将告知数据工厂服务：数据集位于数据工厂外且不由数据工厂中的活动生成。

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob 输出数据集**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**包含复制活动的管道**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，了解创建包含复制活动的管道的分步说明。
