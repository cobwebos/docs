---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5f14a269478541eaa5852697a917afb3d771841a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196945"
---
目前，超磁盘有其他限制，如下所示：

对于超磁盘，目前唯一可用的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 Vm 无法连接到超磁盘。

下表概述了可在中使用的超级磁盘区域，以及相应的可用性选项：

> [!NOTE]
> 如果以下列表中的某个区域没有支持虚拟磁盘的可用性区域，则该区域中的 Vm 必须在没有任何基础结构冗余选项的情况下部署，才能附加超磁盘。

|区域  |支持超磁盘的可用性区域数  |
|---------|---------|
|US Gov 弗吉尼亚州     |无         |
|美国中南部     |无         |
|美国中部     |三个区域         |
|美国西部     |无         |
|美国西部 2    |三个区域         |
|美国东部     |三个区域         |
|美国东部 2     |两个区域         |
|东南亚     |三个区域         |
|北欧     |三个区域          |
|西欧     |三个区域          |
|英国南部     |三个区域          |
|日本东部     |两个区域         |



- 仅支持以下 VM 系列：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非每个 VM 大小都可在包含 ultra 磁盘的每个受支持区域中使用
- 仅可用作数据磁盘，并且仅支持4k 物理扇区大小。 由于 Ultra 磁盘的4K 本机扇区大小，某些应用程序不能与超磁盘兼容。 Oracle Database，其中一个示例需要12.2 版或更高版本才能支持超高磁盘。  
- 只能以空磁盘的形式创建  
- 当前不支持磁盘快照、VM 映像、可用性集、Azure 专用主机或 Azure 磁盘加密
- 当前不支持与 Azure 备份或 Azure Site Recovery 的集成
- GA Vm 上的 IOPS 的当前最大限制为80000。

默认情况下，每个订阅每个区域最多支持16个 TiB 若要请求增加容量，请联系 Azure 支持。