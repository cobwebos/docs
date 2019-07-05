---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542654"
---
#### <a name="additional-premium-file-share-level-limits"></a>更多高级文件共享级别限制

|区域  |确定目标  |
|---------|---------|
|最小大小增大/减少    |1 GiB      |
|基线 IOPS    |每 GiB，多达 100,000 个 1 IOPS|
|爆发的 IOPS    |3 个 x IOPS 每 GiB，多达 100,000 个|
|出口率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |

#### <a name="file-level-limits"></a>文件级别的限制

|区域  |高级文件  |标准文件 |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|每个文件的最大 IOPS     |5,000         |1,000|
|并发句柄    |2,000         |2,000|
|流入量  |300 MiB/sec|      请参阅标准文件吞吐量值|
|流出量   |200 Mib/sec| 请参阅标准文件吞吐量值|
|Throughput| 请参阅高级文件入口/出口值| 最多 60 MiB/秒|