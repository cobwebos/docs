---
title: 启用不允许的 H 系列和 N 系列 Azure 虚拟机的配置和优化
description: 了解如何配置和优化启用了 "不支持" 的 H 系列和 N 系列虚拟机。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/07/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d4661c0819d214a2c750eb1582559f8d8a5959ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88006598"
---
# <a name="configure-and-optimize-vms"></a>配置和优化 VM

本文介绍了如何配置和优化已启用未支持的 [H 系列的 H 系列](../../sizes-hpc.md) 和 [N 系列](../../sizes-gpu.md) vm，并对其进行了优化。

## <a name="vm-images"></a>VM 映像
在启用了支持的 Vm 上，启用 RDMA 需要相应的驱动程序。 在 Linux 上，Marketplace 中的 CentOS-HPC VM 映像已预先配置了适当的驱动程序。 可以使用 [此处的说明](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)，使用正确的驱动程序配置 Ubuntu VM 映像。 还建议使用适当的驱动程序和配置创建 [自定义 VM 映像](../../linux/tutorial-custom-images.md) ，并重复使用这些 recurringly。

> [!NOTE]
> 在启用 GPU 的 [N 系列](../../sizes-gpu.md) vm 上，另外还需要提供相应的 GPU 驱动程序，这些驱动程序可以通过 [VM 扩展](../../extensions/hpccompute-gpu-linux.md) 或 [手动](../../linux/n-series-driver-setup.md)添加。 Marketplace 中的某些 VM 映像还随 Nvidia GPU 驱动程序预装。

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 映像

#### <a name="non-sr-iov-enabled-vms"></a>未启用 SR-IOV 的 Vm
对于不支持 SR-IOV 的支持 [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)，CentOS 版本6.5 或更高版本，适用于 Marketplace 中的最大为7.5。 例如，对于 [H16 系列 vm](../../h-series.md)，建议使用版本7.1 至7.5。 这些 VM 映像已预先加载到 RDMA 和 Intel MPI 版本5.1 的网络直接驱动程序中。

> [!NOTE]
> 对于启用了非 SR-IOV 的虚拟机的这些基于 CentOS 的 HPC 映像，将在 **yum** 配置文件中禁用内核更新。 这是因为 NetworkDirect Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能不起作用。

#### <a name="sr-iov-enabled-vms"></a>启用 SR-IOV 的 Vm
  对于启用了 SR-IOV 的支持 [RDMA 的 vm](../../sizes-hpc.md#rdma-capable-instances)，适用于 Marketplace 中的 [CentOS 版本7.6 或更高](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) 版本 VM 映像。 这些 VM 映像经过优化，并预先加载了 RDMA 的 OFED 驱动程序和各种常用的 MPI 库和科学计算包，并且是开始使用的最简单的方法。

  在 [azhpc](https://github.com/Azure/azhpc-images/tree/master/centos)存储库中创建 CENTOS-HPC 7.6 版和更高版本的 VM 映像时使用的脚本示例。

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 映像
可以配置 Marketplace 上基于 RHEL 或 CentOS 的非 HPC VM 映像，以便在支持支持 [RDMA 的虚拟机](../../sizes-hpc.md#rdma-capable-instances)上使用。 详细了解如何在 Vm 上 [启用无限](enable-infiniband.md) 和 [设置 MPI](setup-mpi.md) 。

  在 [azhpc](https://github.com/Azure/azhpc-images/tree/master/centos)存储库中创建 CENTOS-HPC 7.6 版和更高版本的 VM 映像时使用的脚本示例。

### <a name="ubuntu-vm-images"></a>Ubuntu VM 映像
支持在 Marketplace 中推出的 Ubuntu Server 16.04 LTS、18.04 LTS 和 20.04 LTS VM 映像，适用于 SR-IOV 和非 SR-IOV RDMA 支持的 [vm](../../sizes-hpc.md#rdma-capable-instances)。 详细了解如何在 Vm 上 [启用无限](enable-infiniband.md) 和 [设置 MPI](setup-mpi.md) 。

  在 [azhpc](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc)存储库中，可用于创建基于 UBUNTU 18.04 LTS 的 HPC VM 映像的脚本示例。

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 映像
SLES 12 SP3 for HPC、SLES 12 SP3 for hpc (Premium) 、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium) 、SLES 12 SP4 以及 Marketplace 中的 SLES 15 VM 映像均受支持。 这些 VM 映像已预先加载到 RDMA 和 Intel MPI 版本5.1 的网络直接驱动程序中。 了解有关在 Vm 上 [设置 MPI](setup-mpi.md) 的详细信息。

## <a name="optimize-vms"></a>优化 Vm

下面是一些可选的优化设置，可提高 VM 的性能。

### <a name="update-lis"></a>更新 .LIS

如果功能或性能需要， [Linux Integration Services (.lis) 驱动程序](../../linux/endorsed-distros.md) 可以在受支持的 OS 发行版上安装或更新，尤其是使用自定义映像或更早版本的 CENTOS/RHEL 1.x 或早期版本的来部署。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>回收内存

通过自动回收内存来提高性能，以避免远程内存访问。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

要使其在 VM 重新启动后保持不变：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>禁用防火墙和 SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>禁用 cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>配置 WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
（可选）可以将 WALinuxAgent 作为预作业步骤禁用，并将其恢复后作业，以便将最大 VM 资源可用性恢复到 HPC 工作负荷。


## <a name="next-steps"></a>后续步骤

- 详细了解如何对启用了未支持的[H 系列](../../sizes-hpc.md)和[N 系列](../../sizes-gpu.md)vm[启用允许](enable-infiniband.md)。
- 详细了解如何在 Vm 上安装各种 [受支持的 MPI 库](setup-mpi.md) 及其最佳配置。
- 查看 [HB 系列概述](hb-series-overview.md)和 [HC 系列概述](hc-series-overview.md)，以了解如何对工作负载进行优化配置以提高性能和可伸缩性。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 若要从体系结构角度更概略性地看待如何运行 HPC 工作负荷，请参阅 [Azure 上的高性能计算 (HPC)](/azure/architecture/topics/high-performance-computing/)。
