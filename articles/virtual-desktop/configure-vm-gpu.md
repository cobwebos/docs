---
title: 为 Windows 虚拟桌面配置 GPU-Azure
description: 如何在 Windows 虚拟桌面中启用 GPU 加速呈现和编码。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: aae3f8b1cfe224f0a948eb16bd6ee5120b19dde1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612072"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>配置 Windows 虚拟桌面的图形处理单元（GPU）加速

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面支持 GPU 加速呈现和编码，以提高应用程序的性能和可伸缩性。 GPU 加速对于图形密集型应用尤其重要。

按照本文中的说明创建一个 GPU 优化的 Azure 虚拟机，将其添加到你的主机池，并将其配置为使用 GPU 加速进行呈现和编码。 本文假设你已配置 Windows 虚拟桌面租户。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>选择 GPU 优化的 Azure 虚拟机大小

Azure 提供了大量[GPU 优化的虚拟机大小](/azure/virtual-machines/windows/sizes-gpu)。 主机池的正确选择取决于多种因素，包括特定的应用工作负荷、所需的用户体验质量和成本。 通常，更大和更强大的 Gpu 在给定用户的密度下提供更好的用户体验。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>创建主机池、设置虚拟机并配置应用组

使用所选大小的 VM 创建新的主机池。 有关说明，请参阅[教程：使用 Azure 门户创建主机池](/azure/virtual-desktop/create-host-pools-azure-marketplace)。

Windows 虚拟桌面支持在以下操作系统中执行 GPU 加速呈现和编码：

* Windows 10 版本1511或更高版本
* Windows Server 2016 或更高版本

你还必须配置应用组，或使用创建新主机池时自动创建的默认桌面应用组（名为 "桌面应用程序组"）。 有关说明，请参阅[教程：管理适用于 Windows 虚拟桌面的应用组](/azure/virtual-desktop/manage-app-groups)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在虚拟机中安装支持的图形驱动程序

若要利用 Windows 虚拟桌面中 Azure N 系列 Vm 的 GPU 功能，必须安装相应的图形驱动程序。 按照[支持的操作系统和驱动程序](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers)中的说明，手动或使用 Azure VM 扩展从适当的图形供应商处安装驱动程序。

Windows 虚拟桌面仅支持 Azure 分发的驱动程序。 此外，对于具有 NVIDIA Gpu 的 Azure Vm，Windows 虚拟桌面只支持[NVIDIA 网格驱动程序](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)。

驱动程序安装完成后，需要重新启动 VM。 使用上述说明中的验证步骤确认图形驱动程序已成功安装。

## <a name="configure-gpu-accelerated-app-rendering"></a>配置 GPU 加速应用渲染

默认情况下，在多会话配置中运行的应用和桌面使用 CPU 呈现，并且不会利用可用 Gpu 进行呈现。 为会话主机配置组策略以启用 GPU 加速呈现：

1. 使用具有本地管理员特权的帐户连接到 VM 的桌面。
2. 打开 "开始" 菜单，键入 "gpedit.msc" 以打开组策略编辑器。
3.  > **管理模板** > **Remote Desktop Session Host****Windows Components** >  **Computer Configuration** > Windows 组件 > **远程桌面服务**远程桌面会话主机**远程会话环境**导航到计算机配置。
4. 选择 **"策略" 使用所有远程桌面服务会话的硬件默认图形适配器**，并将此策略设置为 "**启用**" 以在远程会话中启用 GPU 呈现。

## <a name="configure-gpu-accelerated-frame-encoding"></a>配置 GPU 加速帧编码

远程桌面对应用和桌面（无论是使用 GPU 还是 CPU 呈现）呈现的所有图形进行编码以便传输到远程桌面客户端。 默认情况下，远程桌面不会将可用 Gpu 用于此编码。 为会话主机配置组策略以启用 GPU 加速帧编码。 继续执行以上步骤：

1. 选择 "策略"**确定远程桌面连接的 AVC 444 图形模式**，并将此策略设置为 "**启用**"，以在远程会话中强制执行 h.264/AVC 444 编解码器。
2. 选择 "策略"**配置远程桌面连接的 AVC 硬件编码**，并将此策略设置为 "**已启用**" 以在远程会话中启用 AVC/h-p 硬件编码。

    >[!NOTE]
    >在 Windows Server 2016 中，set 选项**倾向于使用 AVC 硬件编码**来**始终尝试**。

3. 现在已经编辑了组策略，请强制执行组策略更新。 打开命令提示符并键入：

    ```batch
    gpupdate.exe /force
    ```

4. 从远程桌面会话注销。

## <a name="verify-gpu-accelerated-app-rendering"></a>验证 GPU 加速应用程序呈现

若要验证应用是否正在使用 GPU 进行呈现，请尝试执行以下任一操作：

* 对于带有 NVIDIA GPU 的 Azure Vm，请使用`nvidia-smi`在运行应用时[验证驱动程序安装](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)中所述的实用程序来检查 GPU 使用率。
* 在支持的操作系统版本上，可以使用任务管理器来检查 GPU 利用率。 在 "性能" 选项卡中选择 GPU，查看应用是否正在使用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>验证 GPU 加速帧编码

验证远程桌面是否正在使用 GPU 加速编码：

1. 使用 Windows 虚拟桌面客户端连接到 VM 的桌面。
2. 启动事件查看器并导航到以下节点：**应用程序和服务日志** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **操作**
3. 若要确定是否使用 GPU 加速编码，请查找事件 ID 170。 如果看到 "AVC 硬件编码器已启用： 1"，则使用 GPU 编码。
4. 若要确定是否使用了 AVC 444 模式，请查找事件 ID 162。 如果出现 "AVC 可用：1初始配置文件： 2048"，则使用 AVC 444。

## <a name="next-steps"></a>后续步骤

这些说明应指导您在一个会话主机（一个 VM）上运行 GPU 加速。 在更大的主机池中启用 GPU 加速的一些其他注意事项：

* 请考虑使用[VM 扩展](/azure/virtual-machines/extensions/overview)简化多个 vm 的驱动程序安装和更新。 将[NVIDIA Gpu 驱动程序扩展](/azure/virtual-machines/extensions/hpccompute-gpu-windows)用于具有 NVIDIA Gpu 的 vm，并为具有 amd Gpu 的 VM 使用 Amd Gpu 驱动程序扩展（即将推出）。
* 请考虑使用 Active Directory 组策略来简化多个 Vm 上的组策略配置。 有关在 Active Directory 域中部署组策略的信息，请参阅使用[组策略对象](https://go.microsoft.com/fwlink/p/?LinkId=620889)。
