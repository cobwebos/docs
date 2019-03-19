---
title: Azure Data Box 磁盘限制 | Microsoft Docs
description: 介绍了 Microsoft Azure Data Box 磁盘的系统限制与建议的大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/19/2019
ms.author: alkohli
ms.openlocfilehash: 9cad48eeadc06c84e326cbc5f19f1c97e151a795
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880443"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box 磁盘限制


在部署和操作 Microsoft Azure Data Box 磁盘解决方案时请考虑这些限制。

## <a name="data-box-service-limits"></a>Data Box 服务限制

 - Data Box 服务目前在 Azure 区域中列出[区域可用性](data-box-disk-overview.md#region-availability)。
 - Data Box 磁盘支持单个存储帐户。

## <a name="data-box-disk-performance"></a>Data Box 磁盘性能

对通过 USB 3.0 连接的磁盘进行测试时，磁盘性能最高为 430 MB/秒。 实际数字根据所用的文件大小而异。 传输较小的文件时，性能可能会下降。

## <a name="azure-storage-limits"></a>Azure 存储限制

本部分介绍了应用于 Data Box 服务的 Azure 存储服务限制，以及 Azure 文件、Azure 块 blob 和 Azure 页 blob 的必需命名约定。 请仔细查看存储限制并遵循所有建议。

有关 Azure 存储服务限制的最新信息以及对共享、容器和文件进行命名的最佳做法，请转到：

- [命名和引用容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和引用共享](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何文件或目录超出了 Azure 存储服务限制，或者不符合 Azure 文件/Blob 命名约定，则这些文件或目录不会通过 Data Box 服务引入到 Azure 存储中。

## <a name="data-upload-caveats"></a>数据上传注意事项

- 不要直接将数据复制到磁盘中。 将数据复制到预先创建*BlockBlob*，*PageBlob*，并*AzureFile*文件夹。
- *BlockBlob* 和 *PageBlob* 下的文件夹是容器。 例如，容器创建为 *BlockBlob/container* 和 *PageBlob/container*。
- 如果要复制的对象与同名云中有现有 Azure 对象 （如 blob)，Data Box 磁盘将文件重命名为 file(1) 在云中。
- 写入到 *BlockBlob* 和 *PageBlob* 共享中的每个文件将分别上传为块 blob 和页 blob。
- 在 *BlockBlob* 和 *PageBlob* 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传。
- 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 当上传完成时，可以在门户中找到此错误日志的路径，你可以查看此日志来采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。
- 如果按顺序指定托管的磁盘，请查看以下其他注意事项：

    - 跨所有预创建的文件夹和所有 Data Box 磁盘，仅可以在资源组中有一个具有给定名称的托管的磁盘。 这意味着到预创建的文件夹上传的 Vhd 应具有唯一的名称。 请确保给定的名称与资源组中的现有托管的磁盘不匹配。 如果 Vhd 具有相同的名称，然后只有一个 VHD 将转换为具有该名称的托管磁盘。 其他 Vhd 作为页 blob 上传到临时存储帐户。
    - 始终将 Vhd 复制到一个预先创建的文件夹。 如果复制这些文件夹之外或在你创建的文件夹中的 Vhd 的 Vhd 作为页 blob 上载到 Azure 存储帐户和非托管磁盘。
    - 可以上传固定的 Vhd 创建托管的磁盘。 动态 Vhd、 差异 Vhd 或 VHDX 不支持文件。

## <a name="azure-storage-account-size-limits"></a>Azure 存储帐户大小限制

下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请转到 [Azure blob 存储规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 文件规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 每个存储帐户 500 TB。 <br> 这包括来自包括 Data Box 磁盘的所有源的数据。|


## <a name="azure-object-size-limits"></a>Azure 对象大小限制

下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 4.75 TiB                                                 |
| 页 blob         | 8 TiB <br> （以页 Blob 格式上传每个文件必须是 512 字节对齐，否则上传将失败。 <br> VHD 和 VHDX 是对齐的 512 字节。） |
|Azure 文件        | 1 TiB <br> 最大 共享的大小是 5 TiB     |
| 托管磁盘     |4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[托管磁盘的可伸缩性目标](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 块 blob、页 blob 和文件命名约定

| 实体                                       | 约定                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 块 Blob 和页 Blob 的容器名称 <br> Azure 文件的文件共享名称 | 必须是有效的 DNS 名称，长度为 3 到 63 个字符。 <br>  必须以字母或数字开头。 <br> 只能包含小写字母、数字和连字符 (-)。 <br> 每个连字符 (-) 字符的前后必须紧接字母或数字。 <br> 名称中不允许连续的连字符。 |
| Azure 文件的目录和文件名     |<li> 保留大小写，不区分大小写，并且长度不能超过 255 个字符。 </li><li> 不能以正斜杠 (/) 结尾。 </li><li>如有，则将自动删除。 </li><li> 不允许使用以下字符：<code>" \\ / : \| < > * ?</code></li><li> 必须正确地对保留的 URL 字符进行转义。 </li><li> 不允许使用非法的 URL 路径字符。 码位等\\uE000 不是有效的 Unicode 字符。 某些 ASCII 或 Unicode 字符，如控制字符 (0x00 到 0x1F \\u0081，等等)，也不允许。 有关管理 HTTP/1.1 中 Unicode 字符串的规则，请参阅第 2.2 节 RFC 2616：基本规则和 RFC 3987。 </li><li> 不允许使用以下文件名：LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、圆点字符 (.) 和两个圆点字符 (..)。</li>|
| 块 Blob 和页 Blob 的 Blob 名称      | Blob 名称区分大小写，只能包含字符的任意组合。 <br> Blob 名称的长度必须为 1 到 1,024 个字符。 <br> 必须正确地对保留的 URL 字符进行转义。 <br>构成 Blob 名称的路径段数目不能超过 254 个。 路径段是指对应于虚拟目录名称的相邻分隔符（例如，正斜杠“/”）之间的字符串。 |

## <a name="managed-disk-naming-conventions"></a>托管磁盘命名约定

| 实体 | 约定                                             |
|-------------------|-----------------------------------------------------------|
| 托管磁盘名称       | <li> 名称必须是 1 到 80 个字符。 </li><li> 名称必须以字母或数字开头、 以字母、 数字或下划线结尾。 </li><li> 名称可能包含仅字母、 数字、 下划线、 句点或连字符。 </li><li>   名称不应包含空格或`/`。                                              |

## <a name="next-steps"></a>后续步骤

- 请查看 [Data Box 系统要求](data-box-system-requirements.md)
