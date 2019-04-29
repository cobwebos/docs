---
title: Azure Data Box Edge 限制 | Microsoft Docs
description: 介绍了 Microsoft Azure Data Box Edge 的系统限制与建议的大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a687398032adffe5e787ef1469abe0b2627a38fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758018"
---
# <a name="azure-data-box-edge-limits"></a>Azure 数据框边缘限制

在部署和操作 Microsoft Azure Data Box Edge 解决方案时请考虑这些限制。

## <a name="data-box-edge-service-limits"></a>Data Box Edge 服务限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge 设备限制

下表介绍了 Data Box Edge 设备的限制。

| 描述 | 值 |
|---|---|
|不。 设备的文件数 |1 亿 |
|不。 设备的共享数 |24 |
|不。 每个容器的共享数 |1 |
|写入到共享的最大文件大小| 5 TB |

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
