---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334971"
---
**高级非托管虚拟机磁盘：每个帐户的限制**

| 资源 | 限制 |
| --- | --- |
| 每个帐户的总磁盘容量 |35 TB |
| 每个帐户的总快照容量 |10 TB |
| 每个帐户的最大带宽（传入 + 传出）<sup>1</sup> |<=50 Gbps |

<sup>1</sup>个*入口*是指发送到存储帐户的请求中的所有数据。 *传出*是指从存储帐户接收的响应中的所有数据。

**高级非托管虚拟机磁盘：每个磁盘的限制**

| 高级存储磁盘类型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁盘大小 |128 GiB |512 GiB |1024 GiB （1 TB） |2048 GiB （2 TB）|4095 GiB （4 TB）|
| 每个磁盘的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每个磁盘的最大吞吐量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每个存储帐户的最大磁盘数 |280 |70 |35 | 17 | 8 |

**高级非托管虚拟机磁盘：每个 VM 的限制**

| 资源 | 限制 |
| --- | --- |
| 每个 VM 的最大 IOPS |GS5 VM 为 80,000 IOPS |
| 每个 VM 的最大吞吐量 |2000 MB/秒，含 GS5 VM |

