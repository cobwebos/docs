---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386279"
---
**高级非托管的虚拟机磁盘：每个帐户限制**

| 资源 | 默认限制 |
| --- | --- |
| 每个帐户的总磁盘容量 |35 TB |
| 每个帐户的总快照容量 |10 TB |
| 每个帐户 （传入 + 传出） 的最大带宽<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*入口*指发送到存储帐户的请求中的所有数据。 *出口*指从存储帐户接收的响应中的所有数据。

**高级非托管的虚拟机磁盘：每个磁盘的限制**

| 高级存储磁盘类型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁盘大小 |128 GiB |512 GiB |1,024 giB (1 TB) |2,048 giB (2 TB)|4,095 giB (4 TB)|
| 每个磁盘的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每个磁盘的最大吞吐量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每个存储帐户磁盘数上限 |280 |70 |35 | 17 | 8 |

**高级非托管的虚拟机磁盘：每个 VM 的限制**

| 资源 | 默认限制 |
| --- | --- |
| 每个 VM 的最大 IOPS |GS5 VM 为 80,000 IOPS |
| 每个 VM 的最大吞吐量 |GS5 vm 2,000 MB/秒 |

