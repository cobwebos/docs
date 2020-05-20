---
title: 使用 Azure Site Recovery 为到 Azure 的 Hyper-V VM 的灾难恢复做准备
description: 了解如何使用 Azure Site Recovery 准备本地 Hyper-V VM 以进行到 Azure 的灾难恢复。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79224024"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>为 Hyper-V VM 到 Azure 的灾难恢复准备本地 Hyper-V 服务器

本文介绍如何在要使用 [Azure Site Recovery](site-recovery-overview.md) 安排 Hyper-VM 到 Azure 的灾难恢复时准备本地 Hyper-V 基础结构。


这是本系列的第二个教程，演示如何安排本地 Hyper-V VM 到 Azure 的灾难恢复。 在第一个教程中，我们[设置了 Hyper-V 灾难恢复所需的 Azure 组件](tutorial-prepare-azure.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 如果 Hyper-V 主机由 System Center VMM 管理，请查看 Hyper-V 要求和 VMM 要求。
> * 准备 VMM（如果适用）。
> * 验证对 Azure 位置的 Internet 访问。
> * 准备 VM，以便可以在故障转移到 Azure 后访问它们。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 Site Recovery 目录的“操作指南”部分所列的文章。

## <a name="before-you-start"></a>开始之前

请确保按照[本系列中的第一个教程](tutorial-prepare-azure.md)中的说明准备好 Azure。

## <a name="review-requirements-and-prerequisites"></a>审查要求和先决条件

确保 Hyper-V 主机和 VM 符合要求。

1. [验证](hyper-v-azure-support-matrix.md#on-premises-servers)本地服务器要求。
2. [检查要复制到 Azure 的 Hyper-V VM 的要求](hyper-v-azure-support-matrix.md#replicated-vms)。
3. 检查 Hyper-V 主机[网络](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)以及主机和来宾[存储](hyper-v-azure-support-matrix.md#hyper-v-host-storage)是否支持本地 Hyper-V 主机。
4. 故障转移后，检查 [Azure 网络](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover)、[存储](hyper-v-azure-support-matrix.md#azure-storage)和[计算](hyper-v-azure-support-matrix.md#azure-compute-features)支持的功能。
5. 复制到 Azure 的本地 VM 必须符合 [Azure VM 要求](hyper-v-azure-support-matrix.md#azure-vm-requirements)。


## <a name="prepare-vmm-optional"></a>准备 VMM（可选）

如果 Hyper-V 主机由 VMM 管理，则需准备本地 VMM 服务器。 

- 请确保 VMM 服务器至少有一个云，包含一个或多个主机组。 运行在虚拟机上的 Hyper-V 主机应位于云中。
- 为网络映射准备 VMM 服务器。

### <a name="prepare-vmm-for-network-mapping"></a>为网络映射准备 VMM

如果使用 VMM，则[网络映射](site-recovery-network-mapping.md)在本地 VMM VM 网络和 Azure 虚拟网络之间进行映射。 映射将确保在故障转移后创建 Azure VM 时，将其连接到正确的网络。

为网络映射准备 VMM，如下所示：

1. 确保有与 Hyper-V 主机所在的云相关联的 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 确保有链接到逻辑网络的[虚拟机网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。
3. 在 VMM 中，将 VM 连接到 VM 网络。

## <a name="verify-internet-access"></a>验证 Internet 访问

1. 对于本教程，最简单的配置是让 Hyper-V 主机和 VMM 服务器能够直接访问 Internet 而无需使用代理。 
2. 请确保该 Hyper-V 主机和 VMM 服务器（如果相关）可以访问以下所需 URL。   
3. 如果要通过 IP 地址来控制访问，请确保：
    - 基于 IP 地址的防火墙规则可以连接到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围。
    
### <a name="required-urls"></a>所需 URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

在实施故障转移方案期间，可能需要连接到已复制的本地网络。

若要在故障转移后使用 RDP 连接到 Windows VM，请按如下所示允许访问：

1. 若要通过 Internet 访问，请在故障转移之前在本地 VM 上启用 RDP。 请确保已针对“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 若要通过站点到站点 VPN 进行访问，请在本地计算机上启用 RDP。 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。
   检查操作系统的 SAN 策略是否已设置为 OnlineAll  。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。
3. 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”，查看 VM 的屏幕截图  。 如果无法连接，请检查 VM 是否正在运行，并查看这些[疑难解答提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

故障转移后，可以使用与复制的本地 VM 相同的 IP 地址或不同的 IP 地址访问 Azure VM。 [详细了解](concepts-on-premises-to-azure-networking.md)如何为故障转移设置 IP 寻址。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 Hyper-V VM 设置到 Azure 的灾难恢复](tutorial-hyper-v-to-azure.md)
> [为 VMM 云中的 Hyper-V VM 设置到 Azure 的灾难恢复](tutorial-hyper-v-vmm-to-azure.md)
