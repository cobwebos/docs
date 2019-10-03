---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67172939"
---
**高级非托管虚拟机磁盘：每个帐户的限制**

| Resource | 默认限制 |
| --- | --- |
| 每个帐户的总磁盘容量 |35 TB |
| 每个帐户的总快照容量 |10 TB |
| 每个帐户的最大带宽（传入 + 传出）<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*入口*是指发送到存储帐户的请求中的所有数据。 *传出*是指从存储帐户接收的响应中的所有数据。

**高级非托管虚拟机磁盘：每磁盘限制**

| 高级存储磁盘类型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁盘大小 |128 GiB |512 GiB |1024 GiB （1 TB） |2048 GiB （2 TB）|4095 GiB （4 TB）|
| 每个磁盘的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每个磁盘的最大吞吐量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每个存储帐户的最大磁盘数 |280 |70 |35 | 17 | 8 |

**高级非托管虚拟机磁盘：每个 VM 的限制**

| Resource | 默认限制 |
| --- | --- |
| 每个 VM 的最大 IOPS |GS5 VM 为 80,000 IOPS |
| 每个 VM 的最大吞吐量 |2000 MB/秒，含 GS5 VM |

