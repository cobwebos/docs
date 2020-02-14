---
title: 适用于 Windows 的 Azure N 系列 AMD GPU 驱动程序设置
description: 如何为在 Azure 中运行 Windows Server 或 Windows 的 N 系列 Vm 设置 AMD GPU 驱动程序
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: fdc6834f3fb5ee97f27a6397645b965863e90a6b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190541"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 Vm 上安装 AMD GPU 驱动程序

若要利用运行 Windows 的新 Azure NVv4 系列 Vm 的 GPU 功能，必须安装 AMD GPU 驱动程序。 接下来的几周内将推出 AMD 驱动程序扩展。 本文提供了受支持的操作系统、驱动程序以及手动安装和验证步骤。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| OS | 驱动程序 |
| -------- |------------- |
| Windows 10 EVD-内部版本1903 <br/><br/>Windows 10-内部版本1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) （.exe） |

## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

1. 下载并提取驱动程序安装程序文件。 导航到文件夹并运行 "setup.exe"，以安装 Windows 操作系统支持的驱动程序。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例演示了如何成功配置 Azure NVv4 VM 上的 Radeon Instinct MI25 卡。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/device-manager.png)

可以使用 dxdiag 来验证 GPU 显示属性，包括视频 RAM。 以下示例显示了 Azure NVv4 VM 上的 Radeon Instinct MI25 卡的 1/8 分区。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag.png)
