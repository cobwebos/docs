---
title: 排查 Azure Data Box 和 Azure Data Box Heavy 的问题 | Microsoft Docs
description: 介绍如何排查在将数据复制到 Azure Data Box 和 Azure Data Box Heavy 时这些设备出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439401"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>排查 Azure Data Box 和 Azure Data Box Heavy 的相关问题

本文详细介绍了使用 Azure Data Box 或 Azure 数据框高时可能会看到如何对问题进行故障排除信息。 此文章包括可能的数据复制到数据框或从数据框中上传数据时，看到的错误的列表。

## <a name="error-classes"></a>错误类

下面总结了中的数据框和数据框大量的错误：

| 错误类别 *        | 描述        | 建议的操作    |
|----------------------------------------------|---------|--------------------------------------|
| 容器或共享名称 | 容器或共享名称未遵循 Azure 命名规则。  |下载错误列表。 <br> 重命名的容器或共享。 [了解详细信息](#container-or-share-name-errors)。  |
| 容器或共享的大小限制 | 容器或共享中的总数据超出了 Azure 的限制。   |下载错误列表。 <br> 减少的容器或共享中的总体数据。 [了解详细信息](#container-or-share-size-limit-errors)。|
| 对象或文件大小限制 | 对象或容器或共享中的文件超过了 Azure 的限制。|下载错误列表。 <br> 减小文件大小的容器或共享中。 [了解详细信息](#object-or-file-size-limit-errors)。 |    
| 数据或文件类型 | 不支持的数据格式或文件类型。 |下载错误列表。 <br> 对于页 blob 或托管的磁盘，请确保数据为 512 字节对齐，复制到预创建的文件夹。 [了解详细信息](#data-or-file-type-errors)。 |
| 非关键的 blob 或文件错误  | Blob 或文件名称不遵循 Azure 命名规则或不支持的文件类型。 | 可能不会复制这些 blob 或文件，或者可能更改名称。 [了解如何修复这些错误](#non-critical-blob-or-file-errors)。 |

\* 第四条错误类别严重错误并就可以继续进行准备交付之前，必须修复。


## <a name="container-or-share-name-errors"></a>容器或共享名称错误

这些是容器和共享名称与相关的错误。

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

 错误说明：容器或共享名称必须包含 3 到 63 个字符。 

**建议的解决方法：** Data Box 或 Data Box Heavy 共享 (SMB/NFS) 中的、将数据复制到的文件夹会成为存储帐户中的一个 Azure 容器。 

- 在设备本地 Web UI 的“连接和复制”页上，下载并查看错误文件，以找出有问题的文件夹名称。 
- 更改 Data Box 或 Data Box Heavy 共享中的文件夹名称，以确保：

    - 名称包含 3 到 63 个字符。
    - 名称只能包含字母、数字和连字符。
    - 名称不能以连字符开头或结尾。
    - 名称不能包含连续的连字符。
    - 有效名称的示例：`my-folder-1`、`my-really-extra-long-folder-111`
    - 无效名称的示例：`my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    有关详细信息，请参阅[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共享名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名约定。


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

 错误说明：容器或共享名称只能包含字母、数字或连字符。

**建议的解决方法：** Data Box 或 Data Box Heavy 共享 (SMB/NFS) 中的、将数据复制到的文件夹会成为存储帐户中的一个 Azure 容器。 

- 在设备本地 Web UI 的“连接和复制”页上，下载并查看错误文件，以找出有问题的文件夹名称。 
- 更改 Data Box 或 Data Box Heavy 共享中的文件夹名称，以确保：

    - 名称包含 3 到 63 个字符。
    - 名称只能包含字母、数字和连字符。
    - 名称不能以连字符开头或结尾。
    - 名称不能包含连续的连字符。
    - 有效名称的示例：`my-folder-1`、`my-really-extra-long-folder-111`
    - 无效名称的示例：`my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    有关详细信息，请参阅[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共享名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名约定。

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

 错误说明：容器名称和共享名称不能以连字符开头或结尾，并且不能包含连续的连字符。

**建议的解决方法：** Data Box 或 Data Box Heavy 共享 (SMB/NFS) 中的、将数据复制到的文件夹会成为存储帐户中的一个 Azure 容器。 

- 在设备本地 Web UI 的“连接和复制”页上，下载并查看错误文件，以找出有问题的文件夹名称。 
- 更改 Data Box 或 Data Box Heavy 共享中的文件夹名称，以确保：

    - 名称包含 3 到 63 个字符。
    - 名称只能包含字母、数字和连字符。
    - 名称不能以连字符开头或结尾。
    - 名称不能包含连续的连字符。
    - 有效名称的示例：`my-folder-1`、`my-really-extra-long-folder-111`
    - 无效名称的示例：`my-folder_1`、`my`、`--myfolder`、`myfolder--`、`myfolder!`

    有关详细信息，请参阅[容器名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)和[共享名称](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)的 Azure 命名约定。

## <a name="container-or-share-size-limit-errors"></a>容器或共享的大小限制错误

这些是与数据超出允许在容器或共享中的数据大小相关的错误。

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

 错误说明：Azure 文件共享将单个共享限制为 5 TB 数据。 某些共享已超过此限制。

**建议的解决方法：** 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 

在错误日志中找到存在此问题的文件夹，并确保该文件夹中的文件小于 5 TB。


## <a name="object-or-file-size-limit-errors"></a>对象或文件大小限制错误

这些是与数据对象或允许在 Azure 中的文件的最大大小超过相关的错误。 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

 错误说明：文件大小超过了最大上传文件大小。

**建议的解决方法：** Blob 或文件大小超过了允许的最大上传限制。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
- 确保 Blob 和文件大小不超过 Azure 对象大小限制。

## <a name="data-or-file-type-errors"></a>数据或文件类型错误

这些是与不支持的文件类型或数据类型的容器或共享中找到相关的错误。 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

 错误说明：Blob 或文件未正确对齐。

**建议的解决方法：** Data Box 或 Data Box Heavy 上的页 Blob 共享仅支持经过 512 字节对齐的文件（例如 VHD/VHDX）。 复制到页 Blob 共享的任何数据将作为页 Blob 上传到 Azure。

从页 Blob 共享中删除任何非 VHD/VHDX 数据。 可以使用块 Blob 或 Azure 文件的共享还存储一般数据。

有关详细信息，请参阅[页 Blob 概述](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

 错误说明：托管磁盘共享中存在不受支持的文件类型。 只允许固定的 VHD。

**建议的解决方法：**

- 确保仅上传固定的 VHD 来创建托管磁盘。
- 不支持 VHDX 文件或者**动态** VHD 和**差异** VHD。

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

 错误说明：托管磁盘的任何现有文件夹中不允许某个目录。 这些文件夹中只允许固定的 VHD。

**建议的解决方法：** 对于托管磁盘，会在每个共享中创建对应于存储帐户中的容器的以下三个文件夹：高级 SSD、标准 HDD 和标准 SSD。 这些文件夹对应于托管磁盘的性能层。

- 确保将页 Blob 数据 (VHD) 复制到其中一个现有文件夹。
- 这些现有的文件夹中不允许某个文件夹或目录。 请删除在这些现有文件夹中创建的所有文件夹。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

 错误说明：在 Linux 中不允许的符号链接。 

**建议的解决方法：** 链接、 管道和其他此类文件，通常是符号链接。 删除链接，或解析链接，然后将数据复制。


## <a name="non-critical-blob-or-file-errors"></a>非关键的 blob 或文件错误

以下部分汇总了复制数据期间会出现的所有错误。

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

 错误说明：Blob 或文件的名称包含不受支持的控制字符。

**建议的解决方法：** 复制的 Blob 或文件的名称包含不受支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

 错误说明：Blob 或文件的名称包含非法字符。

**建议的解决方法：** 复制的 Blob 或文件的名称包含不受支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

 错误说明：Blob 或文件的名称以错误的字符结尾。

**建议的解决方法：** 复制的 Blob 或文件的名称包含不受支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

 错误说明：Blob 或文件的名称包含过多的路径段。

**建议的解决方法：** 复制的文件或 Blob 超过了最大路径段数。 路径段是指连续分隔符（例如正斜杠 /）之间的字符串。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
- 确保 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

 错误说明：Blob 或文件名太长。

**建议的解决方法：** Blob 或文件的名称超过了最大长度。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
- Blob 名称不得超过 1,024 个字符。
- 删除或者重命名 Blob 或文件，使其名称不超过 1,024 个字符。

有关详细信息，请参阅 Blob 名称和文件名的 Azure 命名约定。

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

 错误说明：Blob 或文件名中的某个段太长。

**建议的解决方法：** Blob 或文件名称中的某个路径段超过了最大字符数。 路径段是指连续分隔符（例如正斜杠 /）之间的字符串。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
- 确保 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

 错误说明：为托管磁盘共享指定的容器名称不正确。

**建议的解决方法：** 对于托管磁盘，会在每个共享中创建对应于存储帐户中的容器的以下文件夹：高级 SSD、标准 HDD 和标准 SSD。 这些文件夹对应于托管磁盘的性能层。

- 确保将页 Blob 数据 (VHD) 复制到其中一个现有文件夹。 只有这些现有容器中的数据才会上传到 Azure。
- 在与高级 SSD、标准 HDD 和标准 SSD 相同的级别创建的任何其他文件夹不对应于有效的性能层，因此不能使用。
- 删除在性能层外部创建的文件或文件夹。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>后续步骤

- 了解 [Data Box Blob 存储系统要求](data-box-system-requirements-rest.md)。
