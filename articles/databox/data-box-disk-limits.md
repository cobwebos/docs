---
title: Azure Data Box 磁盘限制 | Microsoft Docs
description: 介绍了 Microsoft Azure Data Box 磁盘的系统限制与建议的大小。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/12/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 4db70fa93914ba0544d9beb8e523241513a2e5ce
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012115"
---
# <a name="azure-data-box-disk-limits-preview"></a>Azure Data Box 磁盘限制（预览版）


在部署和操作 Microsoft Azure Data Box 磁盘解决方案时请考虑这些限制。 

> [!IMPORTANT] 
> Azure Data Box 磁盘以预览版提供。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="data-box-service-limits"></a>Data Box 服务限制

 - Data Box 目前仅在美国和欧洲在 Azure 公有云的所有 Azure 区域中可用。
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

- 不要直接将数据复制到磁盘中。 请将数据复制到预先创建的 *BlockBlob* 和 *PageBlob* 文件夹中。
- *BlockBlob* 和 *PageBlob* 下的文件夹是容器。 例如，容器创建为 *BlockBlob/container* 和 *PageBlob/container*。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob），则 Data Box 磁盘将覆盖云中的文件。
- 写入到 *BlockBlob* 和 *PageBlob* 共享中的每个文件将分别上传为块 blob 和页 blob。
- 在 *BlockBlob* 和 *PageBlob* 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传。
- 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 当上传完成时，可以在门户中找到此错误日志的路径，你可以查看此日志来采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。

## <a name="azure-storage-account-size-limits"></a>Azure 存储帐户大小限制

下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请转到 [Azure blob 存储规模目标](https://docs.microsoft.com/en-us/azure/storage/cstorage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 文件规模目标](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 每个存储帐户 500 TB。 <br> 这包括来自包括 Data Box 磁盘的所有源的数据。|


## <a name="azure-object-size-limits"></a>Azure 对象大小限制

下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 8 TB                                                 |
| 页 blob         | 1 TB <br> （以页 Blob 格式上传的任何文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。） |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Azure 块 blob 和页 blob 命名约定

| 实体                                       | 约定                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 块 Blob 和页 Blob 的容器名称 | 必须是有效的 DNS 名称，长度为 3 到 63 个字符。 <br>  必须以字母或数字开头。 <br> 只能包含小写字母、数字和连字符 (-)。 <br> 每个连字符 (-) 字符的前后必须紧接字母或数字。 <br> 名称中不允许连续的连字符。 |
| 块 Blob 和页 Blob 的 Blob 名称      | Blob 名称区分大小写，只能包含字符的任意组合。 <br> Blob 名称的长度必须为 1 到 1,024 个字符。 <br> 必须正确地对保留的 URL 字符进行转义。 <br>构成 Blob 名称的路径段数目不能超过 254 个。 路径段是指对应于虚拟目录名称的相邻分隔符（例如，正斜杠“/”）之间的字符串。 |
