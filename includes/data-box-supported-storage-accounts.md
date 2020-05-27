---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696437"
---
下面是 Data Box 设备支持的存储帐户和存储类型的列表。 有关所有不同类型的存储帐户及其完整功能的完整列表，请参阅[存储帐户类型](/azure/storage/common/storage-account-overview#types-of-storage-accounts)。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob*** |**Azure 文件存储** |**说明**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y |
| 常规用途 v1 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v2 高级  |  |Y | | |
| Blob 存储标准 |Y | | |支持热和冷。 |

\* *- 上传到页 blob 的数据必须是 512 字节对齐，例如 VHD。*
