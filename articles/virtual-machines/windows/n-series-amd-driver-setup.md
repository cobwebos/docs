---
title: 适用于 Windows 的 Azure N 系列 AMD GPU 驱动程序设置
description: 如何为在 Azure 中运行 Windows 服务器或 Windows 的 N 系列 VM 设置 AMD GPU 驱动程序
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
ms.openlocfilehash: 02213feb507e9a032a50241fddf31714b9dfd7ee
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011051"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 VM 上安装 AMD GPU 驱动程序

要利用运行 Windows 的新 Azure NVv4 系列 VM 的 GPU 功能，必须安装 AMD GPU 驱动程序。 AMD 驱动程序扩展将在未来几周内提供。 本文提供了支持的操作系统、驱动程序以及手动安装和验证步骤。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| (OS) | 驱动程序 |
| -------- |------------- |
| Windows 10 EVD - 生成 1903 <br/><br/>Windows 10 - 生成 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

2. 如果您是 NVv4 预览客户，请停止 VM 并等待它移动到"已停止（已转移）"状态。

3. 请启动 VM 并下载最新的[AMD 清理实用程序](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。 通过运行"amdcleanup 实用程序-x64.exe"卸载现有驱动程序。 请不要使用与前一个驱动程序一起安装的任何现有清理实用程序。  

4. 下载并安装最新的驱动程序。

5. 重启 VM。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 下面的示例显示了 Azure NVv4 VM 上 Radeon 本能 MI25 卡的成功配置。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/device-manager.png)

您可以使用 dxdiag 验证 GPU 显示属性，包括视频 RAM。 下面的示例显示了 Azure NVv4 VM 上的 Radeon 本能 MI25 卡的 1/2 分区。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-output.png)

如果您正在运行 Windows 10 版本 1903 或更高版本，则 dxdiag 将不会在"显示"选项卡中显示任何信息。请使用底部的"保存所有信息"选项，输出文件将显示与 AMD MI25 GPU 相关的信息。

![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-details.png)


