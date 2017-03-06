


Azure 上有多个标准大小可供选择。 其中某些大小的注意事项包括：

* D 系列的 VM 旨在运行需要更高计算能力和临时磁盘性能的应用程序。 D 系列 VM 为临时磁盘提供更快的处理器、更高的内存内核比和固态驱动器 (SSD)。 有关详细信息，请参阅 Azure 博客[新的 D 系列虚拟机大小](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)上的公告。
* Dv2 系列是原 D 系列的后续系列，其特点是 CPU 功能更强大。 Dv2 系列 CPU 比 D 系列 CPU 快大约 35%。 该系列基于最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，通过 Intel Turbo Boost Technology 2.0 可以达到 3.1 GHz。 Dv2 系列的内存和磁盘配置与 D 系列相同。
* F 系列基于 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 处理器，该处理器使用 Intel Turbo Boost 技术 2.0，可实现高达 3.1 GHz 的时钟速度。 此 CPU 性能与 Dv2 系列的 VM 相同。  根据每个核心的 Azure 计算单位 (ACU)，在较低的小时价列表中，F 系列在 Azure 产品组合中具有最高性价比。 
  
    F 系列还为 Azure 的 VM 大小命名引入了新标准。 对于将来发布的此系列和 VM 大小，系列名称字母后面的数字与 CPU 核心数量一致。 其他功能，如针对高级存储进行了优化，使用 CPU 核心计数后面的字母指定。 此命名格式将用于将来发布的 VM 大小，但是不会追溯更改任何已发布的现有的 VM 大小的名称。
* G 系列的 VM 可提供最大的内存，并在配备 Intel Xeon E5 V3 系列处理器的主机上运行。
* DS 系列、DSv2 系列、Fs 系列和 GS 系列的 VM 可使用高级存储，为 I/O 密集型工作负荷提供高性能、低延迟的存储。 这些 VM 使用固态硬盘 (SSD) 托管虚拟机的磁盘，而且还提供本地 SSD 磁盘高速缓存。 高级存储只在某些区域可用。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。
*   A 系列和 Av2 系列 VM 可以部署在各种不同的硬件类型和处理器上。 根据硬件限制大小，为运行中的实例提供一致的处理器性能，不论硬件部署的位置。 若要判断此大小部署所在的物理硬件，请从虚拟机中查询虚拟硬件。
* A0 大小在物理硬件上过度订阅。 仅针对此特定大小，其他客户部署可能影响正在运行的工作负荷的性能。 以下概述的相对性能为预期的基准，受限于近似变化性的 15%。

虚拟机的大小会影响定价。 此外，大小还会影响虚拟机的处理能力、内存和存储容量。 存储成本根据存储帐户中的已使用页数进行单独计算。 有关详细信息，请参阅 [Virtual Machines Pricing Details](https://azure.microsoft.com/pricing/details/virtual-machines/)（虚拟机定价详细信息）和 [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)（Azure 存储定价）。 

以下注意事项可能会帮助你决定大小：

* A8-A11 和 H 系列大小也称为“计算密集型实例”。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。 有关如何使用这些大小的详细信息和注意事项，请参阅[关于 H 系列 VM 和计算密集型 A 系列 VM](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
* Dv2 系列、D 系列、G 系列以及对应的 DS/GS 系列是请求更快速的 CPU、更好的本地磁盘性能，或有更高内存要求之应用程序的最佳选择。  它们为许多企业级应用程序提供强大的组合。
* 对于需要更快的 CPU，但是每个 CPU 核心不需要太多内存或本地 SSD 的工作负荷来说，F 系列 VM 是一个极佳选择。  诸如分析、游戏服务器、Web 服务器和批处理等工作负荷从 F 系列的优势中获益。
* Azure 数据中心内的一些物理主机可能不支持更大的虚拟机大小，例如 A5 – A11。 因此，在以下情况下，可能会显示错误消息“无法配置虚拟机 <machine name>”或“无法创建虚拟机 <machine name>”：将现有虚拟机的大小调整为新的大小时；在 2013 年 4 月 16 日前创建的虚拟网络中创建新的虚拟机时；或者向现有的云服务中添加新的虚拟机时。 有关每个部署方案的解决方法，请参阅支持论坛上的 [Error: “Failed to configure virtual machine”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows)（错误：“无法配置虚拟机”）。  
* 订阅也可能会限制某些大小系列中可部署的核心数。 若要增加配额，请联系 Azure 支持。

## <a name="performance-considerations"></a>性能注意事项
我们已创建 Azure 计算单元 (ACU)，提供一种比较 Azure SKU 的计算 (CPU) 性能的方法。 这有助于轻松确定最有可能满足性能需求的 SKU。  ACU 目前在小型 (Standard_A1) VM 上标准为 100，而所有其他 SKU 表示 SKU 在运行标准基准测试时大约可以有多快。 

> [!IMPORTANT]
> ACU 只是一种规则。  工作负荷的结果可能会有所不同。 
> 
> 

<br>

| SKU 系列 | ACU/核心 |
| --- | --- |
| [A0](#a-series) |50 |
| [A1-A4](#a-series) |100 |
| [A5-A7](#a-series) |100 |
| [A1_v2-A8_v2](#av2-series) |100 |
| [A2m_v2-A8m_v2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-D14](#d-series) |160 |
| [D1_v2-D15_v2](#dv2-series) |210 - 250* |
| [DS1-DS14](#ds-series) |160 |
| [DS1_v2-DS15_v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-G5](#g-series) |180 - 240* |
| [GS1-GS5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |
| [L4s-L32s](#l-series) |180 - 240* |

ACU 标有 *使用 Intel® Turbo 技术来增加 CPU 频率，并提升性能。  提升量可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

## <a name="size-tables"></a>大小表
下表显示这些虚拟机提供的大小和容量。

* 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘和以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB
* 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
* 数据磁盘可以在缓存或非缓存模式下运行。  对于缓存数据磁盘操作，主机缓存模式设置为 **ReadOnly** 或 **ReadWrite**。  对于非缓存数据磁盘操作，主机缓存模式设置为 **None**。
* 最大网络带宽是每个 VM 类型分配并分配的最大聚合带宽。 最大带宽提供选择正确 VM 类型的指导原则，以确保有适当的网络容量可用。 在低、中、高和极高之间切换时，吞吐量将随之增加。 实际的网络性能将取决于许多因素，包括网络和应用程序负载，以及应用程序的网络设置。

## <a name="a-series"></a>A 系列
| 大小 | CPU 核心数 | 内存：GiB | 本地 HDD：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0.768 |20 |1 |1x500 |1/低 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |1/中 |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |1/中 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2/高 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4/高 |
| Standard_A5 |2 |14 |135 |4 |4X500 |1/中 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2/高 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4/高 |

<br>

## <a name="a-series---compute-intensive-instances"></a>A 系列 - 计算密集型实例
有关如何使用这些大小的信息和注意事项，请参阅[关于 H 系列 VM 和计算密集型 A 系列 VM](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

| 大小 | CPU 核心数 | 内存：GiB | 本地 HDD：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2/高 |
| Standard_A9* |16 |112 |382 |16 |16x500 |4/很高 |
| Standard_A10 |8 |56 |382 |16 |16x500 |2/高 |
| Standard_A11 |16 |112 |382 |16 |16x500 |4/很高 |

*支持 RDMA

<br>

## <a name="av2-series"></a>Av2 系列

| 大小            | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 本地磁盘的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大网卡数/网络带宽等级 |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2x500                         | 1/中                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4x500                         | 2/中                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8x500                         | 4/高                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000/160/80                                          | 16/16x500                       | 8/高                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000/40/20                                           | 4/4x500                         | 2/中                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000/80/40                                           | 8/8x500                         | 4/高                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000/160/80                                          | 16/16x500                       | 8/高                     |

## <a name="d-series"></a>D 系列

| 大小         | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 本地磁盘的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大网卡数/网络带宽等级 |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000/46/23                                           | 2/2x500                         | 1/中                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 4/4x500                         | 2/高                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 8/8x500                         | 4/高                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 16/16x500                       | 8/高                     |
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 4/4x500                         | 2/高                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 8/8x500                         | 4/高                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 16/16x500                       | 8/高                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 32/32x500                       | 8/非常高                |
<br>

## <a name="dv2-series"></a>Dv2 系列


| 大小              | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 本地磁盘的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大网卡数/网络带宽等级 |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3.5         | 50             | 3000/46/23                                           | 2/2x500                         | 1/中                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000/93/46                                           | 4/4x500                         | 2/高                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000/187/93                                         | 8/8x500                         | 4/高                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000/375/187                                        | 16/16x500                       | 8/高                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000/750/375                                        | 32/32x500                       | 8/极高           |
| Standard_D11_v2   | 2         | 14          | 100            | 6000/93/46                                           | 4/4x500                         | 2/高                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 8/8x500                         | 4/高                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 16/16x500                       | 8/高                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 32/32x500                       | 8/极高           |
| Standard_D15_v2** | 20        | 140         | 1,000          | 60000/937/468                                        | 40/40x500                       | 8/极高*          |

*某些区域为 Standard_D15_v2 大小提供加速网络。 有关使用情况和可用性的详细信息，请参阅 [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/)（加速网络已推出预览版）和[虚拟机的加速网络](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md)。

**实例对于专用于单个客户的硬件独立。

<br>

## <a name="ds-series"></a>DS 系列*
| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 缓存和本地磁盘的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 32 |1/中 |
| Standard_DS2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 64 |2/高 |
| Standard_DS3 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 128 |4/高 |
| Standard_DS4 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 256 |8/高 |
| Standard_DS11 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 64 |2/高 |
| Standard_DS12 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 128 |4/高 |
| Standard_DS13 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 256 |8/高 |
| Standard_DS14 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 512 |8/非常高 |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

*DS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。

<br>

## <a name="dsv2-series"></a>DSv2 系列*
| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 缓存磁盘最大吞吐量：IOPS / MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 48 |1/中 |
| Standard_DS2_v2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 96 |2/高 |
| Standard_DS3_v2 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 192 |4/高 |
| Standard_DS4_v2 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 384 |8/高 |
| Standard_DS5_v2 |16 |56 |112 |32 |64,000 / 512 (688) |51,200 / 768 |8/极高 |
| Standard_DS11_v2 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 96 |2/高 |
| Standard_DS12_v2 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 192 |4/高 |
| Standard_DS13_v2 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 384 |8/高 |
| Standard_DS14_v2 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 768 |8/极高 |
| Standard_DS15_v2*** |20 |140 |280 |40 |80,000 / 640 (720) |64,000 / 960 |8/极高** |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

*DSv2 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。

**某些区域为 Standard_DS15_v2 大小提供加速网络。 有关使用情况和可用性的详细信息，请参阅 [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/)（加速网络已推出预览版）和[虚拟机的加速网络](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md)。

***实例对于专用于单个客户的硬件独立。
<br>

## <a name="f-series"></a>F 系列

| 大小         | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 本地磁盘的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大网卡数/网络带宽等级 |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 2/2x500                         | 1/中                 |
| Standard_F2  | 2         | 4           | 32             | 6000/93/46                                           | 4/4x500                         | 2/高                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 8/8x500                         | 4/高                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 16/16x500                       | 8/高                     |
| Standard_F16 | 16        | 32          | 256            | 48000/750/375                                        | 32/32x500                       | 8/极高           |
<br>

## <a name="fs-series"></a>Fs 系列*
| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 缓存和本地磁盘的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |1/中 |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2/高 |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4/高 |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8/高 |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8/极高 |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

*Fs 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。  有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。

<br>

## <a name="g-series"></a>G 系列

| 大小         | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 本地磁盘的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大网卡数/网络带宽等级 |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000/93/46                                           | 4/4 x 500                       | 1/高                     |
| Standard_G2  | 4         | 56          | 768            | 12000/187/93                                         | 8/8 x 500                       | 2/高                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000/375/187                                        | 16/16 x 500                     | 4/很高                |
| Standard_G4  | 16        | 224         | 3,072          | 48000/750/375                                        | 32/32 x 500                     | 8/极高           |
| Standard_G5* | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64/64 x 500                     | 8/极高           |

*实例对于专用于单个客户的硬件独立。
<br>

## <a name="gs-series"></a>GS 系列*
| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 缓存和本地磁盘的最大吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10,000 / 100 (264) |5,000 / 125 |1/高 |
| Standard_GS2 |4 |56 |112 |8 |20,000 / 200 (528) |10,000 / 250 |2/高 |
| Standard_GS3 |8 |112 |224 |16 |40,000 / 400 (1,056) |20,000 / 500 |4/很高 |
| Standard_GS4 |16 |224 |448 |32 |80,000 / 800 (2,112) |40,000 / 1,000 |8/极高 |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8/极高 |

MBps = 每秒 10^6 字节，GiB = 1024^3 字节。

*GS 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。 

**实例对于专用于单个客户的硬件独立。
<br>

## <a name="h-series"></a>H 系列
Azure H 系列虚拟机是下一代高性能计算 VM，其目标是满足高端计算需求，例如分子建模和计算流体动力学。 这些 8 核和 16 核 VM 以 Intel Haswell E5-2667 V3 处理器技术为基础，其特点是采用 DDR4 内存以及基于本地 SSD 的存储。 

除了强大的 CPU 功能，H 系列还提供了适合低延迟 RDMA 网络的各种选项，允许使用 FDR InfiniBand 和多种内存配置来满足内存密集型计算需求。

有关如何使用这些大小的信息和注意事项，请参阅[关于 H 系列 VM 和计算密集型 A 系列 VM](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 磁盘最大吞吐量：IOPS | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2/高 |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4/很高 |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2/高 |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4/很高 |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4/很高 |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4/很高 |

*支持 RDMA

<br>


## <a name="ls-series"></a>Ls-系列* 

Ls-系列针对需要低延迟本地存储的工作负荷，如 NoSQL 数据库（例如 Cassandra、MongoDB、Cloudera 和 Redis）进行了优化。 Ls-系列提供多达 32 个 CPU 核心数，使用的是 [Intel® Xeon® 处理器 E5 v3 系列](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)。 这与 G/GS-系列拥有相同的 CPU 性能，并且每个 CPU 核心附带 8 GiB 内存。  

 
| 大小          | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | 最大数据磁盘数 | 缓存磁盘最大吞吐量：IOPS / MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大网卡数/网络带宽等级 | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA/NA (0)          | 5,000 / 125                               | 2/高       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA/NA (0)          | 10,000 / 250                              | 4/很高  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA/NA (0)          | 20,000 / 500                              | 8/极高 | 
| Standard_L32s** | 32   | 256  | 5,630 | 64             | NA/NA (0)          | 40,000 / 1,000                            | 8/极高 | 
 
MBps = 每秒 10^6 字节，GiB = 1024^3 字节。 

*Ls 系列 VM 可能的最大磁盘吞吐量（IOPS 或 MBps）可能受限于附加磁盘的数量、大小和条带化。 有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../articles/storage/storage-premium-storage.md)。 

**实例对于专用于单个客户的硬件独立。



## <a name="n-series"></a>N 系列
NC 和 NV 大小也称为支持 GPU 的实例。 这些是针对不同方案和用例优化的专用虚拟机，其中包含 NVIDIA 的 GPU 卡。 NV 大小已针对远程可视化效果、流式处理、游戏、编码和 VDI 方案进行了优化和设计，使用 OpenGL 和 DirectX 之类的框架。 NC 大小更适用于计算密集型和网络密集型应用程序和算法，包括基于 CUDA 和 OpenCL 的应用程序和模拟。 


### <a name="nv-instances"></a>NV 实例
NV 实例采用 NVIDIA 的 Tesla M60 GPU 卡和 NVIDIA GRID，适用于桌面加速型应用程序和虚拟桌面，方便客户可视化其数据或模拟。 用户可以在 NV 实例上可视化其图形密集型工作流以获取高级图形功能，并可额外运行单精度工作负荷，例如编码和渲染。 Tesla M60 采用 4096 CUDA 核心，其双 GPU 设计最多可以运行 36 个 1080p H.264 流。 


| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = 半块 M60 卡。

**受支持的操作系统**

* Windows Server 2016、Windows Server 2012 R2 - 请参阅[适用于 Windows 的 N 系列驱动程序安装](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

### <a name="nc-instances"></a>NC 实例
NC 实例采用 NVIDIA 的 Tesla K80 卡。 通过将 CUDA 用于能源勘探应用、碰撞模拟、光纤跟踪渲染、深度学习等领域，用户现在分析数据的速度要快得多。 Tesla K80 采用 4992 CUDA 核心，其双 GPU 设计最多可以执行 2.91 兆次的双精度浮点运算，以及 8.93 兆次的单精度浮点运算。

| 大小 | CPU 核心数 | 内存：GiB | 本地 SSD：GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = 半块 K80 卡。

*支持 RDMA

**受支持的操作系统**

* Windows Server 2016、Windows Server 2012 R2 - 请参阅[适用于 Windows 的 N 系列驱动程序安装](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS - 请参阅[适用于 Linux 的 N 系列驱动程序安装](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>注意：使用 CLI 和 PowerShell 的标准 A0 - A4
在经典部署模型中，CLI 和 PowerShell 中的一些 VM 大小名称略有不同：

* Standard_A0 是特小型 
* Standard_A1 是小型
* Standard_A2 是中型
* Standard_A3 是大型
* Standard_A4 是超大型

## <a name="next-steps"></a>后续步骤
* 了解 [Azure 订阅和服务的限制、配额和约束](../articles/azure-subscription-service-limits.md)。
* 详细了解适用于高性能计算 (HPC) 等工作负荷的 [H 系列和计算密集型 A 系列 VM](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

