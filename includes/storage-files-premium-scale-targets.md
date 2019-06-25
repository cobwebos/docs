---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c9561f868c70920a0ffb80777f1fc57abafb0df9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073760"
---
#### <a name="additional-premium-file-share-limits"></a>更多高级文件共享限制

|区域  |确定目标  |
|---------|---------|
|最小大小增大/减少    |1 GiB      |
|基线 IOPS    |每 GiB 多达 100,000 个 1 IOPS|
|爆发的 IOPS    |3 个 x IOPS 每 GiB 多达 100,000 个|
|出口率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |
|最大快照数        |200       |

#### <a name="premium-file-limits"></a>高级文件限制

|区域  |确定目标  |
|---------|---------|
|大小                  |1 TiB         |
|每个文件的最大 IOPS     |5,000         |
|并发句柄    |2,000         |