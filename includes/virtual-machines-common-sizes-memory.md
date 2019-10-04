---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 4d0e7bc7f491e328f5e9fce291d415452c49c745
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830039"
---
内存优化 VM 大小提供适用于关系数据库服务器、中到大型规模的缓存和内存中分析的高内存 CPU 比率。 本文介绍了此分组中各个大小的 vCPU 数、数据磁盘数、NIC 数、存储吞吐量及网络带宽的相关信息。

* Ev3 系列在超线程配置中采用 E5-2673 v4 2.3 GHz (Broadwell) 处理器，针对最常规用途的工作负荷提供了更好的价值主张，因此 Ev3 适用于大多数其他云的常规用途 VM。  在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存也得到了扩展（从 7 GiB/vCPU 到 8 GiB/vCPU）。  Ev3 是 D/Dv2 系列的高内存 VM 大小产品的后继产品。

* Eav3 系列和 Easv3 系列的预览尺寸利用多线程配置中 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器，最多可包含 256mb L3 缓存，同时增加了运行大多数内存优化工作负荷的选项。  Eav3 系列和 Easv3 具有与 Ev3 & Esv3 系列相同的内存和磁盘配置。

* Mv2 系列提供云中任意 VM 的最高 vCPU 计数（最高为208个 vcpu）和最大内存（最大为 5.7 TiB）。 它非常适用于极大型数据库或受益于高 vCPU 计数和大量内存的其他应用程序。

* M 系列提供高 vCPU 计数（最多 128 个vCPU）和大量内存（最高 3.8 TiB）。 它也适用于极大型数据库或其他应用程序，这些应用程序可以受益于高 vCPU 计数和大量内存。

* Dv2 系列、G 系列和 DSv2/GS 适用于需要更快个 vcpu、更好的临时存储性能或具有更高内存需求的应用程序。 它们为许多企业级应用程序提供强大的组合。

* Dv2 系列是原 D 系列的后续系列，其特点是 CPU 功能更强大。 Dv2 系列 CPU 比 D 系列 CPU 快大约 35%。 它基于最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 2.4 GHz (Haswell) 或 E5-2673 v4 2.3 GHz (Broadwell) 处理器，通过英特尔睿频加速技术 2.0 可以达到 3.1 GHz。 Dv2 系列的内存和磁盘配置与 D 系列相同。

* Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。  这些虚拟机大小非常适合于与其他客户的工作负载（涉及符合性和法规要求等元素）高度隔离的工作负载。  客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。  请参阅下面的虚拟机系列表，了解独立 VM 选项。

## <a name="esv3-series"></a>Esv3 系列

ACU：160-190 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

ESv3 系列实例基于 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell) 处理器，可通过 Intel Turbo Boost Technology 2.0 达到 3.5 GHz，并使用高级存储。 Ev3 系列实例适用于内存密集型企业应用程序。


| Size             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800 / 1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> Esv3 系列 VM 的 Intel® 超线程技术功能。

<sup>2</sup> 受约束的可用核心大小。

<sup>3</sup> 实例与专用于单个客户的硬件隔离。

## <a name="easv3-series-preview"></a>Easv3 系列（预览）

高级存储：支持

高级存储缓存：支持

Easv3 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升 Fmax 并使用高级存储。 Easv3 系列大小适用于内存密集型企业应用程序。

[单击此处注册预览](http://aka.ms/azureamdpreview)。

| Size | vCPU | 内存：GiB | 临时存储 (SSD)：GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Ev3 系列 

ACU：160 - 190 <sup>1</sup>

高级存储：不受支持

高级存储缓存：不受支持

Ev3 系列实例基于 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell) 处理器，可通过 Intel Turbo Boost Technology 2.0 达到 3.5 GHz。 Ev3 系列实例适用于内存密集型企业应用程序。

数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 ESv3 大小。 ESv3 系列大小的定价和计费标准与 Ev3 系列相同。 


| Size            | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大临时存储吞吐量：IOPS/读取 Mbps/写入 Mbps | 最大网卡数/网络带宽等级 |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2、&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Ev3 系列 VM 的 Intel® 超线程技术功能。

<sup>2</sup> 受约束的可用核心大小。

<sup>3</sup> 实例与专用于单个客户的硬件隔离。

## <a name="eav3-series-preview"></a>Eav3 系列（预览）

高级存储：不受支持

高级存储缓存：不受支持

Eav3 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升 Fmax 并使用高级存储。 Eav3 系列大小适用于内存密集型企业应用程序。 数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Easv3 系列大小。 Easv3 大小的定价和计费标准与 Eav3 系列相同。

[单击此处注册预览](http://aka.ms/azureamdpreview)。

| Size             | vCPU | 内存：GiB | 临时存储 (SSD)：GiB |
|------------------|------|-------------|-------------------------|
| Standard_E2a_v3  | 2    | 16          | 50                      |
| Standard_E4a_v3  | 4    | 32          | 100                     |
| Standard_E8a_v3  | 8    | 64          | 200                     |
| Standard_E16a_v3 | 16   | 128         | 400                     |
| Standard_E32a_v3 | 32   | 256         | 800                     |
| Standard_E48a_v3 | 48   | 384         | 1200                    |
| Standard_E64a_v3 | 64   | 432         | 1600                    |

## <a name="mv2-series"></a>Mv2 系列

高级存储：支持

高级存储缓存：支持

写入加速器：[支持](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2 系列提供高吞吐量、低延迟、直接映射的本地 NVMe 存储，该存储在超线程 Intel®强®白金 GHz （Skylake）处理器上运行，所有核心基础频率为 2.5 GHz，最大 turbo 频率为 3.8 GHz。 所有 Mv2 系列虚拟机大小均可使用标准和高级永久性磁盘。 Mv2 系列实例是内存优化的 VM 大小，提供无与伦比的计算性能，可支持大型内存中数据库和工作负荷，并且非常适合用于关系数据库服务器、大型缓存和内存中的高内存学. 

|Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1、2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 （7040） | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1、2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 （7040） | 40000 / 1000 | 8 / 16000 |

Mv2 系列 VM 的 Intel®超线程技术功能  

<sup>1</sup>这些大型 vm 需要以下受支持的来宾操作系统之一：Windows Server 2016、Windows Server 2019、SLES 12 SP4、SLES 15。

<sup>2</sup> Mv2 系列 vm 仅为第2代。 如果使用的是 Linux，请参阅以下部分，了解如何查找和选择 SUSE Linux 映像。

#### <a name="find-a-suse-image"></a>查找 SUSE 映像

若要在 Azure 门户中选择合适的 SUSE Linux 映像： 

1. 在 Azure 门户中，选择 "**创建资源**" 
1. 搜索 "SUSE SAP" 
1. SLES for SAP 第2代映像可用作即用即付，或自带订阅（BYOS）。 在搜索结果中，展开所需的图像类别：

    * 适用于 SAP 的 SUSE Linux Enterprise Server （SLES）
    * 适用于 SAP 的 SUSE Linux Enterprise Server （SLES）（BYOS）
    
1. 与 Mv2 系列兼容的 SUSE 图像以名称`GEN2:`作为前缀。 以下 SUSE 映像适用于 Mv2 系列 Vm：

    * GEN2SUSE Linux Enterprise Server （SLES） 12 SP4 for SAP 应用程序
    * GEN2SUSE Linux Enterprise Server （SLES） 15 for SAP 应用程序
    * GEN2SUSE Linux Enterprise Server （SLES） 12 SP4 for SAP 应用程序（BYOS）
    * GEN2SUSE Linux Enterprise Server （SLES） 15 for SAP 应用程序（BYOS）

#### <a name="select-a-suse-image-via-azure-cli"></a>通过 Azure CLI 选择 SUSE 映像

若要查看 Mv2 系列 vm 当前可用的 SLES for SAP 映像列表，请使用以下[`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list)命令：

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

此命令输出适用于 Mv2 系列 Vm 的 SUSE 中当前可用的第2代 Vm。 

示例输出：

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M 系列 

ACU：160-180 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

M 系列大小基于 Intel （R） E7-8890 v3 @ 2.50 GHz   

写入加速器：[支持](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> M 系列 VM 的 Intel® 超线程技术功能

<sup>2</sup> 超过 64 vCPU 的 VM 需要以下受支持的来宾 OS 之一：Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或带 LIS 4.2.1 的 Oracle Linux 7.3。

<sup>3</sup> 受约束的可用核心大小。

<sup>4</sup> 实例与专用于单个客户的硬件隔离。
<br>


## <a name="dsv2-series-11-15"></a>DSv2 系列 11-15

ACU：210 - 250 <sup>1</sup>

高级存储：支持

高级存储缓存：支持

DSv2 系列大小基于 Intel （R）强™ CPU E5-2673 v3 @ 2.40 GHz

| Size | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/Mbps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/Mbps | 最大 NIC 数/预期网络带宽 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> DSv2 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[为实现高性能而设计](../articles/virtual-machines/windows/premium-storage-performance.md)。  
<sup>2</sup> 实例对于专用于单个客户的硬件独立。  
<sup>3</sup> 受约束的可用核心大小。  
<sup>4</sup> 25000 Mbps，具有加速网络。 

<br>

## <a name="dv2-series-11-15"></a>Dv2 系列 11-15

ACU：210 - 250

高级存储：不受支持

高级存储缓存：不受支持

DSv2 系列大小基于 Intel （R）强™ CPU E5-2673 v3 @ 2.40 GHz

| Size              | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大临时存储吞吐量：IOPS/读取 Mbps/写入 Mbps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络带宽 (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000/937/468                                        | 64/64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> 实例对于专用于单个客户的硬件独立。  
<sup>2</sup> 25000 Mbps，具有加速网络。 
