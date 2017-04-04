<!-- A-series - compute-intensive instances, H-series -->

A8-A11 和 H 系列大小也称为“计算密集型实例”。 运行这些大小的硬件专为计算密集型和网络密集型应用程序而设计和优化，包括高性能计算 (HPC) 群集应用程序、建模和模拟。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。 

Azure H 系列虚拟机是下一代高性能计算 VM，其目标是满足高端计算需求，例如分子建模和计算流体动力学。 这些 8 核和 16 核 VM 以 Intel Haswell E5-2667 V3 处理器技术为基础，其特点是采用 DDR4 内存以及基于本地 SSD 的存储。 

除了强大的 CPU 功能，H 系列还提供了适合低延迟 RDMA 网络的各种选项，允许使用 FDR InfiniBand 和多种内存配置来满足内存密集型计算需求。



## <a name="h-series"></a>H 系列

ACU：290-300

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



## <a name="a-series---compute-intensive-instances"></a>A 系列 - 计算密集型实例

ACU：225

| 大小 | CPU 核心数 | 内存：GiB | 本地 HDD：GiB | 最大数据磁盘数 | 数据磁盘最大吞吐量：IOPS | 最大网卡数/网络带宽等级 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2/高 |
| Standard_A9* |16 |112 |382 |16 |16x500 |4/很高 |
| Standard_A10 |8 |56 |382 |16 |16x500 |2/高 |
| Standard_A11 |16 |112 |382 |16 |16x500 |4/很高 |

*支持 RDMA

<br>



