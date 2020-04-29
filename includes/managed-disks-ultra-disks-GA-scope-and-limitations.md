---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008618"
---
目前，超磁盘有其他限制，如下所示：

对于超磁盘，目前唯一可用的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 Vm 无法连接到超磁盘。

下表概述了可在中使用的超级磁盘区域，以及相应的可用性选项：

> [!NOTE]
> 这些区域中的某些可用性区域不提供超磁盘。

|区域  |无基础结构冗余  |可用性区域  |
|---------|---------|---------|
|美国西部     |是         |否         |
|美国西部 2    |否         |是         |
|美国东部     |否         |是         |
|美国东部 2     |否         |是         |
|东南亚     |否         |是         |
|北欧     |否         |是         |
|西欧     |否         |是         |
|英国南部     |否         |是         |

- 仅支持以下 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非每个 VM 大小都可在包含 ultra 磁盘的每个受支持区域中使用
- 仅可用作数据磁盘，并且仅支持4k 物理扇区大小。 由于 Ultra 磁盘的4K 本机扇区大小，某些应用程序不能与超磁盘兼容。 Oracle Database，其中一个示例需要12.2 版或更高版本才能支持超高磁盘。  
- 只能以空磁盘的形式创建  
- 当前不支持磁盘快照、VM 映像、可用性集、Azure 专用主机或 Azure 磁盘加密
- 当前不支持与 Azure 备份或 Azure Site Recovery 的集成
- GA Vm 上的 IOPS 的当前最大限制为80000。

默认情况下，每个订阅每个区域最多支持16个 TiB 若要请求增加容量，请联系 Azure 支持。