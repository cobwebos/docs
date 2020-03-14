---
title: 在 Azure Site Recovery 中管理 VMware vCenter 服务器
description: 本文介绍如何在 Azure Site Recovery 中添加和管理 vmware vCenter，以便将 VMware Vm 灾难恢复到 Azure。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257259"
---
# <a name="manage-vmware-vcenter-server"></a>管理 VMware vCenter 服务器

本文汇总了[Azure Site Recovery](site-recovery-overview.md)中 VMware vCenter 服务器的管理操作。

## <a name="verify-prerequisites-for-vcenter-server"></a>验证 vCenter Server 的先决条件

在将 VMware Vm 灾难恢复到 Azure 的过程中，[在将 VMware](vmware-physical-azure-support-matrix.md#replicated-machines)vm 灾难恢复到 Azure 的先决条件中列出了这些先决条件。

## <a name="set-up-an-account-for-automatic-discovery"></a>设置用于自动发现的帐户

为本地 VMware Vm 设置灾难恢复时，Site Recovery 需要对 vCenter Server/vSphere 主机的访问权限。 然后，Site Recovery 进程服务器可以自动发现 Vm，并根据需要对其进行故障转移。 默认情况下，进程服务器在 Site Recovery 配置服务器上运行。 添加配置服务器的帐户以连接到 vCenter Server/vSphere 主机，如下所示：

1. 登录到配置服务器。
1. 使用桌面快捷方式打开配置服务器工具（_cspsconfigtool.exe_）。
1. 在 "**管理帐户**" 选项卡上，单击 "**添加帐户**"。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供帐户详细信息并单击“确定”以添加该帐户。 该帐户应具有帐户权限表中汇总的权限。

   > [!NOTE]
   > 与 Site Recovery 同步帐户信息需要大约15分钟。

### <a name="account-permissions"></a>帐户权限

|**任务** | **帐户** | **权限** | **详细信息**|
|--- | --- | --- | ---|
|**VM 发现/迁移（无故障回复）** | 至少一个只读用户帐户。 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|
|**复制/故障转移** | 至少一个只读用户帐户。 | 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。<br/><br/> 用于迁移，但不用于完全复制、故障转移和故障回复。|
|**复制/故障转移/故障回复** | 建议创建一个具有所需权限的角色（AzureSiteRecoveryRole），然后将该角色分配给 VMware 用户或组。 | 数据中心对象 –> 传播到子对象、角色=AzureSiteRecoveryRole<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、虚拟机和网络）。|

## <a name="add-vmware-server-to-the-vault"></a>将 VMware 服务器添加到保管库

为本地 VMware Vm 设置灾难恢复时，将在其上发现 Vm 的 vCenter Server/vSphere 主机添加到 Site Recovery 保管库，如下所示：

1. 在保管库 > **Site Recovery 基础结构** > **配置**服务器中，打开配置服务器。
1. 在**详细信息**页中，单击 " **vCenter**"。
1. 在 "**添加 vCenter**" 中，指定 vSphere 主机或 vCenter 服务器的友好名称。
1. 指定服务器的 IP 地址或 FQDN。
1. 除非已将 VMware 服务器配置为在不同的端口上侦听请求，否则请保留 443 作为端口号。
1. 选择用于连接到 VMware vCenter 或 vSphere ESXi 服务器的帐户。 然后单击“确定”。

## <a name="modify-credentials"></a>修改凭据

如有必要，可以修改用于连接到 vCenter Server/vSphere 主机的凭据，如下所示：

1. 登录到配置服务器。
1. 使用桌面快捷方式打开配置服务器工具（_cspsconfigtool.exe_）。
1. 在“管理帐户”选项卡中，单击“添加帐户”。

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 提供新帐户的详细信息，然后单击 **"确定"** 。 帐户需要[帐户权限](#account-permissions)表中列出的权限。
1. 在保管库 > **Site Recovery 基础结构** > **配置**服务器上，打开配置服务器。
1. 在 "**详细信息**" 中，单击 "**刷新服务器**"。
1. 刷新服务器作业完成后，选择 "vCenter Server"。
1. 在 "**摘要**" 中，选择 " **VCenter server/vSphere 主机帐户**" 中新添加的帐户，然后单击 "**保存**"。

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>删除 vCenter Server

1. 在保管库 > **Site Recovery 基础结构** > **配置**服务器上，打开配置服务器。
1. 在“详细信息”页上，选择 vCenter 服务器。
1. 单击“删除”按钮。

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>修改 IP 地址和端口

你可以修改 vCenter Server 的 IP 地址，或用于在服务器和 Site Recovery 之间进行通信的端口。 默认情况下，Site Recovery 通过端口443访问 vCenter Server/vSphere 主机信息。

1. 在保管库 > **Site Recovery 基础结构** > **配置服务器**上，单击 vCenter Server 添加到的配置服务器。
1. 在 " **vCenter 服务器**" 中，单击要修改的 vCenter Server。
1. 在 "**摘要**" 中，更新 IP 地址和端口，并保存所做的更改。

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 要使更改生效，请等待15分钟或[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="migrate-all-vms-to-a-new-server"></a>将所有 Vm 迁移到新服务器

如果要迁移所有 Vm 以使用新 vCenter Server，只需更新分配给 vCenter Server 的 IP 地址。 不要添加另一个 VMware 帐户，因为这可能会导致重复项。 按如下所示更新地址：

1. 在保管库 > **Site Recovery 基础结构** > **配置服务器**上，单击 vCenter Server 添加到的配置服务器。
1. 在 " **vCenter 服务器**" 部分中，单击要从中进行迁移的 vCenter Server。
1. 在 "**摘要**" 中，将 IP 地址更新为新 vCenter Server 的 IP 地址，并保存所做的更改。
1. 一旦更新 IP 地址，Site Recovery 就会开始从新 vCenter Server 接收 VM 发现信息。 这不会影响正在进行的复制活动。

## <a name="migrate-a-few-vms-to-a-new-server"></a>将一些 Vm 迁移到新服务器

如果只想要将几个复制 Vm 迁移到新的 vCenter Server，请执行以下操作：

1. [将](#add-vmware-server-to-the-vault)新 vCenter Server 添加到配置服务器。
1. 为将移到新服务器的 Vm[禁用复制](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
1. 在 VMware 中，将 Vm 迁移到新的 vCenter Server。
1. 再次为迁移的 Vm[启用复制](vmware-azure-tutorial.md#enable-replication)，选择新 vCenter Server。

## <a name="migrate-most-vms-to-a-new-server"></a>将大多数 Vm 迁移到新服务器

如果要迁移到新 vCenter Server 的 Vm 数高于将保留在原始 vCenter Server 上的 Vm 数，请执行以下操作：

1. 将配置服务器设置中分配给 vCenter Server 的[IP 地址更新](#modify-the-ip-address-and-port)为新 vCenter Server 的地址。
1. 为旧服务器上保留的几个 Vm[禁用复制](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
1. [将旧的 vCenter Server](#add-vmware-server-to-the-vault)及其 IP 地址添加到配置服务器。
1. 为保留在旧服务器上的 Vm[重新启用复制](vmware-azure-tutorial.md#enable-replication)。

## <a name="next-steps"></a>后续步骤

如果有任何问题，请参阅[排查 vCenter Server 发现失败](vmware-azure-troubleshoot-vcenter-discovery-failures.md)。
