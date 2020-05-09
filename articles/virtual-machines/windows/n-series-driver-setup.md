---
title: 适用于 Windows 的 Azure N 系列 NVIDIA GPU 驱动程序设置
description: 如何为 Azure 中运行 Windows Server 或 Windows 的 N 系列 VM 安装 NVIDIA GPU 驱动程序
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0b00b2340445e7d103c78c1d8c1ea0e08ecabba5
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628106"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序 

若要利用 NVIDIA Gpu 支持的 Azure N 系列 Vm 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。 [NVIDIA GPU 驱动程序扩展](../extensions/hpccompute-gpu-windows.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](../extensions/hpccompute-gpu-windows.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，本文将提供受支持的操作系统、驱动程序以及安装和验证步骤。 针对 [Linux VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 也提供了驱动程序手动安装信息。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 N 系列 VM。

2. 下载、解压缩并安装 Windows 操作系统支持的驱动程序。

在 VM 上安装 GRID 驱动程序后，需要重启。 安装 CUDA 驱动程序后，不需要重启。

## <a name="verify-driver-installation"></a>验证驱动程序安装

请注意，只有安装了网格驱动程序才能访问 Nvidia 控制面板。 如果已安装 CUDA 驱动程序，则 Nvidia 控制面板将不可见。

可以在设备管理器中验证驱动程序安装。 以下示例展示了如何在 Azure NC VM 上成功配置 Tesla K80 卡。

![GPU 驱动程序属性](./media/n-series-driver-setup/GPU_driver_properties.png)

若要查询 GPU 设备状态，请运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。

1. 打开命令提示符，并更改为 C:\Program Files\NVIDIA Corporation\NVSMI**** 目录。

2. 运行 `nvidia-smi`。 如果安装了驱动程序，将看到如下输出。 除非当前正在 VM 上运行 GPU 工作负荷，否则“GPU-Util”将显示“0%”********。 驱动程序版本和 GPU 详细信息可能与所示的内容不同。

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA 网络连接

可以在同一可用性集或虚拟机规模集的单个放置组中部署的支持 RDMA 的 N 系列 VM（例如 NC24r）上启用 RDMA 网络连接。 必须添加 HpcVmDrivers 扩展才能安装用来启用 RDMA 连接的 Windows 网络设备驱动程序。 若要向支持 RDMA 的 N 系列 VM 添加 VM 扩展，请使用 Azure 资源管理器的 [Azure PowerShell](/powershell/azure/overview) cmdlet。

若要在 West US 区域中名为 myVM 的支持 RDMA 的现有 VM 上安装最新版本 1.1 HpcVMDrivers 扩展，请执行以下命令：
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  有关详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](extensions-features.md)。

对于使用 [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) 或 Intel MPI 5.x 运行的应用程序，RDMA 网络支持消息传递接口 (MPI) 流量。 


## <a name="next-steps"></a>后续步骤

* 为 NVIDIA Tesla GPU 构建 GPU 加速应用程序的开发人员也可下载并安装最新的 [CUDA 工具包](https://developer.nvidia.com/cuda-downloads)。 有关详细信息，请参阅 [CUDA 安装指南](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)。


