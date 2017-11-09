<!-- A-series - compute-intensive instances, H-series -->

A8-A11 和 H 系列大小也称为“计算密集型实例”。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。 

Azure H 系列虚拟机是下一代高性能计算 VM，旨在满足高端计算需求，例如分子建模和计算流体动力学。 这些 8 vCPU 和 16 vCPU 的 VM 基于采用 DDR4 内存和基于 SSD 的临时存储的 Intel Haswell E5-2667 V3 处理器技术构建。 

除强大的 CPU 功能外，H 系列还提供支持使用 InfiniBand 的低延迟 RDMA 网络的各种选项，以及支持内存密集型计算要求的多项内存配置。



## <a name="h-series"></a>H 系列

ACU：290-300

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 磁盘最大吞吐量：IOPS | 最大 NIC 数 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |#N/A  |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |#N/A  |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4  |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4  |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 |

*对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。

<br>



## <a name="a-series---compute-intensive-instances"></a>A 系列 - 计算密集型实例

ACU：225

| 大小 | vCPU | 内存：GiB | 临时存储 (HDD)：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大 NIC 数|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |#N/A |
| Standard_A9* |16 |112 |382 |16 |16x500 |4 |
| Standard_A10 |8 |56 |382 |16 |16x500 |#N/A  |
| Standard_A11 |16 |112 |382 |16 |16x500 |4 |

*对于 MPI 应用程序来说，专用 RDMA 后端网络是通过 FDR InfiniBand 网络启用的，后者可以提供相当低的延迟和高带宽。

<br>



