---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: d76338ccd4dcdf7a9a9a96baa1fb47ed5272d5ff
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821952"
---
| 资源 | 标准文件共享 | 高级文件共享 |
|----------|---------------|------------------------------------------|
| 文件共享的最小大小 | 无最小值;即付即用 | 100 GiB;配 |
| 文件共享的最大大小 | 100 TiB *，5 TiB | 100 TiB |
| 文件共享中文件的最大大小 | 1 TiB | 1 TiB |
| 文件共享中文件的最大数目 | 无限制 | 无限制 |
| 每个共享的最大 IOPS | 10000 IOPS *，1000 IOPS | 100,000 IOPS |
| 每个文件共享的最大存储访问策略数 | 5 | 5 |
| 单个文件共享的目标吞吐量 | 最高 300 MiB/秒 *，最高 60 MiB/秒  | 请参阅高级文件共享入口和出口值|
| 单个文件共享的最大出口数 | 请参阅标准文件共享目标吞吐量 | 最多 6204 MiB/秒 |
| 单个文件共享的最大入口 | 请参阅标准文件共享目标吞吐量 | 最多 4136 MiB/秒 |
| 每个文件打开图柄的最大数目 | 2,000 个打开句柄 | 2,000 个打开句柄 |
| 共享快照的最大数目 | 200 个共享快照 | 200 个共享快照 |
| 最大对象（目录和文件）名称长度 | 2048个字符 | 2048个字符 |
| 最大路径名组成部分（在路径 \A\B\C\D 中，每个字母是一个组成部分） | 255 个字符 | 255 个字符 |

\* 在所有区域中均不可用，请参阅[区域可用性](../articles/storage/files/storage-files-planning.md#regional-availability)，了解可用区域列表。
