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
	ms.date="05/09/2016"
	wacn.date=""/>

# 关于 A8、A9、A10 和 A11 计算密集型实例 

本文提供了有关使用 Azure A8、A9、A10 和 A11 实例（也称计算密集型实例）的背景信息和某些注意事项。本文重点介绍如何对 Linux VM 使用这些实例。本文同样适用于 [Windows VM](/documentation/articles/virtual-machines-windows-a8-a9-a10-a11-specs)。

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## RDMA 网络访问权限

在单个云服务或可用性集中，运行以下受支持 Linux HPC 分发版之一和受支持 MPI 实现的 A8 和 A9 Linux VM 群集可以访问 Azure 中的 RDMA 网络，以运行 Linux MPI 应用程序。有关部署选项和示例配置步骤，请参阅 [Set up a Linux RDMA cluster to run MPI applications（设置 Linux RDMA 群集以运行 MPI 应用程序）](/documentation/articles/virtual-machines-linux-classic-rdma-cluster)。

* **分发版** - 从 Azure 库映像部署的 SUSE Linux Enterprise Server (SLES) 12 for HPC、SLES 12 for HPC (Premium)、基于 CentOS 的 7.1 HPC 或基于 CentOS 的 6.5 HPC

* **MPI** - Intel MPI Library 5.x

    >[AZURE.NOTE] 应用商店中基于 CentOS 的 HPC 映像上已安装 Intel MPI 5.1.3.181。若要在 SLES 12 HPC VM 上使用 Intel MPI，必须单独安装它。

目前，仅当你从 Azure 库部署启用 RDMA 的 SLES 12 HPC 和 CentOS HPC 映像时，才安装 Azure Linux RDMA 驱动程序。无法在部署的其他 Linux VM 上安装驱动程序。

>[AZURE.NOTE]在来自应用商店的基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能无法工作。


## SLES 12 的 RDMA 驱动程序更新
基于 SLES 12 HPC 映像创建 VM 后，需要更新 VM 上用于 RDMA 网络连接的 RDMA 驱动程序。

>[AZURE.IMPORTANT]**需要**对 Azure 区域中的 SLES 12 HPC VM 部署执行此步骤。如果你已部署基于 CentOS 的 7.1 HPC 或 6.5 HPC VM，则不需要执行此步骤。

在更新驱动程序之前，请停止所有 **zypper** 进程，或者所有锁定 VM 上 SUSE 存储库数据库的进程。否则，可能无法正常更新驱动程序。

若要更新每个 VM 上的 Linux RDMA 驱动程序，请在客户端计算机上运行以下 Azure CLI 命令集之一。

**对于在经典部署模型中预配的 SLES 12 HPC VM**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**对于在 Resource Manager 部署模型中预配的 SLES 12 HPC VM**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]安装驱动程序可能需要花费一段时间，并且命令在返回时不会提供输出。更新后，你的 VM 将重新启动，几分钟后即可供使用。

### 用于驱动程序更新的示例脚本

如果你有 SLES 12 HPC VM 的群集，可以针对群集中的所有节点编写驱动程序更新脚本。例如，以下脚本将更新 8 节点群集内的驱动程序。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## HPC Pack 和 Linux 的注意事项

[HPC Pack](https://technet.microsoft.com/zh-cn/library/jj899572.aspx) 是 Microsoft 免费提供的 HPC 群集和作业管理解决方案。最新版本的 HPC Pack 2012 R2 支持多个 Linux 分发在 Windows Server 头节点管理的 Azure VM 中部署的计算节点上运行。部署在 A8 或 A9 VM 上并运行支持 MPI 实现的 Linux 计算节点可以运行访问 RDMA 网络的 MPI 应用程序。若要开始使用，请参阅 [Get started with Linux compute nodes in an HPC Pack cluster in Azure（Azure 的 HPC Pack 群集中的 Linux 计算节点入门）](/documentation/articles/virtual-machines-linux-classic-hpcpack-cluster)。

## 网络拓扑注意事项

* 在 Azure 中的 A8 或 A9 大小 Linux VM 上，在 Linux VM 上，Eth1 保留用于传输 RDMA 网络流量。不要更改引用此网络的配置文件中的任何 Eth1 设置或任何信息。Eth0 保留用于传输常规 Azure 网络流量。

* 在 Azure 中，不支持 IP over Infiniband (IB)。仅支持 RDMA over IB。


## 后续步骤

* 有关 A8、A9、A10 和 A11 实例的可用性和定价的详细信息，请参阅 [Virtual Machines pricing（虚拟机定价）](/home/features/virtual-machines/#price)。

* 有关 VM 存储容量和磁盘详细信息，请参阅 [Sizes for virtual machines（虚拟机大小）](/documentation/articles/virtual-machines-linux-sizes)。

* 若要开始在 Linux 上部署和使用包含 RDMA 的 A8 和 A9 实例，请参阅 [Set up a Linux RDMA cluster to run MPI applications（设置 Linux RDMA 群集以运行 MPI 应用程序）](/documentation/articles/virtual-machines-linux-classic-rdma-cluster)。



<!---HONumber=Mooncake_0620_2016-->