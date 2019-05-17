---
title: 对 Azure Data Box 上的问题进行故障排除 |Microsoft Docs
description: 介绍如何将数据上载到 Azure 时，在 Azure Data Box 中看到的问题进行疑难解答。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/13/2019
ms.author: alkohli
ms.openlocfilehash: 1126002a93419371be3216c55114385c9c600419
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594000"
---
# <a name="troubleshoot-issues-related-to-azure-data-box"></a>对 Azure Data Box 到相关的问题进行故障排除

本文详细介绍了使用 Azure Data Box 时可能会看到如何对问题进行故障排除信息。

## <a name="errors-during-data-copy"></a>数据复制过程中出现错误

以下各节中总结了在数据复制期间出现的所有错误。

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH 

错误说明：容器或共享名称必须包含 3 到 63 个字符。

**建议的解决方法：** 数据已复制到其中的数据框 (SMB/NFS) 共享下的文件夹将成为你的存储帐户中的 Azure 容器。 

- 上**连接和复制**页的数据框本地 web UI、 下载和查看问题的错误文件，以确定该文件夹名称。
- 更改，确保在数据框共享下的文件夹名称：

    - 名称必须介于 3 到 63 个字符之间。
    - 名称只能字母、 数字和连字符。
    - 名称不能启动，或以连字符结尾。
    - 名称不能有连续的连字符。
    - 有效名称的示例： `my-folder-1`， `my-really-extra-long-folder-111`。
    - 不是有效的名称的示例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    有关详细信息，请参阅为 Azure 命名约定[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)并[共享名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

错误说明：容器或共享名称必须包含 3 到 63 个字符。 

**建议的解决方法：** 数据已复制到其中的数据框 (SMB/NFS) 共享下的文件夹将成为你的存储帐户中的 Azure 容器。 

- 上**连接和复制**页的数据框本地 web UI、 下载和查看问题的错误文件，以确定该文件夹名称。
- 更改，确保在数据框共享下的文件夹名称：

    - 名称必须介于 3 到 63 个字符之间。
    - 名称只能字母、 数字和连字符。
    - 名称不能启动，或以连字符结尾。
    - 名称不能有连续的连字符。
    - 有效名称的示例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名称的示例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    有关详细信息，请参阅为 Azure 命名约定[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)并[共享名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

错误说明：容器或共享名称只能包含字母、数字或连字符。

**建议的解决方法：** 数据已复制到其中的数据框 (SMB/NFS) 共享下的文件夹将成为你的存储帐户中的 Azure 容器。 

- 上**连接和复制**页的数据框本地 web UI、 下载和查看问题的错误文件，以确定该文件夹名称。
- 更改，确保在数据框共享下的文件夹名称：

    - 名称必须介于 3 到 63 个字符之间。
    - 名称只能字母、 数字和连字符。
    - 名称不能启动，或以连字符结尾。
    - 名称不能有连续的连字符。
    - 有效名称的示例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名称的示例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    有关详细信息，请参阅为 Azure 命名约定[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)并[共享名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

错误说明：容器名称和共享名称不能启动或连字符结尾，并且不能有连续的连字符。

**建议的解决方法：** 数据已复制到其中的数据框 (SMB/NFS) 共享下的文件夹将成为你的存储帐户中的 Azure 容器。 

- 上**连接和复制**页的数据框本地 web UI、 下载和查看问题的错误文件，以确定该文件夹名称。
- 更改，确保在数据框共享下的文件夹名称：

    - 名称必须介于 3 到 63 个字符之间。
    - 名称只能字母、 数字和连字符。
    - 名称不能启动，或以连字符结尾。
    - 名称不能有连续的连字符。
    - 有效名称的示例： `my-folder-1`， `my-really-extra-long-folder-111`
    - 不是有效的名称的示例： `my-folder_1`， `my`， `--myfolder`， `myfolder--`， `myfolder!`

    有关详细信息，请参阅为 Azure 命名约定[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)并[共享名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)。

### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

错误说明：为托管的磁盘的共享已指定了不正确的容器名称。

**建议的解决方法：** 对于托管磁盘，每个共享中对应于你的存储帐户中的容器创建以下文件夹：高级 SSD、 标准 HDD 和 SSD 标准。 这些文件夹对应于托管磁盘的性能层。

- 请确保将页 blob 数据 (Vhd) 复制到其中一个现有文件夹。 仅从这些现有容器的数据上载到 Azure。
- 在高级 SSD、 标准 HDD 和 SSD 标准与相同的级别创建的任何其他文件夹不对应于有效的性能层，并且不能使用。
- 删除文件或文件夹在性能层之外创建的。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

错误说明：Azure 文件共享限制为 5 TB 数据的共享。 对于某些共享已超出此限制。

**建议的解决方法：** 上**连接和复制**页的数据框本地 web UI 中，下载，并查看的错误文件。

确定具有错误日志中的此问题并确保该文件夹中的文件超过 5 TB 的文件夹。

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

错误说明：Blob 或文件名称包含不受支持的控制字符。

**建议的解决方法：** Blob 或复制的文件包含具有不支持的字符的名称。

上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
删除或重命名这些文件以便删除不支持的字符。

有关详细信息，请参阅为 Azure 命名约定[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)并[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

错误说明：Blob 或文件名称包含非法字符。

**建议的解决方法：** Blob 或复制的文件包含具有不支持的字符的名称。

上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
删除或重命名这些文件以便删除不支持的字符。

有关详细信息，请参阅为 Azure 命名约定[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)并[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

错误说明：Blob 或文件名称包含错误字符结束。

**建议的解决方法：** Blob 或复制的文件包含具有不支持的字符的名称。

上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
删除或重命名这些文件以便删除不支持的字符。

有关详细信息，请参阅为 Azure 命名约定[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)并[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)。


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

错误说明：Blob 或文件名称包含太多路径段。

**建议的解决方法：** 已复制的文件或 blob 超出路径段的最大数目。 路径段指相邻分隔符，例如，正斜杠之间的字符串 /。

- 上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
- 请确保[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)并[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

错误说明：Blob 或文件名太长。

**建议的解决方法：** Blob 或文件名称超过最大长度。

- 上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
- Blob 名称必须不超过 1,024 个字符。
- 删除或重命名 blob 或文件，以便名称不能超过 1024年个字符。

有关详细信息，请参阅 blob 名称和文件名称的 Azure 命名约定。

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

错误说明：Blob 或文件名中的某个段太长。

**建议的解决方法：** 一个路径段中的 blob 或文件名称超出了最大字符数。 路径段指相邻分隔符，例如，正斜杠之间的字符串 /。

- 上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
- 请确保[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)并[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

错误说明：文件大小超过了最大上传文件大小。

**建议的解决方法：** Blob 或文件大小超过允许上传的最大限制。

- 上**连接和复制**页上的本地 web UI、 下载和查看的错误文件。
- 请确保 blob 和文件大小不超过 Azure 对象大小限制。

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

错误说明：Blob 或文件未正确对齐。

**建议的解决方法：** 为 512 个字节的数据框中仅支持文件上的页 blob 共享对齐 （例如 VHD/VHDX）。 复制到页 blob 共享任何数据作为页 blob 上载到 Azure。

从页 blob 共享中删除 VHD/VHDX 的任何数据。 对于块 blob 或 Azure 文件来存储泛型数据，可以使用共享。

有关详细信息，请参阅[概述的页 blob](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

错误说明：不支持的文件类型中不存在托管的磁盘共享。 允许的唯一固定的 Vhd。

**建议的解决方法：**

- 请确保您仅上传固定的 Vhd 创建托管的磁盘。
- VHDX 文件或**动态**并**差异**Vhd 不受支持。

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

错误说明：目录不为托管磁盘允许在任何预先存在的文件夹中。 唯一的固定的 Vhd 可在这些文件夹。

**建议的解决方法：** 对于托管磁盘，每个共享中对应于你的存储帐户中的容器创建以下三个文件夹：高级 SSD、 标准 HDD 和 SSD 标准。 这些文件夹对应于托管磁盘的性能层。

- 请确保将页 blob 数据 (Vhd) 复制到其中一个现有文件夹。
- 文件夹或目录不允许在这些现有的文件夹。 请删除这些预先存在的文件夹中创建的任何文件夹。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>后续步骤

- 了解如何[数据框 Blob 存储的系统要求](data-box-system-requirements-rest.md)。
