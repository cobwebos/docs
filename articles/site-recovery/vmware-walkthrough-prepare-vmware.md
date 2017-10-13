---
title: "准备使用 Azure Site Recovery 将本地 VMware 资源复制到 Azure | Microsoft Docs"
description: "总结了将 VMware VM 上运行的工作负荷复制到 Azure 存储所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>步骤 6：准备将本地 VMware 复制到 Azure

使用本文中的说明准备本地 VMware 服务器，以与 Azure Site Recovery 进行交互，并准备 VMWare VM 用于安装 Mobility Service。 必须在要复制到 Azure 的所有本地 VM 上安装 Mobility Service 代理。

## <a name="prepare-for-automatic-discovery"></a>准备自动发现

Site Recovery 可自动发现在 vSphere ESXi 主机（有或没有 vCenter 服务器）上运行的 VM。 Site Recovery 需要一个访问主机和服务器的帐户才能够实现自动发现：

1. 若要使用专用帐户，请创建一个角色（处于 vCenter 级别，具有下表所述的权限）。 为其指定一个名称，例如 **Azure_Site_Recovery**。
2. 然后在 vSphere 主机或 vCenter 服务器上创建一个用户，并向用户分配该角色。 在 Site Recovery 部署过程中指定此用户帐户。


### <a name="vmware-account-permissions"></a>VMware 帐户权限

Site Recovery 需要 VMware 的访问权限，以便进程服务器可以自动发现 VM，以及实现 VM 的故障转移和故障回复。

- **迁移**：如果只想要将 VMware VM 迁移到 Azure，而不对其进行故障回复，可以使用具有只读角色的 VMware 帐户。 这样的角色可以运行故障转移，但无法关闭受保护的源计算机。 这不是执行迁移所必需的。
- **复制/恢复**：如果要部署完全复制（复制、故障转移和故障回复），该帐户必须能够运行创建和删除磁盘，VM 开机等操作。
- **自动发现**：至少需要一个只读帐户。


**任务** | **所需的帐户/角色** | **权限** | **详细信息**
--- | --- | --- | ---
**进程服务器自动发现 VMware VM** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
**故障转移** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。<br/><br/> 用于迁移，但不用于完全复制、故障转移和故障回复。
**故障转移和故障回复** | 建议创建一个拥有所需权限的角色 (Azure_Site_Recovery)，并将它分配到 VMware 用户或组。 | 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>准备 Mobility Service 的推送安装

必须在要复制的所有 VM 上安装 Mobility Service。 安装该服务的方法有数种，包括手动安装、从 Site Recovery 进程服务器推送安装，以及使用 System Center Configuration Manager 等方法进行安装。

如果要使用推送安装，需要准备一个由 Site Recovery 用于访问 VM 的帐户。

- 可以使用域或本地帐户
- 对于 Windows，如果使用的不是域帐户，则需在本地计算机上禁用远程用户访问控制。 为此，请在注册表中的 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** 下添加值为 1 的 DWORD 项 **LocalAccountTokenFilterPolicy**。
- 如果希望从 CLI 为 Windows 添加注册表项，请键入：       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- 对于 Linux，该帐户应是源 Linux 服务器上的根。



## <a name="next-steps"></a>后续步骤

转到[步骤 7：创建保管库](vmware-walkthrough-create-vault.md)
