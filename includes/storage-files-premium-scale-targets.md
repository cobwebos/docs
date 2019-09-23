---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180034"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他高级文件共享级别限制

|区域  |目标  |
|---------|---------|
|最小大小增加/减少    |1 GiB      |
|基线 IOPS    |每个 GiB 1 IOPS，最多100000|
|IOPS 突发    |每 GiB 的3倍 IOPS，最多100000|
|出口速率         |60 MiB/s + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/s + 0.04 * 预配 GiB |

#### <a name="file-level-limits"></a>文件级别限制

|区域  |高级文件  |标准文件 |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|每个文件的最大 IOPS     |5,000         |1,000|
|并发句柄    |2,000         |2,000|
|流出量  |300 MiB/秒|      查看标准文件吞吐量值|
|流入量  |200 MiB/秒| 查看标准文件吞吐量值|
|吞吐量| 查看高级文件入口/出口值| 最多 60 MiB/秒|
