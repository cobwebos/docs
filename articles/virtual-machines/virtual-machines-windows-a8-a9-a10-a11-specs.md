<!-- not suitable for Mooncake -->

<properties
 pageTitle="关于 Windows 上的 A8 - A11 实例 | Azure"
 description="了解有关针对 Windows VM 和云服务使用 Azure A8、A9、A10 和 A11 计算密集型实例的背景信息和注意事项。"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.date="04/26/2016"
	wacn.date=""/>

# 关于 A8、A9、A10 和 A11 计算密集型实例

本文提供了有关使用 Azure A8、A9、A10 和 A11 实例（也称计算密集型实例）的背景信息和某些注意事项。本文重点介绍如何对 Windows VM 使用这些实例。本文同样适用于 [Linux VM](/documentation/articles/virtual-machines-linux-a8-a9-a10-a11-specs)。

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 网络访问权限

在单个云服务、可用性集或 Azure Batch 池中，A8 和 A9 实例可访问 Azure 中的 RDMA 网络，以运行使用 Microsoft Network Direct 接口在实例间通信的 Windows MPI 应用程序。

请参阅下表，了解 MPI 应用程序要能够访问 A8 或 A9 实例的 Windows 虚拟机、云服务和 Azure Batch 池中的 RDMA 网络所要满足的先决条件。有关典型部署方案，请参阅 [Set up a Windows RDMA cluster with HPC Pack to run MPI applications（使用 HPC Pack 设置一个运行 MPI 应用程序的 Windows RDMA 群集）](/documentation/articles/virtual-machines-windows-classic-hpcpack-rdma-cluster)和 [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch（在 Azure Batch 中使用多实例任务来执行消息传递接口 (MPI) 应用程序）](/documentation/articles/batch-mpi)。


先决条件 | 虚拟机 | 云服务或 Batch 池 
---------- | ------------ | ------------- 
操作系统 | Windows Server 2012 R2 或 Windows Server 2012 | Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 来宾 OS 系列 
MPI | MS-MPI 2012 R2 或更高版本，或 Intel MPI Library 5 | MS-MPI 2012 R2 或更高版本，或 Intel MPI Library 5 


>[AZURE.NOTE]在 A8 和 A9 大小的虚拟机上，HpcVmDrivers 扩展必须添加到 VM 以安装 RDMA 连接所需的 Windows 网络设备驱动程序。根据你的部署方法，HpcVmDrivers 扩展可自动添加到 A8 或 A9 VM，或者需要你自行添加。若要自行添加扩展，请参阅 [Manage VM extensions（管理 VM 扩展）](/documentation/articles/virtual-machines-windows-classic-manage-extensions)。

## HPC Pack 和 Windows 的注意事项

在 Windows Server 上使用 A8、A9、A10 和 A11 实例时，并不需要使用 HPC Pack，但使用该工具可以方便地访问 Azure 中 RDMA 网络的、基于 Windows 的 MPI 应用程序。HPC Pack 2012 R2 和更高版本包含 Windows 消息传递接口 (MS-MPI) 的 Microsoft 实现的运行时环境，部署在 A8 或 A9 实例上时可使用 Azure RDMA 网络。

有关在 Windows Server 上配合使用计算密集型实例和 HPC Pack 的详细信息和清单，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](/documentation/articles/virtual-machines-windows-classic-hpcpack-rdma-cluster)。




## 后续步骤

* 有关 A8、A9、A10 和 A11 实例的可用性和定价的详细信息，请参阅[虚拟机定价](/home/features/virtual-machines/#price)和[云服务定价](/home/features/cloud-services/#price)。

* 有关 VM 存储容量和磁盘详细信息，请参阅 [Sizes for virtual machines（虚拟机大小）](/documentation/articles/virtual-machines-linux-sizes)。

* 若要开始在 Windows 上使用 HPC Pack 部署和使用 A8 和 A9 实例，请参阅[使用 HPC Pack 设置一个用于运行 MPI 应用程序的 Windows RDMA 群集](/documentation/articles/virtual-machines-windows-classic-hpcpack-rdma-cluster)。

* 有关使用 A8 和 A9 实例在 Azure Batch 中运行 MPI 应用程序的信息，请参阅 [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch（在 Azure Batch 中使用多实例任务来执行消息传递接口 (MPI) 应用程序）](/documentation/articles/batch-mpi)。

<!---HONumber=Mooncake_0620_2016-->