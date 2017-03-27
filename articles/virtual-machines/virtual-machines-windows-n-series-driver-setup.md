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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2dbd88a2a83643130c336253d2c7a5a21d73671e
ms.lasthandoff: 03/15/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>为运行 Windows Server 的 N 系列 VM 安装 GPU 驱动程序
若要利用运行 Windows Server 2016 或 Windows Server 2012 R2 的 Azure N 系列 VM 的 GPU 功能，在部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 针对 [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 也提供了驱动程序安装信息。

有关基本规范、存储容量和磁盘详细信息，请参阅[虚拟机的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 另请参阅 [N 系列 VM 的一般注意事项](#general-considerations-for-n-series-vms)。



## <a name="supported-gpu-drivers"></a>支持的 GPU 驱动程序

通过远程桌面连接到每个 N 系列 VM。 下载、解压缩并安装 Windows 操作系统支持的驱动程序。 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>适用于 NC VM 的 NVIDIA Tesla 驱动程序 (Tesla K80)



| 操作系统 | 驱动程序版本 |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (.exe) |


### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>适用于 NV VM 的 NVIDIA GRID 驱动程序 (Tesla M60)

| 操作系统 | 驱动程序版本 |
| -------- |------------- |
| Windows Server 2016 | [369.71](https://go.microsoft.com/fwlink/?linkid=836842) (.zip) |
| Windows Server 2012 R2 | [369.30](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)  |

> [!NOTE]
> 此处提供的驱动程序下载链接在本文发布时是最新的。 有关最新驱动程序，请访问 [NVIDIA](http://www.nvidia.com/) 网站。
>

## <a name="verify-gpu-driver-installation"></a>验证 GPU 驱动程序安装

在 Azure NV VM 上，必须在安装驱动程序后重新启动。 在 NC VM 上，不需重新启动。

可以在设备管理器中验证驱动程序安装。 以下示例展示了如何在 Azure NC VM 上成功配置 Tesla K80 卡。

![GPU 驱动程序属性](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

若要查询 GPU 设备状态，请运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

![NVIDIA 设备状态](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>NC24r VM 的 RDMA 网络

可以在同一可用性集中部署的 NC24r VM 上启用 RDMA 网络连接。 必须添加 HpcVmDrivers 扩展才能安装用来启用 RDMA 连接的 Windows 网络设备驱动程序。 若要向 NC24r VM 添加 VM 扩展，请使用 Azure Resource Manager 的 [Azure PowerShell](/powershell/azureps-cmdlets-docs) cmdlet。

> [!NOTE]
> 当前，只有 Windows Server 2012 R2 支持 NC24r VM 上的 RDMA 网络。
> 

若要在 West US 区域中名为 myVM 的支持 RDMA 的现有 VM 上安装最新版本 1.1 HpcVMDrivers 扩展，请执行以下命令：
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  有关详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

对于使用 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) 或 Intel MPI 5.x 运行的应用程序，RDMA 网络支持消息传递接口 (MPI) 流量。 

[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>后续步骤

* 有关 N 系列 VM 上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）

* 为 NVIDIA Tesla GPU 构建 GPU 加速应用程序的开发人员也可下载并安装适用于 [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) 或 [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe) 的 CUDA 工具包 8。 有关详细信息，请参阅 [CUDA 安装指南](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)。



