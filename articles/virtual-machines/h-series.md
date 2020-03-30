---
title: H 系列 - Azure 虚拟机
description: H 系列 VM 的规格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470497"
---
# <a name="h-series"></a>H 系列

H 系列 VM 针对由高 CPU 频率或每个核心要求的大内存驱动的应用进行了优化。 H 系列 VM 具有 8 或 16 英特尔至强 E5 2667 v3 处理器内核，每个 CPU 内核高达 14 GB 的 RAM，并且没有超线程。 H 系列采用 56 Gb/秒 Mellanox FDR InfiniBand，采用非阻塞脂肪树配置，具有一致的 RDMA 性能。 H 系列 VM 支持英特尔 MPI 5.x 和 MS-MPI。

ACU：290-300

高级存储：不支持

高级存储缓存：不支持

实时迁移：不支持

内存保留更新：不支持

| 大小 | vCPU | 处理器 | 内存 (GB) | 内存带宽 GB/s | 基本 CPU 频率 （GHz） | 全核频率（GHz，峰值） | 单核频率（GHz，峰值） | RDMA 性能（Gb/s） | MPI 支持 | 临时存储 （GB） | 最大数据磁盘数 | 最大以太网 NIC |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | 英特尔强盛 E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | 英特尔 5.x， MS-MPI | 1000 | 32 | 2 |
| Standard_H16  | 16 | 英特尔强盛 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | - | 英特尔 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H8m  | 8  | 英特尔强盛 E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | 英特尔 5.x， MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | 英特尔强盛 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | 英特尔 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | 英特尔强盛 E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | 英特尔 5.x， MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | 英特尔强盛 E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | 英特尔 5.x， MS-MPI | 2000 | 64 | 4 |

<sup>1</sup>对于 MPI 应用程序，FDR InfiniBand 网络启用专用 RDMA 后端网络。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>支持的操作系统映像（Linux）
 
Azure 应用商店有许多支持 RDMA 连接的 Linux 发行版：
  
* **基于 CentOS 的 HPC** - 对于启用非 SR-IOV 的 VM、基于 CentOS 的 6.5 HPC 版本或更高版本，最多适用于 7.5。 对于 H 系列 VM，建议版本 7.1 到 7.5。 在 VM 上安装 RDMA 驱动程序和 Intel MPI 5.1。
  对于 SR-IOV VM，CentOS-HPC 7.6 进行了优化并预加载了 RDMA 驱动程序和安装的各种 MPI 软件包。
  对于其他 RHEL/CentOS VM 映像，添加 InfiniBandLinux 扩展以启用 InfiniBand。 此 Linux VM 扩展安装 Mellanox OFED 驱动程序（在 SR-IOV VM 上）用于 RDMA 连接。 以下 PowerShell cmdlet 在现有支持 RDMA 的 VM 上安装 InfiniBandDriveLinux 扩展的最新版本（版本 1.0）。 支持 RDMA 的 VM 名为*myVM，* 部署在*美国西部*区域名为*myResourceGroup*的资源组中，如下所示：

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  或者，VM 扩展可以包含在 Azure 资源管理器模板中，以便使用以下 JSON 元素轻松部署：
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  以下命令在名为*myVMSS*的现有虚拟机规模集中的所有支持 RDMA 的 VM 上安装最新版本 1.0 InfiniBandDriveLinux 扩展，该组合部署在名为*myResourceGroup*的资源组中：
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > 在基于 CentOS 的 HPC 映像中，内核更新已在 **yum** 配置文件中禁用。 这是因为 Linux RDMA 驱动程序作为 RPM 包分发，如果内核更新，驱动程序更新可能不起作用。
  >
  

* **SUSE Linux 企业服务器**- SLES 12 SP3 用于 HPC，SLES 12 SP3 用于 HPC（高级）、SLES 12 SP1 用于 HPC（高级）、SLES 12 SP1（高级）、SLES 12 SP4 和 SLES 15。 安装 RDMA 驱动程序，并在 VM 上分发 Intel MPI 包。 运行以下命令安装 MPI：

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu 服务器 16.04 LTS，18.04 LTS。 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  有关启用 InfiniBand、设置 MPI 的更多详细信息，请参阅[启用 InfiniBand](./workloads/hpc/enable-infiniband.md)。

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
