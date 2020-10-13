---
title: 使用 Azure Site Recovery 准备 Azure VMware 解决方案 VM 灾难恢复
description: 了解如何使用 Azure Site Recovery 服务为到 Azure 的灾难恢复准备 Azure VMware 解决方案服务器。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 9b04faf6797d04404dc0c5d617af2fd62a68c49a
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814187"
---
# <a name="prepare-azure-vmware-solution-servers-for-disaster-recovery-to-azure"></a>为到 Azure 的灾难恢复准备 Azure VMware 解决方案服务器

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务为到 Azure 的灾难恢复准备 Azure VMware 解决方案服务器。 

这是本系列的第二个教程，演示如何为 Azure VMware 解决方案 VM 设置到 Azure 的灾难恢复。 在第一个教程中，我们[设置了 Azure VMware 解决方案灾难恢复所需的 Azure 组件](avs-tutorial-prepare-azure.md)。


在本文中，学习如何：

> [!div class="checklist"]
> * 在 vCenter 服务器或 vSphere ESXi 主机上准备一个帐户，用于自动发现 VM。
> * 准备一个帐户用于在 VMware VM 上自动安装移动服务。
> * 查看 VMware 服务器和 VM 要求与支持。
> * 准备在故障转移后连接到 Azure VM。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 Site Recovery 目录的“操作指南”部分所列的文章。

## <a name="before-you-start"></a>开始之前

请确保按照[本系列中的第一个教程](avs-tutorial-prepare-azure.md)中的说明准备好 Azure。

## <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 Azure VMware 解决方案服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 你需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

按如下所述创建此帐户：

1. 若要使用专用帐户，请 vCenter 级别创建一个角色。 为该角色指定一个名称，例如 Azure_Site_Recovery  。
2. 为该角色分配下表中汇总的权限。
3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

### <a name="vmware-account-permissions"></a>VMware 帐户权限

**任务** | **角色/权限** | **详细信息**
--- | --- | ---
**VM 发现** | 至少一个只读用户<br/><br/> 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络） 。
**完全复制、故障转移、故障回复** |  创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将该角色分配到 VMware 用户或组<br/><br/> 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络） 。

## <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的计算机上安装移动服务。 为计算机启用了复制时，Site Recovery 可以执行此服务的推送安装，你也可以手动安装或使用安装工具安装。

- 在本教程中，我们将使用推送安装来安装移动服务。
- 若要执行此推送安装，需要准备 Site Recovery 可用于访问 VM 的帐户。 在 Azure 控制台中设置灾难恢复时，你将指定此帐户。

按如下所述准备此帐户：

准备一个在 VM 上具有安装权限的域或本地帐户。

- **Windows VM**：若要在 Windows VM 上安装，如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。 为此，请在注册表 > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 中添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
- **Linux VM**：若要在 Linux VM 上安装，则需在源 Linux 服务器上准备一个根帐户。


## <a name="check-vmware-requirements"></a>检查 VMware 要求

确保 VMware 服务器和 VM 符合要求。

1. 验证 Azure VMware 解决方案[软件版本](../azure-vmware/concepts-private-clouds-clusters.md#vmware-software-versions)。
2. 验证 [VMware 服务器要求](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers)。
3. 对于 Linux VM，请[检查](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)文件系统和存储要求。 
4. 检查[网络](vmware-physical-azure-support-matrix.md#network)和[存储](vmware-physical-azure-support-matrix.md#storage)支持。 
5. 故障转移后，检查 [Azure 网络](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](vmware-physical-azure-support-matrix.md#azure-storage)和[计算](vmware-physical-azure-support-matrix.md#azure-compute)支持的功能。
6. 复制到 Azure 的 Azure VMware 解决方案 VM 必须符合 [Azure VM 要求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。
7. 在 Linux 虚拟机中，设备名称或装入点名称都应具有唯一性。 请确保没有两个设备/装入点具有相同名称。 请注意，名称不区分大小写。 例如，不允许将同一 VM 的两个设备命名为 device1__ 和 Device1__。


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移后，可能需要通过 Azure VMware 解决方案网络连接到 Azure VM。

若要在故障转移后使用 RDP 连接到 Windows VM，请执行以下操作：

- **Internet 访问权限**。 在故障转移之前，在 Azure VMware 解决方案 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP  。
- **站点到站点 VPN 访问权限**：
    - 在故障转移之前，在 Azure VMware 解决方案 VM 上启用 RDP。
    - 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP  。
    - 检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](https://support.microsoft.com/kb/3031135)。
- 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。
- 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”，查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看这些[疑难解答提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

若要在故障转移后使用 SSH 连接到 Linux VM，请执行以下操作：

- 在故障转移之前，请在 Azure VMware 解决方案 VM 上确保安全外壳服务设置为在系统启动时自动启动。
- 确保防火墙规则允许 SSH 连接。
- 在 Azure VM 上执行故障转移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。
- 为 VM [添加公共 IP 地址](./site-recovery-monitor-and-troubleshoot.md)。
- 可选中“启动诊断”查看 VM 的屏幕截图****。


## <a name="failback-requirements"></a>故障回复要求
如果计划故障回复到 Azure VMware 解决方案云，则有多个[故障回复先决条件](avs-tutorial-reprotect.md#before-you-begin)。 现在可以准备这些先决条件，但不需要。 可以在故障转移到 Azure 后准备。




## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [设置灾难恢复](avs-tutorial-replication.md)
- 如果要复制多个 VM，请[执行容量规划](site-recovery-deployment-planner.md)。
