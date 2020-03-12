---
title: H 系列-Azure 虚拟机
description: H 系列 Vm 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: a71b7b7de6f6039106b43576847675f48de803c8
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088065"
---
# <a name="h-series"></a>H 系列

H 系列 Vm 针对按高 CPU 频率驱动的应用程序和每个核心要求提供较大内存的优化。 H 系列 Vm 功能8或 16 Intel 至强 E5 2667 v3 处理器核心，每个 CPU 核心最多 14 GB RAM，无超线程。 H 系列功能 56 Gb/秒，FDR 不会阻止 fat 树配置，以实现一致的 RDMA 性能。 H 系列 Vm 支持 Intel MPI 1.x 和 MS-CHAP。

ACU：290-300

高级存储：不支持

高级存储缓存：不支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/秒 | 基本 CPU 频率（GHz） | 所有核心频率（GHz、峰值） | 单核频率（GHz、峰值） | RDMA 性能（Gb/秒） | MPI 支持 | 临时存储（GB） | 最大数据磁盘数 | 最大以太网 Nic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel 至强 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | Intel 至强 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel 至强 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel 至强 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 1.x，MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>对于 MPI 应用程序，专用 RDMA 后端网络是通过 FDR 无限网络启用的。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>支持的 OS 映像（Linux）
 
Azure Marketplace 提供了许多支持 RDMA 连接的 Linux 分发版：
  
* **基于 CentOS 的 HPC** -适用于不支持 Sr-iov 的 vm、基于 CentOS 的版本 6.5 HPC 或更高版本，最高可达7.5。 对于 H 系列 Vm，建议使用版本7.1 至7.5。 在 VM 上安装 RDMA 驱动程序和 Intel MPI 5.1。
  对于 SR-IOV Vm，CentOS-HPC 7.6 已进行了优化，并预先加载了 RDMA 驱动程序和各种 MPI 包。
  对于其他 RHEL/CentOS VM 映像，请添加 InfiniBandLinux 扩展来启用不允许的。 此 Linux VM 扩展为 RDMA 连接性安装 Mellanox OFED 驱动程序（位于 SR-IOV Vm 上）。 以下 PowerShell cmdlet 将在支持 RDMA 的现有 VM 上安装 InfiniBandDriverLinux 扩展的最新版本（版本1.0）。 支持 RDMA 的 VM 的名称为*myVM* ，并部署在*美国西部*区域中名为*myResourceGroup*的资源组中，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  此外，也可以在 Azure 资源管理器模板中包含 VM 扩展，以便通过以下 JSON 元素进行部署：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  以下命令在名为*myResourceGroup*的资源组中部署的名为*myVMSS*的现有虚拟机规模集中的所有支持 RDMA 的 vm 上安装最新版本 1.0 InfiniBandDriverLinux 扩展。
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序以 RPM 包的形式分发，如果更新了内核，驱动程序更新可能不起作用。
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 for HPC、SLES 12 sp3 for Hpc （高级版）、SLES 12 SP1 for HPC、SLES 12 SP1 for Hpc （高级版）、SLES 12 SP4 和 sles 15。 安装 RDMA 驱动程序，并在 VM 上分发 Intel MPI 包。 运行以下命令安装 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -ubuntu SERVER 16.04 LTS、18.04 LTS。 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  有关启用无法使用的详细信息，[请参阅设置](/workloads/hpc/enable-infiniband.md)MPI。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
