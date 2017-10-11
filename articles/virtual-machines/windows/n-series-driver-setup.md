---
title: "适用于 Windows 的 Azure N 系列驱动程序安装 | Microsoft Docs"
description: "如何为 Azure 中运行 Windows 的 N 系列 VM 安装 NVIDIA GPU 驱动程序"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b480d10df777a2757c073ff77e1845d33d63163a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>为运行 Windows Server 的 N 系列 VM 安装 GPU 驱动程序
若要利用运行 Windows Server 2016 或 Windows Server 2012 R2 的 Azure N 系列 VM 的 GPU 功能，请安装支持的 NVIDIA 图形驱动程序。 部署 N 系列 VM 后，本文提供了驱动程序安装步骤。 针对 [Linux VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 也提供了驱动程序安装信息。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 


[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]



## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 N 系列 VM。

2. 下载、解压缩并安装 Windows 操作系统支持的驱动程序。

在 Azure NV VM 上，必须在安装驱动程序后重新启动。 在 NC VM 上，不需重新启动。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例展示了如何在 Azure NC VM 上成功配置 Tesla K80 卡。

![GPU 驱动程序属性](./media/n-series-driver-setup/GPU_driver_properties.png)

若要查询 GPU 设备状态，请运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。

1. 打开命令提示符，并更改为 C:\Program Files\NVIDIA Corporation\NVSMI 目录。

2. 运行 nvidia-smi。 如果安装了驱动程序，将看到如下输出。 请注意，除非当前正在 VM 上运行 GPU 工作负荷，否则 GPU-Util 将显示 0%。

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>NC24r VM 的 RDMA 网络

可以在同一可用性集中部署的 NC24r VM 上启用 RDMA 网络连接。 必须添加 HpcVmDrivers 扩展才能安装用来启用 RDMA 连接的 Windows 网络设备驱动程序。 若要向 NC24r VM 添加 VM 扩展，请使用 Azure Resource Manager 的 [Azure PowerShell](/powershell/azure/overview) cmdlet。

> [!NOTE]
> 当前，只有 Windows Server 2012 R2 支持 NC24r VM 上的 RDMA 网络。
> 

若要在 West US 区域中名为 myVM 的支持 RDMA 的现有 VM 上安装最新版本 1.1 HpcVMDrivers 扩展，请执行以下命令：
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  有关详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

对于使用 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 或 Intel MPI 5.x 运行的应用程序，RDMA 网络支持消息传递接口 (MPI) 流量。 


## <a name="next-steps"></a>后续步骤

* 有关 N 系列 VM 上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）

* 为 NVIDIA Tesla GPU 构建 GPU 加速应用程序的开发人员也可下载并安装适用于 [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) 或 [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe) 的 CUDA 工具包 8。 有关详细信息，请参阅 [CUDA 安装指南](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)。


