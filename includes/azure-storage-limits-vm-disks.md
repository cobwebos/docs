---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334968"
---
您可以将许多数据磁盘附加到 Azure 虚拟机。 根据 VM 数据磁盘的可伸缩性和性能目标，您可以确定满足性能和容量要求所需的磁盘数量和类型。

> [!IMPORTANT]
> 为了获得最佳性能，需要限制附加到虚拟机的、重度使用的磁盘数，以避免可能的性能限制。 如果同时未充分利用所有连接的磁盘，虚拟机可以支持更多磁盘。

**对于 Azure 托管磁盘：**

下表说明了每个订阅每个区域的资源数的默认和最大限制。 每个资源组的托管磁盘、快照和映像数没有限制。  

> | 资源 | 限制 |
> | --- | --- |
> | 标准托管磁盘 | 50,000 |
> | 标准 SSD 托管磁盘 | 50,000 |
> | 高级托管磁盘 | 50,000 |
> | Standard_LRS快照 | 50,000 |
> | Standard_ZRS快照 | 50,000 |
> | 托管映像 | 50,000 |

* **对于标准存储帐户：** 标准存储帐户的最大总请求率为 20，000 IOPS。 标准存储帐户中所有虚拟机磁盘的总 IOPS 不应超过此限制。
  
    您可以根据请求速率限制粗略计算单个标准存储帐户支持的高利用率磁盘数。 例如，对于基本层 VM，高利用率磁盘的最大数量约为 66，即每个磁盘 20，000/300 IOPS。 标准层 VM 的最大高度使用磁盘数约为 40，即每个磁盘 20，000/500 IOPS。 

* **对于高级存储帐户：** 高级存储帐户的最大总吞吐率为 50 Gbps。 所有 VM 磁盘的总吞吐量不应超过此限制。

