---
title: "include 文件"
description: "include 文件"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA Tesla 驱动程序

| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> 在本文发布时，Tesla 驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](http://www.nvidia.com/) 网站。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驱动程序

| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2016 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 5.2 (386.09)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft 将重新分发适用于 NV VM 的 NVIDIA GRID 驱动程序安装程序。 在 Azure NV VM 上仅安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID 虚拟 GPU 软件的授权。
>