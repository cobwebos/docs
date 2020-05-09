---
title: 排查 Azure Data Box 上的问题，Azure Data Box Heavy
description: 介绍如何排查在将数据复制到 Azure Data Box 和 Azure Data Box Heavy 时这些设备出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/30/2020
ms.author: alkohli
ms.openlocfilehash: 12ca4df6e5dd4ba86ece8469255195e2fa1c155e
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628889"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>排查 Azure Data Box 和 Azure Data Box Heavy 的相关问题

本文详细说明如何排查使用 Azure Data Box 或 Azure Data Box Heavy 时可能出现的问题。 本文包含在将数据复制到 Data Box 时或从 Data Box 上传数据时可能出现的错误列表。

## <a name="error-classes"></a>错误类

Data Box 和 Data Box Heavy 中的错误概括如下：

| 错误类别*        | 说明        | 建议的操作    |
|----------------------------------------------|---------|--------------------------------------|
| 容器或共享名称 | 容器或共享名称未遵循 Azure 命名规则。  |下载错误列表。 <br> 重命名容器或共享。 [了解详细信息](#container-or-share-name-errors)。  |
| 容器或共享大小限制 | 容器或共享中的总数据量超过了 Azure 限制。   |下载错误列表。 <br> 减小容器或共享中的总数据量。 [了解详细信息](#container-or-share-size-limit-errors)。|
| 对象或文件大小限制 | 容器或共享中的对象或文件大小超过了 Azure 限制。|下载错误列表。 <br> 减小容器或共享中的文件大小。 [了解详细信息](#object-or-file-size-limit-errors)。 |    
| 数据或文件类型 | 数据格式或文件类型不受支持。 |下载错误列表。 <br> 对于页 Blob 或托管磁盘，请确保数据已经过 512 字节对齐，并已复制到预先创建的文件夹。 [了解详细信息](#data-or-file-type-errors)。 |
| 非关键 Blob 或文件错误  | Blob 或文件名称未遵循 Azure 命名规则，或文件类型不受支持。 | 无法复制这些 Blob 或文件，或者名称可能已更改。 [了解如何修复这些错误](#non-critical-blob-or-file-errors)。 |

\* 前四个错误类别属于关键错误，必须先予以修复，才能继续准备交付。


## <a name="container-or-share-name-errors"></a>容器或共享名称错误

这些错误与容器和共享名称相关。

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

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


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

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

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

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
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**错误说明**：目录或容器名称包含非法字符。

**建议的解决方法**：已复制的目录或容器名称包含不支持的字符。

- 在本地 web UI 的 "连接和复制" 页上，下载并查看错误文件以标识包含问题的文件夹名称。 
- 重命名目录或容器，以确保它们符合 Azure 命名约定。

有关详细信息，请参阅 [目录](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) 和[容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)的 Azure 命名约定。

## <a name="container-or-share-size-limit-errors"></a>容器或共享大小限制错误

这些错误与超过了容器或共享中允许的数据大小的数据相关。

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**错误说明：** Azure 文件共享将共享限制为 5 TB 的数据。 某些共享已超过此限制。

**建议的解决方法：** 在本地 web UI 的 "**连接和复制**" 页上，下载并查看错误文件。

在错误日志中找到存在此问题的文件夹，并确保该文件夹中的文件小于 5 TB。


## <a name="object-or-file-size-limit-errors"></a>对象或文件大小限制错误

这些错误与超过了 Azure 中允许的最大对象或文件大小的数据相关。 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**错误说明：** 文件大小超过上传的最大文件大小。

**建议的解决方法：** Blob 或文件大小超出了上载所允许的最大限制。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。****
- 确保 Blob 和文件大小不超过 Azure 对象大小限制。

## <a name="data-or-file-type-errors"></a>数据或文件类型错误

这些错误与在容器或共享中找到不受支持的文件类型或数据类型相关。 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**错误说明：** Blob 或文件的对齐不正确。

**建议的解决方法：** Data Box 或 Data Box Heavy 上的 blob 共享仅支持512字节（如 VHD/VHDX）的文件。 复制到页 Blob 共享的任何数据将作为页 Blob 上传到 Azure。

从页 Blob 共享中删除任何非 VHD/VHDX 数据。 可以使用块 Blob 或 Azure 文件的共享还存储一般数据。

有关详细信息，请参阅[页 Blob 概述](../storage/blobs/storage-blob-pageblob-overview.md)。

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**错误说明：** 托管磁盘共享中存在不受支持的文件类型。 只允许固定的 VHD。

**建议的解决方法：**

- 确保仅上传固定的 VHD 来创建托管磁盘。
- 不支持 VHDX 文件或**动态**vhd 和**差异**vhd。

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**错误说明：** 托管磁盘的任何预先存在的文件夹中都不允许使用目录。 这些文件夹中只允许固定的 VHD。

**建议的解决方法：** 对于托管磁盘，在每个共享中，会创建以下三个文件夹，它们对应于存储帐户中的容器：高级 SSD、标准 HDD 和标准 SSD。 这些文件夹对应于托管磁盘的性能层。

- 确保将页 Blob 数据 (VHD) 复制到其中一个现有文件夹。
- 这些现有的文件夹中不允许某个文件夹或目录。 请删除在这些现有文件夹中创建的所有文件夹。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**错误说明：** 不允许在 Linux 中使用符号链接。 

**建议的解决方法：** 符号链接通常是链接、管道和其他此类文件。 请删除链接，或解决链接错误并复制数据。


## <a name="non-critical-blob-or-file-errors"></a>非关键 Blob 或文件错误

以下部分总结了与在数据复制期间看到的 blob、文件或容器的名称有关的所有非关键错误。 如果出现这些错误，则会修改名称，使其符合 Azure 命名约定。 数据上传的相应订单状态将为“已完成，但出现警告”****。  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**错误说明：** Blob 或文件名包含不受支持的控制字符。

**建议的解决方法：** 复制的 Blob 或文件的名称包含不受支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**错误说明：** Blob 或文件名称包含非法字符。

**建议的解决方法：** 复制的 Blob 或文件的名称包含不受支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。 
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**错误说明：** Blob 或文件名以错误字符结尾。

**建议的解决方法：** 已复制的 blob 或文件包含的名称包含不支持的字符。

在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。****
删除或重命名这些文件，以删除不受支持的字符。

有关详细信息，请参阅适用于[blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)的 Azure 命名约定。


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**错误说明：** Blob 或文件名包含太多路径段。

**建议的解决方法：** 已复制的 blob 或文件超过了最大路径段数。 路径段是指连续分隔符（例如正斜杠 /）之间的字符串。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。****
- 确保 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**错误说明：** Blob 或文件名太长。

**建议的解决方法：** Blob 或文件名超过了最大长度。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。****
- Blob 名称不得超过 1,024 个字符。
- 删除或者重命名 Blob 或文件，使其名称不超过 1,024 个字符。

有关详细信息，请参阅 Blob 名称和文件名的 Azure 命名约定。

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**错误说明：** Blob 或文件名中的某个段太长。

**建议的解决方法：** Blob 或文件名中的某个路径段超出了最大字符数。 路径段是指连续分隔符（例如正斜杠 /）之间的字符串。

- 在本地 Web UI 的“连接和复制”页上，下载并查看错误文件。****
- 确保 [Blob 名称](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names)和[文件名](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)符合 Azure 命名约定。


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**错误说明：** 为托管磁盘共享指定了不正确的容器名称。

**建议的解决方法：** 对于托管磁盘，在每个共享中，会创建以下文件夹，它们对应于存储帐户中的容器：高级 SSD、标准 HDD 和标准 SSD。 这些文件夹对应于托管磁盘的性能层。

- 确保将页 Blob 数据 (VHD) 复制到其中一个现有文件夹。 只有这些现有容器中的数据才会上传到 Azure。
- 在与高级 SSD、标准 HDD 和标准 SSD 相同的级别创建的任何其他文件夹不对应于有效的性能层，因此不能使用。
- 删除在性能层外部创建的文件或文件夹。

有关详细信息，请参阅[复制到托管磁盘](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)。


## <a name="next-steps"></a>后续步骤

- 了解 [Data Box Blob 存储系统要求](data-box-system-requirements-rest.md)。
