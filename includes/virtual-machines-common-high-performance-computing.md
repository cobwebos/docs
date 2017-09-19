组织具有大规模计算需求。 这些大型计算工作负荷包括工程设计和分析、财务风险计算、图像渲染、复杂建模、Monte Carlo 仿真等。 

有了 Azure，可以使用所选 Azure 基础结构服务或托管服务在 Azure 虚拟机的群集上运行高性能计算 (HPC) 和批处理工作负荷。 使用 Azure，可以灵活地将计算资源扩展到数千个 VM 或核心，然后在需要较少的资源时进行缩减。 

Azure HPC 解决方案高效运行计算密集型的 Linux 和 Windows 工作负荷（从并行批处理作业到传统的 HPC 仿真）。 现在已支持行业领先的 HPC 应用程序在 Azure VM 中运行，你可以充分利用为计算密集型作业而设计的专用 VM 大小和映像。 


## <a name="solution-architectures"></a>解决方案体系结构

Azure 中的计算密集型示例解决方案体系结构包括：

* 使用所选的 Linux 或 Windows 群集或网格管理器，在虚拟机中运行 HPC 应用程序或[虚拟机规模集](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) [了解详细信息](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* 创建混合式解决方案，以扩展本地 HPC 群集，将（“突发”）高峰工作负荷卸载到 Azure VM [了解详细信息](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/)
* 使用托管的可缩放 Azure [Batch](https://azure.microsoft.com/services/batch/) 服务运行计算密集型工作负荷，而无需管理基础结构 [了解详细信息](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)

## <a name="hpc-cluster-and-grid-solutions"></a>HPC 群集和网格解决方案
将熟悉的 HPC 工作负荷管理工具部署或扩展到 Azure 虚拟机并运行计算密集型工作负荷。 选项包括开放源代码工具（如 [Slurm 工作负荷管理器](https://slurm.schedmd.com/)）和来自 Microsoft 及其他发布者的群集或网格管理器。

### <a name="linux-and-oss-cluster-solutions"></a>Linux 和 OSS 群集解决方案
使用 Azure 资源管理器模板可在虚拟机或虚拟机规模集中部署 Linux HPC 群集解决方案，包括：

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

另请参阅 [5 单击模板](https://github.com/tanewill/5clickTemplates)集合。

### <a name="grid-managers"></a>网格管理器
具有商业网格管理器的 Microsoft 合作伙伴可使其解决方案在 Azure VM 中可用。 示例包括：

* [IBM Spectrum Symphony 和 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
[HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) 是在 Microsoft Azure 和 Windows Server 技术基础之上构建的 Microsoft 免费 HPC 解决方案。 HPC Pack 提供了几个可在 [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 和 [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM 中运行的选项。 

了解如何：

* [在 Azure 中创建 HPC Pack 2016 群集](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [使用 HPC Pack 扩展到 Azure](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) 是一种平台服务，用于在云中高效运行大规模并行和高性能计算 (HPC) 应用程序。 Azure Batch 可以计划要在托管的虚拟机池上运行的计算密集型工作，并且可以自动缩放计算资源以符合作业的需求。 

使用 Batch SDK 和工具可将 HPC 应用程序或容器工作负荷与 Azure 集成，将数据暂存到 Azure，并生成作业执行管道。 还可以按[降低的价格](https://azure.microsoft.com/pricing/details/batch/)使用多余（低优先级） Azure VM 容量，显著降低在 Batch 中运行某些工作负荷的成本。

了解如何：

* [开始使用 Batch 进行开发](../articles/batch/batch-dotnet-get-started.md)
* [使用 Azure Batch 代码示例](https://github.com/Azure/azure-batch-samples)
* [将低优先级 VM 与 Batch（预览版）配合使用](../articles/batch/batch-low-pri-vms.md)
* [使用 Batch Shipyard 运行容器化的 HPC 工作负荷](https://github.com/Azure/batch-shipyard)
* [将 R 语言与 Batch 配合使用](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC 和 GPU VM 大小
Azure 提供了一系列的 [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 和 [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM 大小，包括为计算密集型工作负荷设计的大小。 例如，H16r 和 H16mr VM 可以连接到高吞吐量后端 RDMA 网络。 此云网络可以提高 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) 或 Intel MPI 下运行的紧密耦合的并行应用程序性能。 N 系列的 VM 具备为计算密集型或图形密集型应用程序（包括人工智能 (AI) 学习和可视化）设计的 NVIDIA GPU。 

了解更多：

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) 和 [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM 的高性能计算大小 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) 和 [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM 的启用了 GPU 的大小 

了解如何：

* [设置 Linux RDMA 群集以运行 MPI 应用程序](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [通过 Microsoft HPC Pack 设置 Windows RDMA 群集以运行 MPI 应用程序](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [使用 Batch 池中的计算密集型 VM](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>HPC 映像

请访问 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 查找为 HPC 设计的 Linux 和 Windows VM 映像。 示例包括：

* [RogueWave 的基于 CentOS 的 HPC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [适用于 Windows 和 Linux 的 Azure 数据科学 VM](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>HPC 应用程序

在 Azure 中运行自定义 HPC 应用程序或商业 HPC 应用程序。 有一些应用程序已成为使用更多 VM 或计算核心高效进行缩放的基准。 下面是一些示例。

> [!IMPORTANT]
> 请向商业应用程序的供应商咨询有关在云中运行的许可或其他限制。 并非所有供应商都提供即付即用许可。 可能需要云中有一个用于自己的解决方案的许可服务器，或连接到本地许可证服务器。

### <a name="engineering-applications"></a>工程应用程序


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB 分布式计算服务器](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM +](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>图形和渲染

* Azure Batch（预览版）上的 [Autodesk Maya、3ds Max 和 Arnold](../articles/batch/batch-rendering-service.md)

### <a name="ai-and-deep-learning"></a>AI 和深度学习

* [Microsoft 认知工具包](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [适用于数据科学 VM 的深度学习工具包](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [用于深入学习的 Batch Shipyard 窍门](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>解决方案合作伙伴

在 Azure 中开发或提供批处理和 HPC 应用程序的合作伙伴包括：

* [Cycle Computing](https://cyclecomputing.com/)（现[已加入 Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)）
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>HPC 存储

大规模的批处理和 HPC 工作负荷具有超过传统云文件系统功能的数据存储和访问需求。 可以在 Azure 中实现并行文件系统解决方案，如 [Lustre](http://lustre.org/) 和 [BeeGFS](http://www.beegfs.com/content/)。

了解更多：

* [Azure 上用于 HPC 存储的并行文件系统](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>相关的 Azure 服务

Azure 虚拟机、虚拟机规模集、Batch 和相关的计算服务是大多数 Azure HPC 解决方案的基础。 但是，你的解决方案可以利用许多相关的 Azure 服务。 下面是部分列表：

### <a name="storage"></a>存储

* [Blob、表和队列存储](../articles/storage/storage-introduction.md)
* [文件存储](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>数据和分析
* 用于 Azure 上的 Hadoop 群集的 [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [机器学习](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [SQL 数据库](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>联网
* [虚拟网络](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>容器
* [容器服务](../articles/container-service/dcos-swarm/container-service-intro.md)
* [容器注册表](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>客户案例

以下是已解决 Azure HPC 解决方案的业务问题的客户示例：

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>后续步骤
* 了解有关[工程模拟](https://simulation.azure.com/)、[渲染](https://simulation.azure.com/)以及[银行和资本市场](https://finance.azure.com/)的大型计算解决方案的详细信息。
* 有关最新通告，请参阅 [Microsoft HPC 和批处理团队博客](http://blogs.technet.com/b/windowshpc/)与 [Azure 博客](https://azure.microsoft.com/blog/tag/hpc/)。


