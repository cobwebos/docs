---
title: " 在 Azure Site Recovery 中管理 VMware vCenter 服务器 | Microsoft Docs"
description: "本文介绍如何在 Azure Site Recovery 中添加和管理 VMware vCenter。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 12/04/2017
ms.author: anoopkv
ms.openlocfilehash: 2dcca54c9e1e9330bf7c2a37dc08f67817b6af47
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>在 Azure Site Recovery 中管理 VMware vCenter 服务器
本文介绍可以在 VMware vCenter 上执行的各种 Site Recovery 操作。

## <a name="prerequisites"></a>先决条件

**支持 VMware vCenter 和 VMware vSphere ESX 主机** | **详细信息** |
|--- | --- |
|**本地 VMware 服务器** | 一台或多台 VMware vSphere 服务器，运行带有最新更新的 6.5、6.0 或 5.5。 服务器应位于配置服务器（或单独的进程服务器）所在的同一网络。<br/><br/> 建议使用一台 vCenter 服务器（运行带有最新更新的 6.5、6.0 或 5.5）来管理主机。 部署版本 6.0 时，只有 5.5 版中提供的功能才受支持。|

## <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户
Site Recovery 需要 VMware 的访问权限，以便进程服务器可以自动发现虚拟机，以及实现虚拟机的故障转移和故障回复。

* **迁移**：如果只想要将 VMware 虚拟机迁移到 Azure，而不对其进行故障回复，可以使用具有只读角色的 VMware 帐户。 这样的角色可以运行故障转移，但无法关闭受保护的源计算机。 这不是执行迁移所必需的。
* **复制/恢复**：如果要部署完全复制（复制、故障转移和故障回复），该帐户必须能够运行创建和删除磁盘，虚拟机开机等操作。
* **自动发现**：至少需要一个只读帐户。


|**任务** | **所需的帐户/角色** | **权限** | **详细信息**|
|--- | --- | --- | ---|
|**进程服务器自动发现 VMware 虚拟机** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|
|**故障转移** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。<br/><br/> 用于迁移，但不用于完全复制、故障转移和故障回复。|
|**故障转移和故障回复** | 建议创建一个拥有所需权限的角色 (AzureSiteRecoveryRole)，然后将它分配到 VMware 用户或组 | 数据中心对象 –> 传播到子对象、角色=AzureSiteRecoveryRole<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>创建帐户以连接到 VMware vCenter 服务器/VMware vSphere EXSi 主机
1. 使用桌面上的快捷方式登录到配置服务器并启动 cspsconfigtool.exe 。
2. 在“管理帐户”选项卡中，单击“添加帐户”。

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 提供帐户详细信息并单击“确定”以添加该帐户。 该帐户应具有[为自动发现准备帐户](#prepare-an-account-for-automatic-discovery)部分中列出的特权。

  >[!NOTE]
  大约需要 15 分钟才能将帐户信息同步到 Site Recovery 服务。


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>关联 VMware vCenter/VMware vSphere ESX 主机（添加 vCenter）
* 在 Azure 门户中，浏览到“YourRecoveryServicesVault” > “Site Recovery 基础结构” > “配置服务器” > “ConfigurationServer”
* 在配置服务器的详细信息页中，单击“+vCenter”按钮。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>修改用于连接到 vCenter 服务器/vSphere ESXi 主机的凭据

1. 登录配置服务器并启动 cspsconfigtool.exe。
2. 在“管理帐户”选项卡中，单击“添加帐户”。

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 提供新帐户的详细信息并单击“确定”以添加该帐户。 该帐户应具有[为自动发现准备帐户](#prepare-an-account-for-automatic-discovery)部分中列出的特权。
4. 在 Azure 门户中，浏览到“YourRecoveryServicesVault” > “Site Recovery 基础结构” > “配置服务器” > “ConfigurationServer”
5. 在配置服务器的详细信息页中单击“刷新服务器”按钮。
6. 刷新服务器作业完成后，选择“vCenter 服务器”以打开“vCenter 摘要”页面。
7. 在“vCenter 服务器/vSphere 主机帐户”字段选择新添加的帐户，并单击“保存”按钮。

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>在 Azure Site Recovery 中删除 vCenter
1. 在 Azure 门户中，浏览到“YourRecoveryServicesVault” > “Site Recovery 基础结构” > “配置服务器” > “ConfigurationServer”
2. 在配置服务器的详细信息页，选择“vCenter 服务器”以打开“vCenter 摘要”页面。
3. 单击“删除”按钮，删除 vCenter

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
如果需要修改 vCenter IP 地址/FQDN、端口详细信息，则需删除再重新添加 vCenter 服务器。
