---
title: "适用于虚拟机的加速网络 - 门户 | Microsoft 文档"
description: "了解如何使用 Azure 门户为 Azure 虚拟机配置加速网络。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: af4515c6-4377-4d4a-a104-18fe1348252c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 90dc3b45f470bfac03c34c1a20a7b54b9acbc281
ms.lasthandoff: 03/31/2017


---
# <a name="accelerated-networking-for-a-virtual-machine-using-the-azure-portal"></a>使用 Azure 门户为虚拟机配置加速网络
> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-accelerated-networking-portal.md)
> * [PowerShell](virtual-network-accelerated-networking-powershell.md)
> 
> 

使用加速网络可以实现对虚拟机 (VM) 的单根 I/O 虚拟化 (SR-IOV)，大幅提升其网络性能。 这种高性能路径会绕过数据路径中的主机，降低延迟、抖动，以及受支持 VM 类型上的最苛刻网络工作负荷的 CPU 利用率。 本文介绍如何使用 Azure 门户在 Azure Resource Manager 部署模型中配置加速网络。 也可以使用 Azure PowerShell 创建具有加速网络的 VM。 若要了解如何操作，请单击本文顶部的“PowerShell”框。

下图显示了具有和没有加速网络的两个虚拟机 (VM) 之间的通信：

![比较](./media/virtual-network-accelerated-networking-portal/image1.png)

在不使用加速网络的情况下，传入和传出 VM 的所有网络流量必须遍历主机和虚拟交换机。 虚拟交换机针对网络流量实施所有策略，例如网络安全组、访问控制列表、隔离和其他网络虚拟化服务。 若要了解详细信息，请阅读 [Hyper-V 网络虚拟化和虚拟交换机](https://technet.microsoft.com/library/jj945275.aspx)一文。

在使用加速网络的情况下，网络流量将抵达网卡 (NIC)，然后转发到 VM。 在不使用加速网络的情况下由虚拟交换机应用的所有网络策略将会卸载，并在硬件中应用。 由于在硬件中应用策略，NIC 可以绕过主机和虚拟交换机将网络流量直接转发到 VM，同时保留它在主机中应用的所有策略。

加速网络的优势仅适用于已启用该功能的 VM。 为获得最佳效果，最好是在连接到同一个 VNet 的最少两个 VM 上启用此功能。 跨 VNet 通信或者在本地连接时，此功能对总体延迟的影响极小。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="benefits"></a>优点
* **更低的延迟/更高的每秒数据包数 (pps)：**从数据路径中去除虚拟交换机可以消除数据包在主机中进行策略处理所花费的时间，同时增大了 VM 中可处理的数据包数。
* **减少抖动：**虚拟交换机处理取决于需要应用的策略数量，以及正在执行处理的 CPU 工作负荷。 将策略实施卸载到硬件消除了这种可变性，因为可以将数据包直接传送到 VM，省去了主机与 VM 之间的通信，以及所有的软件中断和上下文切换。
* **降低了 CPU 利用率：**绕过主机中的虚拟交换机可以减少用于处理网络流量的 CPU 资源。

## <a name="limitations"></a>限制
此功能存在以下限制：

* **网络接口创建：**只能为新网络接口启用加速网络。  无法在现有网络接口上启用它。
* **VM 创建：**已启用加速网络的网络接口只能在创建 VM 时附加到该 VM。 该网络接口无法附加到现有 VM。
* **区域：**仅在美国中西部以及西欧 Azure 区域提供。 推出的区域今后会不断增加。
* **支持的操作系统：**Microsoft Windows Server 2012 R2 和 Windows Server 2016 Technical Preview 5。 不久后即会增加对 Linux 和 Windows Server 2012 的支持。
* **VM 大小：**Standard_D15_v2 和 Standard_DS15_v2 是唯一受支持的 VM 实例大小。 有关详细信息，请参阅 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文。 支持的 VM 实例大小组合今后会不断增加。

当这些限制有变化时，我们会通过 [Azure 虚拟网络更新](https://azure.microsoft.com/updates/accelerated-networking-in-preview)页发出通告。

## <a name="create-a-windows-vm-with-accelerated-networking"></a>创建具有加速网络的 Windows VM
1. 向[加速网络订阅](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)发送一封包含订阅 ID 和目标用途的电子邮件，以便注册预览版。 只有在收到一封告知允许你使用预览版的电子邮件之后，才完成剩余的步骤。
2. 通过 http://portal.azure.com 登录到 Azure 门户。
3. 完成[创建 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文中的步骤，并选择以下选项来创建一个 VM：
   
   * 选择本文“限制”部分中所列的操作系统。
   * 选择本文“限制”部分中所列的位置（区域）。
   * 选择本文“限制”部分中所列的 VM 大小。 如果其中一个支持的大小未列出，请在“**选择大小**”边栏选项卡中单击“**查看全部**”来从展开的列表中选择一个大小。
   * 在“**设置**”边栏选项卡中对“**加速网络**”单击“*启用*”，如下图所示：
     
       ![设置](./media/virtual-network-accelerated-networking-portal/image3.png)
     
     > [!NOTE]
     > 仅当符合以下条件时，才显示“加速网络”选项：
     > 
     > * 已允许你使用预览版
     > * 选择了本文“限制”部分中所述的受支持操作系统、位置和 VM 大小。
     > 
     > 
4. 创建 VM 后，请下载[加速网络驱动程序](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84)，连接并登录到该 VM，然后在其中运行驱动程序安装程序。
5. 右键单击 Windows 按钮，然后单击“**设备管理器**”。 检查“**Mellanox ConnectX-3 Virtual Function Ethernet Adapter**”是否显示在展开的“**网络**”选项下面，如下图所示：
   
    ![设备管理器](./media/virtual-network-accelerated-networking-portal/image2.png)


