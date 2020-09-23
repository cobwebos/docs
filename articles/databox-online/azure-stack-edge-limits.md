---
title: Azure Stack Edge Pro 限制 |Microsoft Docs
description: 了解部署和操作 Azure Stack Edge Pro 时的限制和建议大小，包括服务限制、设备限制和存储限制。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5f3c39ce7334145d3ffc1d54badb1f7b766da70f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904451"
---
# <a name="azure-stack-edge-pro-limits"></a>Azure Stack Edge Pro 限制

部署和操作 Microsoft Azure Stack Edge Pro 解决方案时，请考虑这些限制。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 服务限制

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack 边缘设备限制

下表描述了 Azure Stack Edge Pro 设备的限制。 

| 说明 | 值 |
|---|---|
|不是。 设备的文件数 |1 亿 |
|不是。 设备的共享数 |24 |
|不是。 每个容器的共享数 |1 |
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

- [准备部署 Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)
