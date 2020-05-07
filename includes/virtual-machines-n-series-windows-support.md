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
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77135069"
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

请注意，Nvidia 扩展将始终安装最新的驱动程序。 我们在此处提供了与旧版本相关的客户的以前版本的链接。

对于 Windows Server 2019、Windows Server 2016 和 Windows 10 （最多生成1909）：
- [网格10.1 （442.06）](https://go.microsoft.com/fwlink/?linkid=874181) （.exe）
- [网格10.0 （441.66）](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) （.exe） 

对于 Windows Server 2012 R2、Windows Server 2008 R2、Windows 8 和 Windows 7： 
- [网格10.1 （442.06）](https://go.microsoft.com/fwlink/?linkid=874184) （.exe）
- [网格10.0 （441.66）](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) （.exe）  
