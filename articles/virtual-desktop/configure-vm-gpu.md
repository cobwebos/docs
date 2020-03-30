---
title: 为 Windows 虚拟桌面配置 GPU - Azure
description: 如何在 Windows 虚拟桌面中启用 GPU 加速呈现和编码。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 941a1ff23668a3202028e8b693b57d902095b3b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78384828"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>为 Windows 虚拟桌面配置图形处理单元 （GPU） 加速

Windows 虚拟桌面支持 GPU 加速呈现和编码，以提高应用性能和可伸缩性。 GPU 加速对于图形密集型应用尤为重要。

按照本文中的说明创建 GPU 优化的 Azure 虚拟机，将其添加到主机池，并将其配置为使用 GPU 加速进行渲染和编码。 本文假定您已经配置了 Windows 虚拟桌面租户。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>选择 GPU 优化的 Azure 虚拟机大小

Azure 提供了许多[GPU 优化的虚拟机大小](/azure/virtual-machines/windows/sizes-gpu)。 主机池的正确选择取决于许多因素，包括您的特定应用工作负载、所需的用户体验质量和成本。 通常，更大、功能更强的 GPU 在给定用户密度下提供更好的用户体验。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>创建主机池、预配虚拟机和配置应用组

使用所选大小的 VM 创建新的主机池。 有关说明，请参阅[教程：使用 Azure 应用商店创建主机池](/azure/virtual-desktop/create-host-pools-azure-marketplace)。

Windows 虚拟桌面支持 GPU 加速呈现和编码在以下操作系统中：

* Windows 10 版本 1511 或更新
* Windows Server 2016 或更高版本

您还必须配置应用组，或使用在创建新主机池时自动创建的默认桌面应用组（名为"桌面应用程序组"）。 有关说明，请参阅[教程：管理 Windows 虚拟桌面的应用组](/azure/virtual-desktop/manage-app-groups)。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在虚拟机中安装受支持的图形驱动程序

要利用 Windows 虚拟桌面中的 Azure N 系列 VM 的 GPU 功能，必须安装相应的图形驱动程序。 按照[支持操作系统和驱动程序](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers)的说明，手动或使用 Azure VM 扩展安装来自相应图形供应商的驱动程序。

只有 Azure 分发的驱动程序支持 Windows 虚拟桌面。 附加功能，对于具有 NVIDIA GPU 的 Azure VM，Windows 虚拟桌面仅支持[NVIDIA 网格驱动程序](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)。

驱动程序安装后，需要重新启动 VM。 使用上述说明中的验证步骤确认已成功安装图形驱动程序。

## <a name="configure-gpu-accelerated-app-rendering"></a>配置 GPU 加速应用渲染

默认情况下，以多会话配置运行的应用和桌面使用 CPU 呈现，并且不利用可用的 GPU 进行呈现。 为会话主机配置组策略以启用 GPU 加速呈现：

1. 使用具有本地管理员权限的帐户连接到 VM 的桌面。
2. 打开"开始"菜单并键入"gpedit.msc"以打开组策略编辑器。
3. 将树导航到**计算机配置** > **管理模板** > **Windows 组件** > **远程桌面服务** > **远程桌面会话主机** > **远程会话环境**。
4. 选择策略**使用所有远程桌面服务会话的硬件默认图形适配器**，并将此策略设置为 **"已启用"，** 以便在远程会话中启用 GPU 呈现。

## <a name="configure-gpu-accelerated-frame-encoding"></a>配置 GPU 加速帧编码

远程桌面对应用和桌面呈现的所有图形（无论是使用 GPU 渲染还是 CPU）进行编码，以便传输到远程桌面客户端。 默认情况下，远程桌面不会利用可用于此编码的可用 GPU。 为会话主机配置组策略以启用 GPU 加速帧编码。 继续上述步骤：

1. 为**远程桌面连接选择"H.264/AVC 444 图形模式"策略优先级**，并将此策略设置为**启用**以强制远程会话中的 H.264/AVC 444 编解码器。
2. 选择策略**为远程桌面连接配置 H.264/AVC 硬件编码**，并将此策略设置为 **"已启用"，** 以便在远程会话中启用 AVC/H.264 的硬件编码。

    >[!NOTE]
    >在 Windows 服务器 2016 中，设置选项 **"首选 AVC 硬件编码**"以**始终尝试**。

3. 现在，组策略已过编辑，请强制进行组策略更新。 打开命令提示符并键入：

    ```batch
    gpupdate.exe /force
    ```

4. 从远程桌面会话注销。

## <a name="verify-gpu-accelerated-app-rendering"></a>验证 GPU 加速应用渲染

要验证应用是否使用 GPU 进行渲染，请尝试以下任一操作：

* 对于具有 NVIDIA GPU 的 Azure `nvidia-smi` VM，请使用["验证驱动程序安装](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)"中所述的实用程序，以检查运行应用时的 GPU 利用率。
* 在支持的操作系统版本上，可以使用任务管理器检查 GPU 利用率。 在"性能"选项卡中选择 GPU 以查看应用是否正在使用 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>验证 GPU 加速帧编码

要验证远程桌面是否使用 GPU 加速编码，

1. 使用 Windows 虚拟桌面客户端连接到 VM 的桌面。
2. 启动事件查看器并导航到以下节点：**应用程序和服务日志** > **微软** > **Windows** > **远程桌面服务-RdpCoreCDV** > **操作**
3. 要确定是否使用 GPU 加速编码，请查找事件 ID 170。 如果您看到"AVC 硬件编码器已启用：1"，则使用 GPU 编码。
4. 要确定是否使用 AVC 444 模式，请查找事件 ID 162。 如果您看到"AVC 可用：1 初始配置文件：2048"，则使用 AVC 444。

## <a name="next-steps"></a>后续步骤

这些说明应该让您在单个会话主机 VM 上使用 GPU 加速启动和运行。 跨较大主机池启用 GPU 加速的一些其他注意事项：

* 请考虑使用[VM 扩展](/azure/virtual-machines/extensions/overview)来简化跨多个 VM 的驱动程序安装和更新。 使用[NVIDIA GPU 驱动程序扩展](/azure/virtual-machines/extensions/hpccompute-gpu-windows)功能，用于具有 NVIDIA GPU 的 VM，并使用 AMD GPU 驱动程序扩展（即将推出）用于具有 AMD GPU 的 VM。
* 请考虑使用 Active 目录组策略来简化多个 VM 的组策略配置。 有关在活动目录域中部署组策略的信息，请参阅[使用组策略对象](https://go.microsoft.com/fwlink/p/?LinkId=620889)。
