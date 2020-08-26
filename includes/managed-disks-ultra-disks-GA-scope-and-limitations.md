---
title: 包含文件
description: 包含文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225184"
---
目前，超磁盘有其他限制，如下所示：

对于超磁盘，目前唯一可用的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 Vm 无法连接到超磁盘。

下表概述了可在中使用的超级磁盘区域，以及相应的可用性选项：

> [!NOTE]
> 如果以下列表中的某个区域没有支持虚拟磁盘的可用性区域，则该区域中的 Vm 必须在没有任何基础结构冗余选项的情况下部署，才能附加超磁盘。

|区域  |支持超磁盘的可用性区域数  |
|---------|---------|
|US Gov 弗吉尼亚州     |无         |
|US Gov 亚利桑那州     |无         |
|美国中南部     |无         |
|美国中部     |三个区域         |
|美国西部     |无         |
|美国西部 2    |三个区域         |
|美国东部     |三个区域         |
|美国东部 2     |三个区域         |
|东南亚     |三个区域         |
|东亚     |无         |
|北欧     |三个区域          |
|西欧     |三个区域          |
|英国南部     |三个区域          |
|Japan East     |三个区域         |
|法国中部    |两个区域        |
|Brazil South    |无        |
|澳大利亚东部    |三个区域        |
|加拿大中部 *    |三个区域        |

\* 联系 Azure 支持部门以获取对此区域可用性区域的访问权限。

- 仅支持以下 VM 系列：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
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
- 仅支持未缓存的读取和非缓存写入
- GA Vm 上的 IOPS 的当前最大限制为80000。

默认情况下，每个订阅每个区域最多支持16个 TiB 若要请求增加容量，请联系 Azure 支持。