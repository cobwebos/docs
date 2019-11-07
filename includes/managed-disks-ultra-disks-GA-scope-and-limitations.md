---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600973"
---
目前，超磁盘有其他限制，如下所示：

- 以下区域支持，每个区域有不同数量的可用性区域：
    - 美国东部 2
    - 美国东部
    - 美国西部 2
    - 东南亚
    - 北欧
    - 欧洲西部
    - 英国南部 
- 只能在可用性区域中使用（区域外部的可用性集和单一 VM 部署无法附加超级磁盘）
- 仅支持以下 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非每个 VM 大小都可在包含 ultra 磁盘的每个受支持的区域中使用。
- 只可用作数据磁盘，并且仅支持 4k 物理扇区大小  
- 只能以空磁盘的形式创建  
- 尚不支持磁盘快照、VM 映像、可用性集和 Azure 磁盘加密
- 尚不支持与 Azure 备份或 Azure Site Recovery 的集成
- GA Vm 上的 IOPS 的当前最大限制为80000。