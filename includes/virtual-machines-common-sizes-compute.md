---
title: 포함 파일
description: 포함 파일
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

시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

## <a name="fsv2-series-sup1sup"></a>Fsv2 시리즈 <sup>1</sup>

ACU: 195~210

Premium Storage: 지원됨

高级存储缓存：支持

| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시된 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31(32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63(64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127(128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255(256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512(512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 （768）        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024(1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2，&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152(1520)     | 80000/1100             | 8 / 30000              |

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

<sup>3</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.
