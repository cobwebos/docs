---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935772"
---
目前，超磁盘有其他限制，如下所示：

- 以下区域支持，每个区域有不同数量的可用性区域：
    - 美国东部 2
    - 美国东部
    - 美国西部 2
    - 东南亚
    - 北欧
    - 西欧
    - 英国南部 
- 只能在可用性区域中使用（区域外部的可用性集和单一 VM 部署无法附加超级磁盘）
- 仅支持以下 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非每个 VM 大小都可在包含 ultra 磁盘的每个受支持区域中使用
- 仅可用作数据磁盘，并且仅支持4k 物理扇区大小。 由于 Ultra 磁盘的4K 本机扇区大小，某些应用程序不能与超磁盘兼容。 Oracle Database，其中一个示例需要12.2 版或更高版本才能支持超高磁盘。  
- 只能以空磁盘的形式创建  
- 尚不支持磁盘快照、VM 映像、可用性集和 Azure 磁盘加密
- 尚不支持与 Azure 备份或 Azure Site Recovery 的集成
- GA Vm 上的 IOPS 的当前最大限制为80000。
- 如果你想要参与有限的虚拟机预览，可以使用超磁盘完成 160000 IOPS，请 UltraDiskFeedback@microsoft email 发送电子邮件