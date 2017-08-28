---
title: "使用脱机方法上传大量数据到 Data Lake Store | Microsoft Docs"
description: "使用 AdlCopy 工具将数据从 Azure 存储 blob 复制到 Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b469c0ebe9838a1ea986cff3043e3008941e9aa9
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>使用 Azure 导入/导出服务将数据脱机复制到 Data Lake Store
本文介绍如何使用脱机复制方法（例如 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)）将大型数据集 (>200 GB) 复制到 Azure Data Lake Store。 具体而言，本文中用作示例的文件大小为 339,420,860,416 字节，即约 319GB 磁盘空间。 命名此文件为 319GB.tsv。

使用 Azure 导入/导出服务，可以将硬盘驱动器传送到 Azure 数据中心，从而安全地将大量数据传输到 Azure Blob 存储。

## <a name="prerequisites"></a>先决条件
在开始之前，必须满足以下条件：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 存储帐户**。
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>准备数据
使用导入/导入服务前，请将要传输的数据文件拆分为**小于 200GB 大小的副本**。 导入工具无法处理大于 200GB 的文件。 在本教程中，我们将文件拆分成多个块，每个 100 GB。 可使用 [Cygwin](https://cygwin.com/install.html) 实现此目的。 Cygwin 支持 Linux 命令。 在这种情况下，使用以下命令：

    split -b 100m 319GB.tsv

此拆分操作会创建具有以下名称的文件。

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>准备好数据可使用的磁盘
遵循[使用 Azure 导入/导出服务](../storage/common/storage-import-export-service.md)中的说明（“准备驱动器”部分下面）准备硬盘。 下面是整个步骤：

1. 购买满足用于 Auzre 导入/导出服务要求的硬盘。
2. 数据发送到 Azure 数据中心后，确认数据要复制到的 Azure 存储帐户。
3. 使用命令行实用程序 [Azure 导入/导出工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)。 下面是有关如何使用该工具的示例代码段。

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    请参阅[使用 Azure 导入/导出服务](../storage/common/storage-import-export-service.md)查看更多示例代码片段。
4. 上面的命令会在指定位置创建日志文件。 之后使用此日志文件从 [Azure 经典门户](https://manage.windowsazure.com)创建导入作业。

## <a name="create-an-import-job"></a>创建导入作业
现在可按照使用 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)（**创建导入作业**部分之下）中的说明创建导入作业。 对于此导入作业，除其他详细信息外，还提供准备磁盘驱动器时创建的日志文件。

## <a name="physically-ship-the-disks"></a>物理发送磁盘
现在可以物理方式将磁盘发送到 Azure 数据中心。 随后，会在 Azure 数据中心将数据复制到创建此导入作业时提供的 Azure 存储 blob 中。 此外，创建此作业时，如果选择了稍后提供跟踪信息，则现在可返回到导入作业并更新跟踪号。

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>将数据从 Azure 存储 blob 复制到 Azure Data Lake Store
导入作业状态显示已完成后，可验证此数据在先前指定的 Azure 存储 Blob 中是否可用。 然后可使用多种方法从 Blob 将数据移动到 Azure Data Lake Store。 有关上传数据的所有可用选项，请参阅[引入数据到 Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store)。

本部分提供 JSON 定义，此定义可用于创建进行复制数据的 Azure 数据工厂管道。 可从 [Azure 门户](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md) 使用这些 JSON 定义。

### <a name="source-linked-service-azure-storage-blob"></a>源链接服务（Azure 存储 Blob）
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>目标链接服务（Azure Data Lake Store）
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>输入数据集
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>输出数据集
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>管道（复制活动）
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
有关详细信息，请参阅[使用 Azure 数据工厂将数据从从 Azure 存储 Blob 移动到 Data Lake Store](../data-factory/data-factory-azure-datalake-connector.md)。

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>重新构造 Azure Data Lake Store 中的数据文件
以 319GB 大小的文件开始，将其拆分为多个更小的文件，以便可使用 Azure 导入/导出服务进行传输。 此数据已在 Azure Data Lake Store 中，因此现在可将文件重构为其原始大小。 为此，可使用以下 Azure PowerShell cmldt。

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

