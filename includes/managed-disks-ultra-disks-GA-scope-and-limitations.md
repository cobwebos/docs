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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008618"
---
目前，超磁盘有额外的限制，如下所示：

目前唯一可用于超磁盘的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 VM 无法连接超磁盘。

下表概述了提供区域超磁盘的区域及其相应的可用性选项：

> [!NOTE]
> 这些区域中的某些可用性区域不提供超磁盘。

|区域  |无基础架构冗余  |可用性区域  |
|---------|---------|---------|
|美国西部     |是         |否         |
|美国西部 2    |否         |是         |
|美国东部     |否         |是         |
|美国东部 2     |否         |是         |
|东南亚     |否         |是         |
|北欧     |否         |是         |
|西欧     |否         |是         |
|英国南部     |否         |是         |

- 仅在以下 VM 系列中受支持：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非所有 VM 大小都可用于每个支持的区域，具有超磁盘
- 仅作为数据磁盘提供，仅支持 4k 物理扇区大小。 由于超磁盘的 4K 本机扇区大小，有些应用程序与超磁盘不兼容。 例如，Oracle 数据库需要版本 12.2 或更高版本才能支持超磁盘。  
- 只能以空磁盘的形式创建  
- 当前不支持磁盘快照、VM 映像、可用性集、Azure 专用主机或 Azure 磁盘加密
- 当前不支持与 Azure 备份或 Azure 站点恢复集成
- GA VM 上的 IOPS 当前最大限制为 80，000。

默认情况下，Azure 超级磁盘每个订阅最多提供 16 个 TiB，但超磁盘可按请求支持更高的容量。 要请求增加容量，请与 Azure 支持部门联系。