---
title: Azure Data Box 限制 | Microsoft Docs
description: 介绍 Microsoft Azure Data Box 组件和连接的系统限制与建议大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 147cf61dcd36edc75a936cf9b467fd89c8d8a965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326592"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 限制

部署和操作 Microsoft Azure Data Box 时请考虑这些限制。 下表介绍了 Data Box 的这些限制。


## <a name="data-box-service-limits"></a>Data Box 服务限制

 - 如果通过 Data Box 服务使用多个存储帐户，则所有存储帐户应属于同一 Azure 区域。
 - 我们建议使用不超过三个存储帐户。 使用更多存储帐户可能影响性能。

## <a name="data-box-limits"></a>Data Box 限制

- Data Box 最多可存储 5 亿个文件。

## <a name="azure-storage-limits"></a>Azure 存储限制

本部分介绍了应用于 Data Box 服务的 Azure 存储服务限制，以及 Azure 文件、Azure 块 blob 和 Azure 页 blob 的必需命名约定。 请仔细查看存储限制并遵循所有建议。

有关 Azure 存储服务限制的最新信息以及对共享、容器和文件进行命名的最佳做法，请转到：

- [命名和引用容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和引用共享](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何文件或目录超出了 Azure 存储服务限制，或者不符合 Azure 文件/Blob 命名约定，则这些文件或目录不会通过 Data Box 服务引入到 Azure 存储中。

## <a name="data-upload-caveats"></a>数据上传注意事项

- 不要直接将文件复制到任何预创建的共享。 需要在共享下创建文件夹，然后将文件复制到该文件夹。
- StorageAccount_BlockBlob 和 StorageAccount_PageBlob 下的文件夹为容器。 例如，容器创建为 StorageAccount_BlockBlob/container 和 StorageAccount_PageBlob/container。
- 直接在 StorageAccount_AzureFiles 下创建的每个文件夹都将转换为 Azure 文件共享。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则 Data Box 将覆盖云中的文件。
- 写入到 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 共享中的每个文件将分别上传为块 blob 和页 blob。
- Azure Blob 存储不支持目录。 如果在 StorageAccount_BlockBlob 文件夹下创建文件夹，将以 blob 的名义创建虚拟文件夹。 对于 Azure 文件，将维护实际的目录结构。
- 在 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传。
- 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 当上传完成时，可以找到此错误日志的路径，并且可以查看此日志来采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。

## <a name="azure-storage-account-size-limits"></a>Azure 存储帐户大小限制

下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请转到 [Azure blob 存储规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 文件规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 每个存储帐户 500 TiB。 <br> 这包括来自所有源（包括 Data Box）的数据。|
| Azure 文件                                                          | 每个共享 5 TiB。<br> StorageAccount_AzureFiles 下的所有文件夹都须遵循此限制。       |

## <a name="azure-object-size-limits"></a>Azure 对象大小限制

下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 4.75 TiB                                                 |
| 页 blob         | 8 TiB <br> 以页 Blob 格式上传的文件都必须是 512 字节对齐（整数倍），否则上传失败。 <br> VHD 和 VHDX 为 512 字节对齐。 |
| Azure 文件        | 1 TiB                                                      |
| 托管磁盘     | 4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[标准 SSD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[高级 SSD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[标准 HDD 的可伸缩性目标](../virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[托管磁盘的定价和计费](../virtual-machines/windows/disks-types.md#billing)</li>                                                     |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 块 blob、页 blob 和文件命名约定

| 实体                                       | 约定                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 块 Blob 和页 Blob 的容器名称 | 必须是有效的 DNS 名称，长度为 3 到 63 个字符。 <br>  必须以字母或数字开头。 <br> 只能包含小写字母、数字和连字符 (-)。 <br> 每个连字符 (-) 字符的前后必须紧接字母或数字。 <br> 名称中不允许连续的连字符。 |
| Azure 文件的共享名                  | 同上                                                                                                                                                                                                                                                                                                             |
| Azure 文件的目录和文件名     |<li> 保留大小写，不区分大小写，并且长度不能超过 255 个字符。 </li><li> 不能以正斜杠 (/) 结尾。 </li><li>如有，则将自动删除。 </li><li> 不允许使用以下字符：<code>" \\ / : \| < > * ?</code></li><li> 必须正确地对保留的 URL 字符进行转义。 </li><li> 不允许使用非法的 URL 路径字符。 码位等\\uE000 不是有效的 Unicode 字符。 某些 ASCII 或 Unicode 字符，如控制字符 (0x00 到 0x1F \\u0081，等等)，也不允许。 有关管理 HTTP/1.1 中 Unicode 字符串的规则，请参阅第 2.2 节 RFC 2616：基本规则和 RFC 3987。 </li><li> 不允许使用以下文件名：LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、圆点字符 (.) 和两个圆点字符 (..)。</li>|
| 块 Blob 和页 Blob 的 Blob 名称      | </li><li>Blob 名称区分大小写，只能包含字符的任意组合。 </li><li>Blob 名称的长度必须为 1 到 1,024 个字符。 </li><li>必须正确地对保留的 URL 字符进行转义。 </li><li>构成 Blob 名称的路径段数目不能超过 254 个。 路径段是指对应于虚拟目录名称的相邻分隔符（例如，正斜杠“/”）之间的字符串。</li> |
