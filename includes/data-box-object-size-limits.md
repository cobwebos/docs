---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244582"
---
下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 默认限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | ~ 4.75 TiB                                                 |
| 页 blob         | 8 TiB <br> 以页 Blob 格式上传的文件都必须是 512 字节对齐（整数倍），否则上传失败。 <br> VHD 和 VHDX 为 512 字节对齐。 |
| Azure 文件        | 1 TiB                                                      |
| 托管磁盘     | 4 TiB <br> 有关大小和限制的详细信息，请参阅： <li>[标准 SSD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[高级 SSD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[标准 HDD 的可伸缩性目标](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[托管磁盘的定价和计费](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
