---
title: "从 FTP 服务器移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从 FTP 服务器移动数据。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>使用 Azure 数据工厂从 FTP 服务器移动数据
本文概述了如何在 Azure 数据工厂中使用复制活动，将数据从 FTP 服务器移至受支持的接收器数据存储。 本文建立在 [数据移动活动](data-factory-data-movement-activities.md)一文的基础上，该文提供通过复制活动和一系列支持作为源/接收器的数据存储进行数据移动的一般概述。

数据工厂当前仅支持将数据从 FTP 服务器移至其他数据存储，但不可将数据从其他数据存储移至 FTP 服务器。 它支持本地和云 FTP 服务器。

如果要将数据从**本地** FTP 服务器移至云数据存储（示例：Azure Blob 存储），则安装并使用数据管理网关。 数据管理网关是安装在本地计算机的客户端代理，允许将云服务连接到本地资源。 请参阅[数据管理网关](data-factory-data-management-gateway.md)，了解有关网关的详细信息。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解设置和使用网关的分步说明。 即使 FTP 服务器位于 Azure IaaS 虚拟机 (VM) 上，也可使用网关连接到它。

可将网关作为 FTP 服务器安装于同一本地计算机或 Azure IaaS VM 上。 但是，建议将网关安装于单独的计算机或单独的 Azure IaaS VM 上，避免资源争用并获得更好的性能。 在单独的计算机上安装网关时，计算机应能够访问 FTP 服务器。

## <a name="copy-data-wizard"></a>复制数据向导
要创建从 FTP 服务器复制数据的管道，最简单的方法是使用复制数据向导。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>示例：将数据从 FTP 服务器复制到 Azure blob
此示例演示如何将数据从 FTP 服务器复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例具有以下数据工厂实体：

* [FtpServer](#ftp-linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
* [FileShare](#fileshare-dataset-type-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#ftp-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每小时将数据从 FTP 服务器复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

**FTP 链接服务** 此示例使用通过纯文本用户名和密码进行的基本身份验证。 也可使用以下任一方式：

* 匿名身份验证
* 使用加密凭据的基本身份验证
* FTP over SSL/TLS (FTPS)

请参阅 [FTP 链接服务](#ftp-linked-service-properties)一节，了解各种可使用的身份验证。

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
**FTP 输入数据集** 此数据集指 FTP 文件夹 `mysharedfolder` 和文件 `test.csv`。 该管道将文件复制到目标。

设置 "external": "true" 将告知数据工厂服务：数据集位于数据工厂外且不由数据工厂中的活动生成。

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

**Azure Blob 输出数据集**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

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


**包含复制活动的管道**

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

## <a name="ftp-linked-service-properties"></a>FTP 链接服务属性
下表提供 FTP 链接服务专属的 JSON 元素描述。

| 属性 | 说明 | 必选 | 默认 |
| --- | --- | --- | --- |
| type |type 属性必须设置为 FtpServer |是 |&nbsp; |
| 主机 |FTP 服务器的名称或 IP 地址 |是 |&nbsp; |
| authenticationType |指定身份验证类型 |是 |基本、匿名 |
| username |有权访问 FTP 服务器的用户 |否 |&nbsp; |
| password |用户 (username) 的密码 |否 |&nbsp; |
| encryptedCredential |访问 FTP 服务器的加密凭据 |否 |&nbsp; |
| gatewayName |用于连接本地 FTP 服务器的数据管理网关名称 |否 |&nbsp; |
| 端口 |FTP 服务器侦听的端口 |否 |21 |
| enableSsl |指定是否使用 FTP over SSL/TLS 通道 |否 |是 |
| enableServerCertificateValidation |指定使用 FTP over SSL/TLS 通道时，是否启用服务器 SSL 证书验证 |否 |是 |

### <a name="using-anonymous-authentication"></a>使用匿名身份验证

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

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>使用纯文本用户名和密码进行基本身份验证

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

### <a name="using-port-enablessl-enableservercertificatevalidation"></a>使用端口、enableSsl、enableServerCertificateValidation
 
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

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>将 encryptedCredential 用于身份验证和网关
    
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

请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)，了解有关设置本地 FTP 数据源凭据的详细信息。

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>FTP 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，其类型属性因源和接收器类型而异。

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)，了解创建包含复制活动的管道的分步说明。



<!--HONumber=Nov16_HO3-->


