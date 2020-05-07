---
title: Azure Data Box Gateway 限制 | Microsoft Docs
description: 介绍了 Microsoft Azure Data Box Gateway 的系统限制与建议的大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d160b0ead1ceb8c41bede20cb389a360d24258f9
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561387"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway 限制

在部署和操作 Microsoft Azure Data Box Gateway 解决方案时请考虑这些限制。

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway 服务限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway 设备限制

下表介绍了 Data Box Gateway 设备的限制。

| 说明 | 值 |
|---|---|
|否。 设备的文件数 |1 亿 <br> 对于要添加的每个25000000文件（最大限制为100000000），应添加 2 TB 的磁盘空间、8 GB RAM 和4核 CPU。 |
|否。 设备的共享数 |24 |
|否。 Azure 存储容器的共享数 |1 |
|写入到共享的最大文件大小|对于 2 TB 的虚拟设备，最大文件大小为 500 GB。 <br> 最大文件大小按上述比例随数据磁盘大小而增大，直至到达最大大小 5 TB。 |

## <a name="azure-storage-limits"></a>Azure 存储限制

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>数据上传注意事项

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure 存储帐户大小和对象大小限制

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 对象大小限制

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
