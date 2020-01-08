---
title: Azure 数据工厂中支持的文件格式
description: 本主题说明 Azure 数据工厂中基于文件的连接器支持的文件格式和压缩代码。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439577"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Azure 数据工厂中支持的文件格式和压缩编解码器

*本文适用于以下连接器： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您可以使用[复制活动](copy-activity-overview.md)在两个基于文件的数据存储之间按原样复制文件，在这种情况下，将在不进行任何序列化或反序列化的情况下有效地复制数据。 

此外，还可以分析或生成给定格式的文件。 例如，你可以执行以下操作：

* 复制本地 SQL Server 数据库中的数据，并以 Parquet 格式写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本（CSV）格式的文件，并使用 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统中复制压缩文件，将其解压缩，然后将提取的文件写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本（CSV）格式的数据，并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的更多活动。

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)
