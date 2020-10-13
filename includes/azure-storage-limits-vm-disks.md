---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829259"
---
可以将多个数据磁盘附加到 Azure 虚拟机。 根据 VM 数据磁盘的可伸缩性和性能目标，可以确定满足性能和容量要求所需的磁盘数量和类型。

> [!IMPORTANT]
> 为了获得最佳性能，需要限制附加到虚拟机的、重度使用的磁盘数，以避免可能的性能限制。 如果附加的所有磁盘未在同时重度使用，则虚拟机可以支持更多的磁盘。

**对于 Azure 托管磁盘：**

下表说明了每个订阅的每个区域的资源数的默认限制和最大限制。 对每个资源组的托管磁盘、快照和映像的数量没有限制。  

> | 资源 | 限制 |
> | --- | --- |
> | 标准托管磁盘 | 50,000 |
> | 标准 SSD 托管磁盘 | 50,000 |
> | 高级托管磁盘 | 50,000 |
> | 标准 LRS 快照 | 50,000 |
> | Standard_ZRS snapshots | 50,000 |
> | 托管映像 | 50,000 |

**对于标准存储帐户：** 标准存储帐户的总请求率上限为 20,000 IOPS。 在标准存储帐户中，所有虚拟机磁盘的 IOPS 总数不应超过此限制。
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**对于高级存储帐户：** 高级存储帐户的总吞吐量速率上限为 50 Gbps。 所有 VM 磁盘的总吞吐量不应超过此限制。

