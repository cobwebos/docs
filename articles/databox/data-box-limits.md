---
title: Azure Data Box 限制 | Microsoft Docs
description: 介绍 Microsoft Azure Data Box 组件和连接的系统限制与建议大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7d699fc47fa0a0cb57d103ff42ff17bdc0f3a82b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202677"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 限制

部署和操作 Microsoft Azure Data Box 时请考虑这些限制。 下表介绍了 Data Box 的这些限制。

## <a name="data-box-service-limits"></a>Data Box 服务限制

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box 限制

- Data Box 最多可存储导入和导出的500000000文件。
- Data Box 在云中最多支持 512 个容器或共享。 用户共享中的顶层目录将成为云中的容器或 Azure 文件共享。 
- 由于 ReFS 元数据空间消耗，Data Box 用量容量可能小于 80 TB。

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项


### <a name="for-import-order"></a>对于导入顺序

导入顺序 Data Box 注意事项包括：

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>对于出口订单

导出顺序 Data Box 注意事项包括：

- Data Box 是一种基于 Windows 的设备，不支持区分大小写的文件名。 例如，Azure 中可能有两个不同的文件，其名称大小写不同。 不要使用 "数据" 框导出此类文件，因为这些文件将在设备上覆盖。
- 如果输入文件中有重复的标记，或引用相同数据的标记，Data Box 导出可能会跳过或覆盖文件。 文件数量和显示在 Azure 门户上的数据大小可能与设备上存在的数据的实际大小不同。 
- Data Box 通过 SMB 将数据导出到基于 Windows 的系统，并受文件和文件夹的 SMB 限制的限制。 不会导出名称不受支持的文件和文件夹。
- 从前缀到容器的映射均为1:1。
- 文件名的最大大小为1024个字符，不会导出超过此长度的文件名。
- *Xml*文件中的重复前缀 (会导出创建订单) 。 不会忽略重复的前缀。
- 页 blob 和容器名称区分大小写，因此，如果大小写不匹配，将找不到 blob 和/或容器。
 

## <a name="azure-storage-account-size-limits"></a>Azure 存储帐户大小限制

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 块 blob、页 blob 和文件命名约定

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
