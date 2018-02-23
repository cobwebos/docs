
GPU 优化 VM 大小是具有单个或多个 NVIDIA GPU 的专用虚拟机。 这些大小是针对计算密集型、图形密集型和可视化工作负荷设计的。 本文介绍了此分组中各个大小的 GPU、vCPU、数据磁盘和 NIC 的数目及类型以及存储吞吐量及网络带宽的相关信息。 

* **NC、NCv2 和 ND** 大小针对计算密集型和网络密集型应用程序和算法进行了优化，包括基于 CUDA 和 OpenCL 的应用程序和模拟、AI 以及深度学习。 
* **NV** 大小已针对远程可视化效果、流式处理、游戏、编码和 VDI 方案进行了优化和设计，使用 OpenGL 和 DirectX 之类的框架。  


## <a name="nc-instances"></a>NC 实例

NC 实例采用 [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) 卡。 通过将 CUDA 用于能源勘探应用、碰撞模拟、光纤跟踪渲染、深度学习等领域，用户可以更快地分析数据。 NC24r 配置提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。


| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | 最大数据磁盘数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = 半块 K80 卡。

*支持 RDMA

## <a name="ncv2-instances"></a>NCv2 实例

NCv2 实例是 NC 系列机器的下一代产品，采用了 [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU。 这些 GPU 可提供比当前 NC 系列高 2 倍以上的计算性能。 客户可将这些更新的 GPU 用于传统的 HPC 工作负荷，例如油藏模拟、DNA 测序、蛋白质分析、Monte Carlo 模拟和其他工作负荷。 与 NC 系列一样，NCv2 系列提供了针对紧密耦合的并行计算工作负荷优化的低延迟、高吞吐量网络接口。

> [!IMPORTANT]
> 对于此大小系列，订阅中的 vCPU（核心）配额在每个区域中最初都设置为 0。 可以请求在某个[可用区域](https://azure.microsoft.com/regions/services/)中[提高此系列的 vCPU 配额](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | 最大数据磁盘数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 |

1 GPU = 一个 P100 卡。

*支持 RDMA

## <a name="nd-instances"></a>ND 实例

ND 系列虚拟机是针对 AI 和深度学习工作负荷设计的 GPU 家族的新成员。 它们在训练和推理方面性能卓越。 ND 实例采用 [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU。 这些实例可以针对单精度浮点运算和利用 Microsoft 认知工具包、TensorFlow、Caffe 及其他框架的 AI 工作负荷提供卓越的性能。 ND 系列还提供了更大的 GPU 内存大小（24 GB），能够适应更大的神经网络模型。 与 NC 系列一样，ND 系列可通过 RDMA 和 InfiniBand 连接提供含辅助型低延迟、高吞吐量网络的配置，以便可运行跨多个 GPU 的大规模训练作业。

> [!IMPORTANT]
> 对于此大小系列，订阅中每个区域的 vCPU（核心）配额最初都设置为 0。 可以请求在某个[可用区域](https://azure.microsoft.com/regions/services/)中[提高此系列的 vCPU 配额](../articles/azure-supportability/resource-manager-core-quotas-request.md)。
>

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | 最大数据磁盘数 |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 336 | 1 | 12 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 |
| Standard_ND24s |24 |448 | 1344 | 4 | 32 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 |

1 GPU = 一个 P40 卡。

*支持 RDMA

## <a name="nv-instances"></a>NV 实例



NV 实例采用 [NVIDIA Tesla M60 ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 和 NVIDIA GRID 技术，适用于桌面加速型应用程序和虚拟桌面，方便客户可视化其数据或模拟。 用户可以在 NV 实例上直观显示其图形密集型工作流以获取高级图形功能，并可额外运行单精度工作负荷，例如编码和渲染。 

| 大小 | vCPU | 内存：GiB | 临时存储 (SSD) GiB | GPU | 最大数据磁盘数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = 半块 M60 卡。


