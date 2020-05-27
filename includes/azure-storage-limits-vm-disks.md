---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334968"
---
可以将多个数据磁盘附加到 Azure 虚拟机。 根据 VM 数据磁盘的可伸缩性和性能目标，可以确定需要满足性能和容量要求的磁盘数量和类型。

> [!IMPORTANT]
> 为了获得最佳性能，需要限制附加到虚拟机的、重度使用的磁盘数，以避免可能的性能限制。 如果所有附加的磁盘都没有同时使用，则虚拟机可以支持更多的磁盘。

**对于 Azure 托管磁盘：**

下表说明了每个订阅每个区域的资源数的默认值和最大限制。 对于每个资源组，托管磁盘数、快照数和映像数没有限制。  

> | 资源 | 限制 |
> | --- | --- |
> | 标准托管磁盘 | 50,000 |
> | 标准 SSD 托管磁盘 | 50,000 |
> | 高级托管磁盘 | 50,000 |
> | Standard_LRS 快照 | 50,000 |
> | Standard_ZRS 快照 | 50,000 |
> | 托管映像 | 50,000 |

* **对于标准存储帐户：** 标准存储帐户的总请求速率为 20000 IOPS。 在标准存储帐户中，所有虚拟机磁盘的 IOPS 总数不应超过此限制。
  
    根据请求速率限制，你可以大致计算单个标准存储帐户支持的高利用率磁盘数。 例如，对于基本层 VM，利用率最高的磁盘数约为66，每个磁盘的 IOPS 为 20000/300。 标准层 VM 的利用率最高的磁盘数约为40，即每个磁盘 20000/500 IOPS。 

* **对于高级存储帐户：** 高级存储帐户的总吞吐量速率为 50 Gbps。 所有 VM 磁盘的总吞吐量不应超过此限制。

