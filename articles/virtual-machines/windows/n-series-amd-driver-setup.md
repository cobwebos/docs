---
title: 适用于 Windows 的 Azure N 系列 AMD GPU 驱动程序设置
description: 如何为在 Azure 中运行 Windows Server 或 Windows 的 N 系列 Vm 设置 AMD GPU 驱动程序
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197996"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 Vm 上安装 AMD GPU 驱动程序

若要利用运行 Windows 的新 Azure NVv4 系列 Vm 的 GPU 功能，必须安装 AMD GPU 驱动程序。 [AMD Gpu 驱动程序扩展](../extensions/hpccompute-amd-gpu-windows.md)在 NVV4 系列 VM 上安装 AMD gpu 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 请参阅[AMD GPU 驱动程序扩展文档](../extensions/hpccompute-amd-gpu-windows.md)以了解支持的操作系统和部署步骤。

如果选择手动安装 AMD GPU 驱动程序，本文将提供受支持的操作系统、驱动程序以及安装和验证步骤。

只有 Microsoft 发布的 GPU 驱动程序在 NVv4 Vm 上受支持。 请不要从任何其他源安装 GPU 驱动程序。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows 10 EVD-内部版本1903 <br/><br/>Windows 10-内部版本1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) （.exe） |


## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

2. 如果你是 NVv4 preview 客户，请停止 VM，并等待它移动到 "已停止（已解除分配）" 状态。

3. 请启动 VM，并下载最新的[AMD 清理实用程序](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)。 通过运行 "amdcleanuputility-x64" 卸载现有驱动程序。 请不要使用随先前驱动程序一起安装的任何现有清理实用程序。  

4. 下载并安装最新的驱动程序。

5. 重启 VM。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例演示了如何成功配置 Azure NVv4 VM 上的 Radeon Instinct MI25 卡。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/device-manager.png)

可以使用 dxdiag 来验证 GPU 显示属性，包括视频 RAM。 以下示例显示了 Azure NVv4 VM 上的 Radeon Instinct MI25 卡的1/2 分区。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-output.png)

如果运行的是 Windows 10 内部版本1903或更高版本，则 dxdiag 在 "显示" 选项卡中将不显示任何信息。请使用底部的 "保存所有信息" 选项，输出文件将显示与 AMD MI25 GPU 相关的信息。

![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-details.png)


