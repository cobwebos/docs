---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 04b085d2e990a580ddc99acb3b83ac8bd8ac2db3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "86998965"
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驱动程序

仅下表中列出的操作系统支持适用于 NC、NCv2、NCv3、ND 和 NDv2 系列 VM 的 NVIDIA Tesla (CUDA) 驱动程序（对 NV 系列为可选项）。 在本文发布时，驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](https://www.nvidia.com/) 网站。

> [!TIP]
> 作为一种在 Windows Server VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Windows Server 2016 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。


| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或虚拟应用程序的 NV 和 NVv3 系列 Vm 重新分发 NVIDIA 网格驱动程序安装程序。 仅在 Azure NV 系列 Vm 上安装这些网格驱动程序，且仅在下表中列出的操作系统上安装。 这些驱动程序包括 Azure 中 GRID Virtual GPU Software 的许可。 无需设置 NVIDIA vGPU 软件许可证服务器。

Azure 重新分发的网格驱动程序不适用于非 NV 系列 Vm，如 NC、NCv2、NCv3、ND 和 NDv2 系列 Vm。

请注意，Nvidia 扩展将始终安装最新的驱动程序。 我们在此处提供了与旧版本相关的客户的以前版本的链接。

对于 Windows Server 2019、Windows Server 2016 和 Windows 10 （最多生成2004）：
- [GRID 11 （451.48）](https://go.microsoft.com/fwlink/?linkid=874181) （.exe）
- [网格10.1 （442.06）](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) （.exe） 

对于 Windows Server 2012 R2： 
- [GRID 11 （451.48）](https://go.microsoft.com/fwlink/?linkid=874184) （.exe）
- [网格10.1 （442.66）](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) （.exe）  
