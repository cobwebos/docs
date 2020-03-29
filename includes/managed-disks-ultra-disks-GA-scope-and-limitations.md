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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935772"
---
目前，超磁盘有额外的限制，如下所示：

- 支持以下区域，每个区域的可用性区域数量不同：
    - 美国东部 2
    - 美国东部
    - 美国西部 2
    - 东南亚
    - 北欧
    - 西欧
    - 英国南部 
- 只能在可用性区域中使用（区域外部的可用性集和单一 VM 部署无法附加超级磁盘）
- 仅在以下 VM 系列中受支持：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非所有 VM 大小都可用于每个支持的区域，具有超磁盘
- 仅作为数据磁盘提供，仅支持 4k 物理扇区大小。 由于超磁盘的 4K 本机扇区大小，有些应用程序与超磁盘不兼容。 例如，Oracle 数据库需要版本 12.2 或更高版本才能支持超磁盘。  
- 只能以空磁盘的形式创建  
- 尚不支持磁盘快照、VM 映像、可用性集和 Azure 磁盘加密
- 尚不支持与 Azure 备份或 Azure 站点恢复集成
- GA VM 上的 IOPS 当前最大限制为 80，000。
- 如果您想参与 VM 的有限预览，该预览可以使用超磁盘完成 160，000 个 IOPS，请发送电子邮件UltraDiskFeedback@microsoft.com