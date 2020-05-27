---
title: 适用于 Windows 的 Azure N 系列 AMD GPU 驱动程序安装
description: 如何为 Azure 中运行 Windows Server 或 Windows 的 N 系列 VM 安装 AMD GPU 驱动程序
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 745ec7ebf792fe1165022516be4c83fb9e864cc9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799883"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>在运行 Windows 的 N 系列 VM 上安装 AMD GPU 驱动程序

若要利用运行 Windows 的全新 Azure NVv4 系列 VM 的 GPU 功能，必须安装 AMD GPU 驱动程序。 [AMD GPU 驱动程序扩展](../extensions/hpccompute-amd-gpu-windows.md)会在 NVv4 系列 VM 上安装 AMD GPU 驱动程序。 请使用 Azure 门户或工具（例如 Azure PowerShell 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的操作系统和部署步骤，请参阅 [AMD GPU 驱动程序扩展文档](../extensions/hpccompute-amd-gpu-windows.md)。

如果选择手动安装 AMD GPU 驱动程序，请参阅本文，其中提供了受支持的操作系统、驱动程序以及安装和验证步骤。

NVv4 VM 仅支持 Microsoft 发布的 GPU 驱动程序。 请勿从任何其他源安装 GPU 驱动程序。

有关基本规范、存储容量和磁盘详细信息，请参阅 [GPU Windows VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。



## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

| OS | 驱动程序 |
| -------- |------------- |
| Windows 10 EVD - 版本 1903 <br/><br/>Windows 10 - 版本 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>驱动程序安装

1. 通过远程桌面连接到每个 NVv4 系列 VM。

2. 下载并安装最新版驱动程序。

3. 重启 VM。

## <a name="verify-driver-installation"></a>验证驱动程序安装

可以在设备管理器中验证驱动程序安装。 以下示例展示了如何在 Azure NVv4 VM 上成功配置 Radeon Instinct MI25 卡。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/device-manager.png)

可以使用 dxdiag 验证 GPU 显示属性（包括视频 RAM）。 以下示例显示了 Azure NVv4 VM 上 Radeon Instinct MI25 卡的 1/2 分区。
<br />
![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-output.png)

如果运行的是 Windows 10 内部版本 1903 或更高版本，则 dxdiag 不会在“显示”选项卡中显示任何信息。请使用底部的“保存所有信息”选项，输出文件会显示与 AMD MI25 GPU 相关的信息。

![GPU 驱动程序属性](./media/n-series-amd-driver-setup/dxdiag-details.png)


