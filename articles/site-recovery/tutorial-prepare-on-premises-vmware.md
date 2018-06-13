---
title: 准备本地 VMware 服务器用于将 VMware VM 灾难恢复到 Azure| Microsoft 文档
description: 了解如何准备本地 VMware 服务器使用 Azure Site Recovery 服务灾难恢复到 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4fecd5f8ddb4a6f432995a7779e29479b5b1a7c0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677509"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>准备本地 VMware 服务器用于灾难恢复到 Azure

本教程将介绍如何准备本地 VMware 基础架构用于将 VMware VM 复制到 Azure。 在本教程中，将了解如何：

> [!div class="checklist"]
> * 在 vCenter 服务器或 vSphere ESXi 主机上准备一个帐户，以自动化 VM 发现
> * 准备一个帐户用于在 VMware VM 上自动安装移动服务
> * 查看 VMware 服务器要求
> * 查看 VMware VM 要求

在本教程系列中，我们演示如何使用 Azure Site Recovery 备份单个 VM。 如果你计划保护多个 VMware VM，则应下载[部署规划器工具](https://aka.ms/asr-deployment-planner)用于 VMware 复制。 此工具可以收集有关 VM 兼容性、每个 VM 的磁盘以及每个磁盘数据变化的信息。 该工具还介绍了成功复制和测试故障转移所需的网络带宽要求，以及所需的 Azure 基础结构。 [详细了解](site-recovery-deployment-planner.md)如何运行此工具。

这是教程系列中的第二个教程。 请确保你已[设置 Azure 组件](tutorial-prepare-azure.md)，如上一个教程中所述。

## <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 你需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

按如下所述创建此帐户：

1. 若要使用专用帐户，请 vCenter 级别创建一个角色。 为该角色指定一个名称，例如 Azure_Site_Recovery。
2. 为该角色分配下表中汇总的权限。
3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

### <a name="vmware-account-permissions"></a>VMware 帐户权限

**任务** | **角色/权限** | **详细信息**
--- | --- | ---
**VM 发现** | 至少一个只读用户<br/><br/> 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
**完全复制、故障转移、故障回复** |  创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将该角色分配到 VMware 用户或组<br/><br/> 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

## <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的 VM 上安装移动服务。 为 VM 启用复制后，Site Recovery 会自动安装此服务。 为了实现自动安装，你需要准备一个由 Site Recovery 用于访问 VM 的帐户。 在 Azure 控制台中设置灾难恢复时，你将指定此帐户。

1. 准备一个在 VM 上具有安装权限的域或本地帐户。
2. 若要在 Windows VM 上安装，如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。
   - 从注册表中的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下，添加值为 1 的 DWORD 项 LocalAccountTokenFilterPolicy。
3. 若要在 Linux VM 上安装，则需在源 Linux 服务器上准备一个根帐户。


## <a name="check-vmware-server-requirements"></a>检查 VMware 服务器要求

请确保 VMware 服务器满足以下要求。

**组件** | **要求**
--- | ---
**vCenter 服务器** | vCenter 6.5、6.0 或 5.5
**vSphere 主机** | vSphere 6.5、6.0、5.5

## <a name="check-vmware-vm-requirements"></a>检查 VMware VM 要求

请确保 VM 符合下表中总结的 Azure 要求。

**VM 要求** | **详细信息**
--- | ---
**操作系统磁盘大小** | 最大 2048 GB
**操作系统磁盘计数** | 1
**数据磁盘计数** | 64 或更少
**数据磁盘 VHD 大小** | 最大 4095 GB
**网络适配器** | 支持多个适配器
**共享 VHD** | 不支持
**FC 磁盘** | 不支持
**硬盘格式** | VHD 或 VHDX。<br/><br/> 尽管 Azure 当前不支持 VHDX，但故障转移到 Azure 时，站点恢复会自动将 VHDX 转换为 VHD。 故障回复到本地时，VM 将继续使用 VHDX 格式。
**Bitlocker** | 不支持。 为 VM 启用复制之前禁用。
**VM 名称** | 介于 1 和 63 个字符之间。<br/><br/> 限制为字母、数字和连字符。 VM 名称必须以字母或数字开始或结尾。
**VM 类型** | 第 1 代 - Linux 或 Windows<br/><br/>第 2 代 - 仅限 Windows

VM 还必须运行受支持的操作系统。 有关受支持版本的完整列表，请参阅 [Site Recovery 支持矩阵](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

在实施故障转移方案期间，你可能需要通过本地网络连接到 Azure 中的复制 VM。

若要在故障转移后使用 RDP 连接到 Windows VM，请执行以下操作：

1. 若要通过 Internet 访问，请在故障转移之前在本地 VM 上启用 RDP。 请确保已针对“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 若要通过站点到站点 VPN 进行访问，请在本地计算机上启用 RDP。 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。
   检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。
3. 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看这些[故障排除技巧](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

若要在故障转移后使用 SSH 连接到 Linux VM，请执行以下操作：

1. 在本地计算机上故障转移之前，请确保安全外壳服务设置为在系统启动时自动启动。 确保防火墙规则允许 SSH 连接。

2. 在 Azure VM 上执行故障转移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。
   为 VM [添加公共 IP 地址](site-recovery-monitoring-and-troubleshooting.md)。 可选中“启动诊断”查看 VM 的屏幕截图。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [针对 VMware VM 设置到 Azure 的灾难恢复](tutorial-vmware-to-azure.md)
