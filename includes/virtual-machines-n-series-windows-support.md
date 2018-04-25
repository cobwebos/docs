---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 689e45f96ed5e7aaea3aecd1193e9cd58f10e80d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA Tesla (CUDA) 驱动程序

在本文发布时，下表中的驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](http://www.nvidia.com/) 网站。

> [!TIP]
> 作为一种在 Windows Server VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Windows Server 2016 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。


| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驱动程序

Microsoft 将重新分发适用于 NV VM 的 NVIDIA GRID 驱动程序安装程序。 在 Azure NV VM 上仅安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID 虚拟 GPU 软件的授权。

| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |