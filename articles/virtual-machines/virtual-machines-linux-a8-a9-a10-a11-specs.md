<!-- not suitable for Mooncake -->

<properties
 pageTitle="关于 A8 - A11 实例和 Linux | Azure"
 description="了解有关使用适用于 Linux VM 的 Azure A8、A9、A10 和 A11 计算密集型实例的背景信息和注意事项。"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
	ms.service="virtual-machines-linux"
	ms.date="01/26/2016"
	wacn.date=""/>

# 关于在 Linux 中使用 A8、A9、A10 和 A11 计算密集型实例

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 网络访问权限

在单个云服务或可用性集中，A8 和 A9 实例可访问 Azure 中的 RDMA 网络，以运行 Linux MPI 应用程序。目前，仅 [Intel MPI Library 5](https://software.intel.com/intel-mpi-library/) 支持 Azure Linux RDMA。

>[AZURE.NOTE] 目前，Azure Linux RDMA 驱动程序不可通过驱动程序扩展安装。仅当使用来自 Azure 库的已启用 RDMA 的 SLES 12 映像时，它们才可用。

下表汇总了 Linux MPI 应用程序要能够访问计算节点 (IaaS) 群集中的 RDMA 网络所需具备的先决条件。有关部署选项和配置步骤，请参阅[设置 Linux RDMA 群集以运行 MPI 应用程序](/documentation/articles/virtual-machines-linux-classic-rdma-cluster)。

先决条件 | 虚拟机 (IaaS)
------------ | -------------
操作系统 | 来自 Azure 库的 SLES 12 HPC 映像
MPI | Intel MPI Library 5

## HPC Pack 和 Linux 的注意事项

[HPC Pack](https://technet.microsoft.com/zh-cn/library/jj899572.aspx) 是用于 Windows 的 Microsoft 免费 HPC 群集和作业管理解决方案。最新版本的 HPC Pack 2012 R2 支持多个 Linux 分发在 Windows Server 头节点管理的 Azure VM 中部署的计算节点上运行。部署在 A8 或 A9 VM 上并运行支持 MPI 实现的 Linux 计算节点可以运行访问 RDMA 网络的 MPI 应用程序。若要开始使用，请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster)。

## 后续步骤

* 有关 A8、A9、A10 和 A11 实例的可用性和定价的详细信息，请参阅 [Virtual Machines pricing（虚拟机定价）](/home/features/virtual-machines/#price)。

* 若要开始在 Linux 上部署和使用包含 RDMA 的 A8 和 A9 实例，请参阅 [Set up a Linux RDMA cluster to run MPI applications（设置 Linux RDMA 群集以运行 MPI 应用程序）](/documentation/articles/virtual-machines-linux-classic-rdma-cluster)。



<!---HONumber=Mooncake_0425_2016-->