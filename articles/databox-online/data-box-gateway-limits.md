---
title: Azure Data Box Gateway 限制 | Microsoft Docs
description: 介绍了 Microsoft Azure Data Box Gateway 的系统限制与建议的大小。
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: edb4995b626055be830a7accb74d99f1db3ef8d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962167"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure Data Box Gateway 限制（预览版）


在部署和操作 Microsoft Azure Data Box Gateway 解决方案时请考虑这些限制。 

> [!IMPORTANT] 
> Data Box Gateway 为预览版。 在部署此解决方案之前，请查看[预览版的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway 服务限制

- 在此版本中，该服务仅在美国、欧洲和亚太地区的某些区域中可用。 有关详细信息，请转到[适用区域](#data-box-gateway-overview#region-availability)。 存储帐户的物理位置应当最靠近其中部署了设备的区域（可能不同于服务地区）。
- 不支持将 Data Box Gateway 资源移动到不同的订阅或资源组。 有关更多详细信息，请转到[将资源移动到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway 设备限制

下表介绍了 Data Box Gateway 设备的限制。

| 说明 | 值 |
|---|---|
|每台 设备的文件数 |1 亿 <br> 限制为每 2 TB 磁盘空间大约 2500 万个文件，最大限制为 1 亿 |
|每台 设备的共享数 |24 |
|写入到共享的最大文件大小|5 TB |

## <a name="azure-storage-limits"></a>Azure 存储限制

本部分介绍了应用于 Data Box Gateway/Data Box Edge 服务的 Azure 存储服务限制，以及 Azure 文件、Azure 块 blob 和 Azure 页 blob 的必需命名约定。 请仔细查看存储限制并遵循所有建议。

有关 Azure 存储服务限制的最新信息以及对共享、容器和文件进行命名的最佳做法，请转到：

- [命名和引用容器](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [命名和引用共享](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> 如果有任何文件或目录超出了 Azure 存储服务限制，或者不符合 Azure 文件/Blob 命名约定，则这些文件或目录不会通过 Data Box Gateway/Data Box Edge 服务引入到 Azure 存储中。

## <a name="data-upload-caveats"></a>数据上传注意事项

将数据移动到 Azure 中时，下面的警告适用。

- 建议不要将多台设备写入到同一容器中。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则设备将覆盖云中的文件。 
- 在共享文件夹下创建的任何空目录层次结构（不含任何文件）都不会上传到 blob 容器。


## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 存储帐户大小和对象大小限制

下面是对复制到存储帐户的数据的大小限制。 请确保上传的数据符合这些限制。 有关这些限制的最新信息，请转到 [Azure blob 存储规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets)和 [Azure 文件规模目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)。

| 复制到 Azure 存储帐户的数据的大小                      | 默认限制          |
|---------------------------------------------------------------------|------------------------|
| 块 Blob 和页 blob                                            | 每个存储帐户 500 TB|


## <a name="azure-object-size-limits"></a>Azure 对象大小限制

下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 8 TB                                                 |
| 页 blob         | 1 TB <br> 以页 Blob 格式上传的每个文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。 |


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
