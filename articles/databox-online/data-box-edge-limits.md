---
title: Azure Data Box Edge 限制 | Microsoft Docs
description: 描述 Azure Data Box Edge 的系统限制和建议大小。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 9adfc1ef355c31f0bfeeb6c3b194d62426b7b9b3
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244317"
---
# <a name="azure-data-box-edge-limits"></a>Azure Data Box Edge 限制

在部署和操作 Microsoft Azure Data Box Edge 解决方案时请考虑这些限制。

## <a name="data-box-edge-service-limits"></a>Data Box Edge 服务限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge 设备限制

下表介绍了 Data Box Edge 设备的限制。

| 描述 | ReplTest1 |
|---|---|
|否。 设备的文件数 |1 亿 |
|否。 设备的共享数 |24 |
|否。 每个容器的共享数 |第 |
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

- [准备部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
