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
ms.date: 11/30/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 4bf470737f961219250c37d890ed81eb62409626
ms.openlocfilehash: feb9370f0241fd860749d32f5db5842cb18463f1


---
# <a name="set-up-gpu-drivers-for-n-series-vms"></a>为 N 系列的 VM 安装 GPU 驱动程序
若要利用运行 Windows Server 的 Azure N 系列 VM 的 GPU 功能，部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 本文同样适用于 [Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

有关基本规范、存储容量和磁盘详细信息，请参阅[虚拟机的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




## <a name="supported-gpu-drivers"></a>支持的 GPU 驱动程序

通过远程桌面连接到每个 N 系列 VM。 下载、解压缩并安装 Windows 操作系统支持的驱动程序。 

### <a name="nvidia-grid-drivers-for-nv-vms"></a>适用于 NV VM 的 NVIDIA GRID 驱动程序

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836843) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>适用于 NC VM 的 NVIDIA Tesla 驱动程序

* [Windows Server 2016](https://go.microsoft.com/fwlink/?linkid=836841) (.zip)

* [Windows Server 2012 R2](https://go.microsoft.com/fwlink/?linkid=836842) (.zip)



## <a name="verify-driver-installation"></a>验证驱动程序安装

在 Azure NV VM 上，必须在安装驱动程序后重新启动。 在 NC VM 上，不需重新启动。

可以在设备管理器中验证驱动程序安装。 以下示例演示了如何在 Azure NC VM 上成功配置 K80 卡。

![GPU 驱动程序属性](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

若要查询 GPU 设备状态，请运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

![NVIDIA 设备状态](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="next-steps"></a>后续步骤

* 有关 N 系列虚拟机上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）

* 为 NVIDIA Tesla GPU 构建 GPU 加速应用程序的开发人员也可下载并安装 [CUDA 工具包 8](https://developer.nvidia.com/cuda-downloads)。





<!--HONumber=Dec16_HO2-->


