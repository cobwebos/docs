---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76749012"
---
<!-- F-series, Fs-series* -->

计算优化 VM 大小的 CPU 与内存的比率较高。 这些大小适用于中等流量的 web 服务器、网络设备、批处理和应用程序服务器。 本文提供有关个 vcpu、数据磁盘和 Nic 的数量的信息。 它还包括有关此分组中每个大小的存储吞吐量和网络带宽的信息。

Fsv2 系列基于 Intel®强®白金8168处理器。 它的核心 Turbo 时钟速度始终为 3.4 GHz，最大单核 turbo 主频为 3.7 GHz。 Intel® AVX-512 说明是 Intel 可伸缩处理器上的新说明。 这些说明为单精度和双精度浮点运算上的矢量处理工作负荷提供了高达2倍的性能提升。 换句话说，对于任何计算工作负荷，它们都非常快。

凭借较低的每小时定价，Fsv2 系列在基于每个 vCPU 的 Azure 计算单位 (ACU) 的 Azure 产品组合中具有最高性价比。

## <a name="fsv2-series-sup1sup"></a>Fsv2 系列 <sup>1</sup>

ACU：195 - 210

高级存储：支持

高级存储缓存：支持

| 大小             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 缓存和临时存储的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数/预期网络带宽 (MBps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 （768）        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2，&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> Fsv2 系列 Vm 功能 Intel®超线程技术。

<sup>2</sup>使用超过64的 vCPU 需要以下受支持的来宾操作系统之一：
- Windows Server 2016 或更高版本
- Ubuntu 16.04 LTS 或更高版本，带 Azure 优化内核（4.15 内核或更高版本）
- SLES 12 SP2 或更高版本
- RHEL 或 CentOS 版本6.7 到6.10，其中安装了 Microsoft 提供的 .LIS 包4.3.1 （或更高版本）
- RHEL 或 CentOS 版本7.3，其中安装了 Microsoft 提供的 .LIS 包4.2.1 （或更高版本）
- RHEL 或 CentOS 版本7.6 或更高版本
- UEK4 或更高版本的 Oracle Linux
- Debian 9 precise-backports 内核，Debian 10 或更高版本
- 使用4.14 内核或更高版本的 CoreOS

<sup>3</sup> 实例与专用于单个客户的硬件隔离。
