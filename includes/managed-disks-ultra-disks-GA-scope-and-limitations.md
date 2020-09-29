---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b41105bafedb8eeaffe5f266f5dd824957c57e0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441187"
---
目前，超磁盘有其他限制，如下所示：

对于超磁盘，目前唯一可用的基础结构冗余选项是可用性区域。 使用任何其他冗余选项的 Vm 无法连接到超磁盘。

下表概述了可在中使用的超级磁盘区域，以及相应的可用性选项：

> [!NOTE]
> 如果以下列表中的某个区域没有支持虚拟磁盘的可用性区域，则该区域中的 Vm 必须在没有任何基础结构冗余选项的情况下部署，才能附加超磁盘。

|区域  |冗余选项  |
|---------|---------|
|Brazil South     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|印度中部     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|东亚     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|德国中西部     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|韩国中部     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|美国中南部    |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|US Gov 亚利桑那州     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|US Gov 弗吉尼亚州     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|美国西部     |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持)         |
|澳大利亚中部    |仅 (可用性集和虚拟机规模集的单个 Vm 不受支持) |
|澳大利亚东部     |三个可用性区域         |
|Southeast Asia    |三个可用性区域        |
|加拿大中部 *     |三个可用性区域          |
|美国中部     |三个可用性区域          |
|美国东部     |三个可用性区域          |
|美国东部 2     |三个可用性区域         |
|法国中部    |两个可用性区域        |
|Japan East    |三个可用性区域        |
|北欧    |三个可用性区域        |
|英国南部    |三个可用性区域        |
|西欧    | 三个可用性区域|
|美国西部 2    |三个可用性区域|

\* 联系 Azure 支持部门以获取对此区域可用性区域的访问权限。

- 仅支持以下 VM 系列：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 并非每个 VM 大小都可在包含 ultra 磁盘的每个受支持的区域中使用。
- 仅作为数据磁盘提供。 
- 默认情况下支持4k 物理扇区大小。 512E 扇区大小作为一款公开提供的产品提供，但你必须 [注册它](https://aka.ms/ultradisk512e)。 大多数应用程序都与4k 扇区大小兼容，但某些应用程序需要512字节扇区大小。 Oracle Database，其中一个示例需要12.2 版或更高版本才能支持4k 本地磁盘。 对于较旧版本的 Oracle DB，需要512字节扇区大小。
- 只能创建为空磁盘。
- 当前不支持磁盘快照、VM 映像、可用性集、Azure 专用主机或 Azure 磁盘加密。
- 当前不支持与 Azure 备份或 Azure Site Recovery 集成。
- 仅支持未缓存的读取和未缓存的写入。
- GA Vm 上的 IOPS 的当前最大限制为80000。

默认情况下，每个订阅每个区域最多支持16个 TiB 若要请求增加容量，请联系 Azure 支持。
