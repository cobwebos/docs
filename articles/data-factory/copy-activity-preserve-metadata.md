---
title: 使用 Azure 数据工厂中的复制活动保留元数据和 ACL
description: 了解如何使用 Azure 数据工厂中的复制活动在复制期间保留元数据和 ACL。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: jingwang
ms.openlocfilehash: a1527195296237eb8c9c309f8ac4a5911136cf77
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891755"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure 数据工厂中的复制活动保留元数据和 ACL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

当使用 Azure 数据工厂复制活动将数据从源复制到接收器时，在以下情况下，还可以保留元数据和 ACL。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 保留数据湖迁移的元数据

在将数据从一个数据湖迁移到另一个数据湖（包括 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md) 和 [Azure data lake Storage Gen2](connector-azure-data-lake-storage.md)）时，可以选择保留文件元数据和数据。

复制活动支持在数据复制期间保留以下属性：

- **所有客户指定的元数据** 
- 以及以下五个数据存储内置系统属性： **、** （Amazon S3 除外）、`contentType`、`contentLanguage`、`contentEncoding``contentDisposition``cacheControl`。

**处理元数据的差异：** Amazon S3 和 Azure 存储允许在客户指定的元数据的密钥中设置不同的字符集。 当你选择使用 "复制活动" 保存元数据时，ADF 会自动将无效字符替换为 "_"。

按原样将文件从 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob 复制到二进制格式的 Azure Data Lake Storage Gen2/Azure Blob 时，可以在用于活动创作的“复制活动” > “设置”选项卡或复制数据工具的“设置”页上找到“保留”选项****************。

![复制活动保留元数据](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>将 Acl 从 Data Lake Storage Gen1/Gen2 保留到 Gen2

从 Azure Data Lake Storage Gen1 升级到 Gen2 或在 ADLS Gen2 之间复制数据时，可以选择保留 POSIX 访问控制列表（Acl）以及数据文件。 有关访问控制的详细信息，请参阅 Azure Data Lake Storage Gen2 中 Azure Data Lake Storage Gen1 和[access control](../storage/blobs/data-lake-storage-access-control.md)中的[访问控制](../data-lake-store/data-lake-store-access-control.md)。

复制活动支持在数据复制期间保留以下类型的 Acl。 您可以选择一个或多个类型：

- **ACL**：复制并保留对文件和目录的 POSIX 访问控制列表。 它将完整的现有 Acl 从源复制到接收器。 
- **所有者**：复制并保留文件和目录的拥有用户。 需要超级用户访问接收器 Data Lake Storage Gen2。
- **组**：复制并保留所属的文件和目录组。 需要超级用户对接收器 Data Lake Storage Gen2 或拥有用户的访问权限（如果拥有用户也是目标组的成员）。

如果指定从文件夹复制，则如果`recursive`将设置为 true，则数据工厂将复制该给定文件夹及其下的文件和目录的 acl。 如果指定从单个文件复制，则会复制该文件上的 Acl。

>[!NOTE]
>使用 ADF 将 Acl 从 Data Lake Storage Gen1/Gen2 保留到 Gen2 时，接收器上的现有 Acl Gen2's 对应的文件夹/文件将被覆盖。

>[!IMPORTANT]
>选择保留 Acl 时，请确保为数据工厂授予足够高的权限，以便在接收器 Data Lake Storage Gen2 帐户上运行。 例如，使用帐户密钥身份验证，或将存储 Blob 数据所有者角色分配给服务主体或托管标识。

当你将源配置为具有二进制格式或二进制复制选项的源作为 Data Lake Storage Gen1/Gen2，并以二进制格式或二进制复制选项接收 Data Lake Storage Gen2 时，你可以在复制数据工具中的 "**设置**" 页上或在活动创作的 "**复制活动** > **设置**" 选项卡上找到**保留**选项。

![Data Lake Storage Gen1/Gen2 到 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

下面是复制活动 JSON 配置的示例（请参阅 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤

请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)
