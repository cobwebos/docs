---
title: 在 Azure Site Recovery 中管理 VMware vCenter 服务器
description: 本文介绍如何使用 Azure Site Recovery 添加和管理 VMware vCenter，以便将 VMware VM 灾难恢复到 Azure。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083978"
---
# <a name="manage-vmware-vcenter-server"></a>管理 VMware vCenter 服务器

本文介绍可以在 VMware vCenter 上执行的各种 Site Recovery 操作。 请在开始之前验证是否满足[先决条件](vmware-physical-azure-support-matrix.md#replicated-machines)。


## <a name="set-up-an-account-for-automatic-discovery"></a>设置用于自动发现的帐户

Site Recovery 需要 VMware 的访问权限，以便进程服务器可以自动发现虚拟机，以及实现虚拟机的故障转移和故障回复。 创建访问帐户，如下所示：

1. 登录配置服务器计算机。
2. 使用桌面快捷方式启动 cspsconfigtool.exe。
3. 在“管理帐户”选项卡中，单击“添加帐户”。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. 提供帐户详细信息并单击“确定”以添加该帐户。  该帐户应具备下表中概述的权限。 

大约需要 15 分钟才能将帐户信息同步到 Site Recovery 服务。

### <a name="account-permissions"></a>帐户权限

|**任务** | **帐户** | **权限** | **详细信息**|
|--- | --- | --- | ---|
|**自动发现/迁移（无故障回复）** | 至少需要一个只读用户 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|
|**复制/故障转移** | 至少需要一个只读用户| 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。<br/><br/> 用于迁移，但不用于完全复制、故障转移和故障回复。|
|**复制/故障转移/故障回复** | 建议创建一个拥有所需权限的角色 (AzureSiteRecoveryRole)，然后将它分配到 VMware 用户或组 | 数据中心对象 –> 传播到子对象、角色=AzureSiteRecoveryRole<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|


## <a name="add-vmware-server-to-the-vault"></a>将 VMware 服务器添加到保管库

1. 在 Azure 门户中，打开你的保管库>“Site Recovery 基础结构” **“配置服务器”，然后打开配置服务器** > 。
2. 在“详细信息”页上单击“+vCenter”。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>修改凭据

修改用于连接到 vCenter 服务器或 ESXi 主机的凭据，如下所示：

1. 登录到配置服务器并从桌面启动 cspsconfigtool.exe。
2. 在“管理帐户”选项卡中，单击“添加帐户”。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 提供新帐户的详细信息并单击“确定”以添加该帐户。 该帐户应具备[上方](#account-permissions)列出的权限。
4. 在 Azure 门户上，打开保管库>“Site Recovery 基础结构” **“配置服务器”，然后打开配置服务器** > 。
5. 在“详细信息”页上，单击“刷新服务器”。
6. 刷新服务器作业完成后，选择“vCenter 服务器”以打开 vCenter“摘要”页面。
7. 在“vCenter 服务器/vSphere 主机帐户”字段选择新添加的帐户，并单击“保存”。

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>删除 vCenter 服务器

1. 在 Azure 门户中，打开你的保管库>“Site Recovery 基础结构” **“配置服务器”，然后打开配置服务器** > 。
2. 在“详细信息”页上，选择 vCenter 服务器。
3. 单击“删除”按钮。

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>修改 vCenter IP 地址和端口

1. 登录到 Azure 门户。
2. 导航到“恢复服务保管库” **“Site Recovery 基础结构”** “配置服务器” >  > 。
3. 单击 vCenter 分配到的配置服务器。
4. 在“vCenter 服务器”部分，单击要修改的 vCenter。
5. 在 vCenter 摘要页上，更新相应字段中 vCenter 的 IP 地址和端口，然后保存更改。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. 要使更改生效，请等待 15 分钟或[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>将所有受保护的虚拟机迁移到新的 vCenter

若要将所有虚拟机迁移到新的 vCenter，请不要添加另一个 vCenter 帐户。 这可能会导致出现重复条目。 只需更新新 vCenter 的 IP 地址即可：

1. 登录到 Azure 门户。
2. 导航到“恢复服务保管库” **“Site Recovery 基础结构”** “配置服务器” >  > 。
3. 单击旧 vCenter 分配到的配置服务器。
4. 在“vCenter 服务器”部分，单击要从中迁移的 vCenter。
5. 在 vCenter 摘要页上，更新“vCenter 服务器/vSphere 主机名或 IP 地址”字段中新 vCenter 的 IP 地址。 保存所做更改。

更新 IP 地址后，Site Recovery 组件会立即开始从新的 vCenter 接收虚拟机的发现信息。 这不会影响正在进行的复制活动。

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>将少量受保护的虚拟机迁移到新的 vCenter

> [!NOTE]
> 本部分仅适用于将少量受保护的虚拟机迁移到新 vCenter 的情况。 若要保护新 vCenter 中的一组新虚拟机，请[将新 vCenter 的详细信息添加到配置服务器](#add-vmware-server-to-the-vault)，并从 **[启用保护](vmware-azure-tutorial.md#enable-replication)** 开始。

将少量的虚拟机移到新的 vCenter：

1. [将新 vCenter 的详细信息添加到配置服务器](#add-vmware-server-to-the-vault)。
2. [禁用要迁移的虚拟机的复制](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
3. 完成将所选虚拟机迁移到新 vCenter 的过程。
4. 现在，在[启用保护时选择新的 vCenter](vmware-azure-tutorial.md#enable-replication)，以保护迁移的虚拟机。

> [!TIP]
> 如果所要迁移的虚拟机数量**大于**旧 vCenter 中保留的虚拟机数量，请按照此处的说明更新新 vCenter 的 IP 地址。 如果旧 vCenter 中保留了少量的虚拟机，请[禁用复制](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)；[将新 vCenter 的详细信息添加到配置服务器](#add-vmware-server-to-the-vault)，然后开始 **[启用保护](vmware-azure-tutorial.md#enable-replication)** 。

## <a name="frequently-asked-questions"></a>常见问题

1. 将受保护的虚拟机从一个 ESXi 主机移到另一个主机是否会影响复制？

    否，这不会影响正在进行的复制。 但是，[请务必部署具有足够特权的主目标服务器](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. 哪些端口号用于在 vCenter 与其他 Site Recovery 组件之间进行通信？

    默认端口为 443。 配置服务器将通过此端口访问 vCenter/vSphere 主机信息。 若要更新此信息，请单击[此处](#modify-the-vcenter-ip-address-and-port)。
