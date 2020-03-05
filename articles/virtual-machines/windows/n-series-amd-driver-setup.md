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
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269438"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 Vm 上安装 AMD GPU 驱动程序

若要利用运行 Windows 的新 Azure NVv4 系列 Vm 的 GPU 功能，必须安装 AMD GPU 驱动程序。 接下来的几周内将推出 AMD 驱动程序扩展。 本文提供了受支持的操作系统、驱动程序以及手动安装和验证步骤。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| OS | 驱动程序 |
| -------- |------------- |
| Windows 10 EVD-内部版本1903 <br/><br/>Windows 10-内部版本1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

2. 如果你是 NVv4 preview 客户，请停止 VM，并等待它移动到 "已停止（已解除分配）" 状态。

3. 请启动 VM，然后通过运行位于文件夹 ". ..\AMDCleanUninstallUtility" 的 "amdcleanuputility-x64" 来卸载预览驱动程序。 确切的路径将取决于先前驱动程序安装文件的位置。  

4. 下载并安装最新的驱动程序。

5. 重启 VM。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例演示了如何成功配置 Azure NVv4 VM 上的 Radeon Instinct MI25 卡。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/device-manager.png)

可以使用 dxdiag 来验证 GPU 显示属性，包括视频 RAM。 以下示例显示了 Azure NVv4 VM 上的 Radeon Instinct MI25 卡的 1/8 分区。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag.png)
