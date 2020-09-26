---
title: 为 FCI 准备虚拟机
description: 准备 Azure 虚拟机，使其与 Azure 虚拟机上 (FCI) 和 SQL Server 的故障转移群集实例一起使用。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: f42d6c8015061406958bdc16473dc0f042d3143a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272491"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>为 Azure Vm 上的 FCI (SQL Server 准备虚拟机) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何 (Vm 准备 Azure 虚拟机，) 将其与 SQL Server 故障转移群集实例一起使用 (FCI) 。 根据 FCI 存储解决方案的不同，配置设置会有所不同，因此请验证是否选择了正确的配置以满足你的环境和业务的需要。 

若要了解详细信息，请参阅 [有关 Azure vm 的 SQL Server FCI](failover-cluster-instance-overview.md) 和 [群集最佳实践](hadr-cluster-best-practices.md)的概述。 

## <a name="prerequisites"></a>先决条件 

- 一个 Microsoft Azure 订阅。 [免费试用](https://azure.microsoft.com/free/)。 
- Azure 虚拟机上的 Windows 域或使用虚拟网络配对扩展到 Azure 的本地数据中心。
- 有权在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- 能够为以下组件提供足够 IP 地址空间的 Azure 虚拟网络和子网：
   - 两台虚拟机
   - Windows 故障转移群集的 IP 地址
   - 每个 FCI 的 IP 地址
- 在 Azure 网络上配置的、指向域控制器的 DNS。

## <a name="choose-an-fci-storage-option"></a>选择 FCI 存储选项

虚拟机的配置设置取决于计划用于 SQL Server 故障转移群集实例的存储选项。 准备虚拟机之前，请查看可用的 [FCI 存储选项](failover-cluster-instance-overview.md#storage) ，并选择最适合你的环境和业务需求的选项。 然后，根据存储选择，在本文中仔细选择适当的 VM 配置选项。 

## <a name="configure-vm-availability"></a>配置 VM 可用性 

故障转移群集功能要求将虚拟机放置在 [可用性集](../../../virtual-machines/linux/tutorial-availability-sets.md) 或 [可用性区域](../../../availability-zones/az-overview.md#availability-zones)中。 如果选择 "可用性集"，则可以使用 [邻近的放置组](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) 来更近地定位 vm。 事实上，近程放置组是使用 Azure 共享磁盘的先决条件。 

请仔细选择与预期群集配置匹配的 VM 可用性选项： 

 - **Azure 共享磁盘**：使用容错域和更新域设置为1并置于[邻近位置组](../../../virtual-machines/windows/proximity-placement-groups-portal.md)中的[可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。
 - **高级文件共享**： [可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 或 [可用性区域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。 如果选择 "可用性区域" 作为 Vm 的可用性配置，则高级文件共享是唯一的共享存储选项。 
 - **存储空间直通**： [可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。

>[!IMPORTANT]
>创建虚拟机后无法设置或更改可用性集。

## <a name="create-the-virtual-machines"></a>创建虚拟机

配置 VM 可用性后，便可以创建虚拟机了。 你可以选择使用未安装 SQL Server 的 Azure Marketplace 映像。 但是，如果在 Azure Vm 上为 SQL Server 选择映像，则需要先从虚拟机中卸载 SQL Server，然后才能配置故障转移群集实例。 


将两个虚拟机放在以下位置：

- 在可用性集所在的同一个 Azure 资源组中，如果要使用可用性集。
- 在与域控制器相同的虚拟网络上。
- 能够为两个虚拟机提供足够 IP 地址空间的子网中，以及最终可能要在群集上使用的所有 FCI 中。
- 在 Azure 可用性集或可用性区域中。

可以通过使用 [包含](sql-vm-create-portal-quickstart.md) 或 [未](../../../virtual-machines/windows/quick-create-portal.md) 预装 SQL Server 的映像创建 Azure 虚拟机。 如果选择 SQL Server 映像，则需要在安装故障转移群集实例之前手动卸载 SQL Server 实例。 


## <a name="uninstall-sql-server"></a>卸载 SQL Server

作为 FCI 创建过程的一部分，将 SQL Server 作为群集实例安装到故障转移群集。 *如果在没有 SQL Server 的情况下部署了包含 Azure Marketplace 映像的虚拟机，则可以跳过此步骤。* 如果使用预安装的 SQL Server 部署映像，则需要从 SQL VM 资源提供程序中注销 SQL Server VM，然后卸载 SQL Server。 

### <a name="unregister-from-the-sql-vm-resource-provider"></a>从 SQL VM 资源提供程序中注销

Azure Marketplace 中的 SQL Server VM 映像将自动注册到 SQL VM 资源提供程序。 在卸载预安装的 SQL Server 实例之前，必须先 [从 SQL VM 资源提供程序中注销每个 SQL Server VM](sql-vm-resource-provider-register.md#unregister-from-rp)。 

### <a name="uninstall-sql-server"></a>卸载 SQL Server

从资源提供程序中注销后，可以卸载 SQL Server。 在每个虚拟机上执行以下步骤： 

1. 使用 RDP 连接到虚拟机。

   首次使用 RDP 连接到虚拟机时，系统会提示是否允许在网络上发现该电脑。 请选择“是”。

1. 如果使用的是基于 SQL Server 的虚拟机映像之一，请删除 SQL Server 实例：

   1. 在“程序和功能”中，右键单击“Microsoft SQL Server 201_ (64 位)”，然后选择“卸载/更改”  。
   1. 选择“删除”。
   1. 选择默认实例。
   1. 删除“数据库引擎服务”下的所有功能。 请勿在 " **共享功能**" 下删除任何内容。 此时会显示以下屏幕截图中所示的内容：

      ![选择特征](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. 依次选择“下一步”、“删除”。 
   1. 成功删除实例后，请重新启动虚拟机。 

## <a name="open-the-firewall"></a>打开防火墙 

在每个虚拟机上，打开 SQL Server 使用的 Windows 防火墙 TCP 端口。 默认情况下，此端口为1433。 但你可以在 Azure VM 部署上更改 SQL Server 端口，因此请打开 SQL Server 在你的环境中使用的端口。 此端口会在从 Azure Marketplace 部署 SQL Server 映像时自动打开。 

如果使用 [负载均衡器](hadr-vnn-azure-load-balancer-configure.md)，还需要打开运行状况探测使用的端口。 默认情况下，此端口为59999。 但可以是创建负载均衡器时指定的任何 TCP 端口。 

此表详细说明了可能需要打开的端口，具体取决于你的 FCI 配置： 

   | 目的 | 端口 | 说明
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server 的默认实例正常使用的端口。 如果使用了库中的某个映像，此端口会自动打开。 </br> </br> **使用者**：所有 FCI 配置。 |
   | 运行状况探测 | TCP 59999 | 任何打开的 TCP 端口。 将负载均衡器 [运行状况探测](hadr-vnn-azure-load-balancer-configure.md#configure-health-probe) 和群集配置为使用此端口。 </br> </br> **使用者**： FCI 和负载均衡器。 |
   | 文件共享 | UDP 445 | 文件共享服务使用的端口。 </br> </br> **使用者**：包含高级文件共享的 FCI。 |

## <a name="join-the-domain"></a>加入域

还需要将虚拟机加入到域。 可以通过使用 [快速入门模板](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)来执行此操作。 

## <a name="review-storage-configuration"></a>查看存储配置

从 Azure Marketplace 创建的虚拟机随附附加存储。 如果计划使用高级文件共享或 Azure 共享磁盘来配置 FCI 存储，则可以删除附加的存储以节省成本，因为本地存储不用于故障转移群集实例。 但是，可以使用连接的存储进行存储空间直通 FCI 解决方案，因此在这种情况下删除它们可能是无用。 查看 FCI 存储解决方案，确定删除附加的存储是否适合节省成本。 


## <a name="next-steps"></a>后续步骤

准备好虚拟机环境后，即可配置故障转移群集实例。 

选择以下指南之一来配置适用于你的业务的 FCI 环境： 
- [配置 FCI 与 Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [使用高级文件共享配置 FCI](failover-cluster-instance-premium-file-share-manually-configure.md)
- [配置 FCI 与存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

若要了解详细信息，请参阅 [Azure vm 上的 SQL Server](failover-cluster-instance-overview.md) 和 [支持的 HADR 配置](hadr-cluster-best-practices.md)的 FCI 概述。 

有关其他信息，请参阅： 
- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
