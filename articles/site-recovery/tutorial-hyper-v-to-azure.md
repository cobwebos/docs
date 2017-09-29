---
title: "使用 Azure Site Recovery 对 Azure 本地 Hyper-V VM 设置灾难恢复 | Microsoft 文档"
description: "了解如何使用 Azure Site Recovery 服务设置本地 Hyper-V VM 的灾难恢复。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>对 Azure 设置本地 Hyper-V VM 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务有助于实施灾难恢复策略，因为它可以管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程演示如何对 Azure 设置本地 Hyper-V VM 的灾难恢复 本教程适用于托管和不托管在 System Center Virtual Machine Manager (VMM) 云中的 Hyper-V VM。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Azure 和本地先决条件
> * 为 Site Recovery 创建恢复服务保管库 
> * 设置源和目标复制环境 
> * 设置网络映射（如果 Hyper-V 由 System Center VMM 托管）
> * 创建复制策略
> * 为虚拟机启用复制


## <a name="prerequisites"></a>先决条件

要完成本教程：

- 确保了解[方案体系结构和组件](concepts-hyper-v-to-azure-architecture.md)。
- 查看所有组件的[支持要求](site-recovery-support-matrix-to-azure.md)。
- 确保想要复制的虚拟机符合 [Azure VM 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
- 要规划容量计划：
    - 使用 [Hyper-V 容量计划工具](http://aka.ms/asr-capacity-planner-excel)规划你的更改率。
    - 使用 [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) 分析源环境、估计带宽和目标要求。
- 准备 Azure。 需要 Azure 订阅、Azure 虚拟网络和存储帐户。
- 准备本地 Hyper-V 主机和相关的 VMM 服务器。





### <a name="set-up-an-azure-account"></a>设置 Azure 帐户

获取 Microsoft [Azure 帐户](http://azure.microsoft.com/)。

- 可以从[免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。
- 了解有关 [Site Recovery 定价](site-recovery-faq.md#pricing)的信息，并获取[定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。
- 了解 Site Recovery 所[支持的区域](https://azure.microsoft.com/pricing/details/site-recovery/)。

### <a name="verify-azure-account-permissions"></a>验证 Azure 帐户权限

确保你的 Azure 帐户具有复制虚拟机所需的权限。

- 查看将计算机复制到 Azure 所需的[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)
- 验证和修改[基于角色的访问](../active-directory/role-based-access-control-configure.md)权限。 


### <a name="set-up-an-azure-network"></a>设置 Azure 网络

设置 [Azure 网络](../virtual-network/virtual-network-get-started-vnet-subnet.md)。

- 在故障转移后创建 Azure VM 时，Azure VM 将置于此网络中。
- 该网络应位于与恢复服务保管库相同的区域。


### <a name="set-up-an-azure-storage-account"></a>设置 Azure 存储帐户

设置 [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

- Site Recovery 将本地计算机复制到 Azure 存储。 发生故障转移后，将从存储中创建 Azure VM。
- 存储帐户必须与恢复服务保管库位于相同区域中。
- 存储帐户可以是标准帐户，也可以是[高级](../storage/common/storage-premium-storage.md)帐户。
- 如果创建的是高级帐户，则还需要一个标准帐户来记录数据。

### <a name="prepare-hyper-v-hosts"></a>准备 Hyper-V 主机

1. 验证 Hyper-V 主机是否符合[支持要求](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
2. 确保主机可访问这些 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 任何基于 IP 地址的防火墙规则都应允许与 Azure 通信。
    - 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

### <a name="prepare-vmm-servers"></a>准备 VMM 服务器

如果 Hyper-V 主机由 VMM 管理，则需准备本地 VMM 服务器。 

- 验证 VMM 服务器符合[支持要求](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)。
- 请确保 VMM 服务器至少有一个云，包含一个或多个主机组。 运行在虚拟机上的 Hyper-V 主机应位于云中。
- VMM 服务器需要 Internet 访问权限，并可以访问下面的 URL：

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 任何基于 IP 地址的防火墙规则都应允许与 Azure 通信。
    - 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。
- 准备 VMM 服务器以用于网络映射。


#### <a name="prepare-vmm-for-network-mapping"></a>为网络映射准备 VMM

如果使用 VMM，则[网络映射](site-recovery-network-mapping.md)在本地 VMM VM 网络和 Azure 虚拟网络之间进行映射。 映射将确保在故障转移后创建 Azure VM 时，将其连接到正确的网络。

为网络映射准备 VMM，如下所示：

1. 确保有与 Hyper-V 主机所在的云相关联的 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
2. 确保有链接到逻辑网络的[虚拟机网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。
3. 将虚拟机连接到虚拟机网络。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要将内容复制到的位置。

1. 在保管库中，单击“Site Recovery” > “准备基础结构” > “保护目标”。
2. 在“保护目标”中，选择“到 Azure”> “是，使用 Hyper-V”。 
    - 如果 Hyper-V 主机不是由 VMM 管理，则选择“否”以确认没有使用 VMM。
    - 如果主机是由 VMM 云管理，则选择“是”。

## <a name="set-up-the-source-environment"></a>设置源环境

设置源环境时，安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并在保管库中注册本地服务器。 

1. 在“准备基础结构”中，单击“源”。 
    - 如果不使用 VMM，则单击“+Hyper-V 站点”，并指定站点名称。 然后单击“+Hyper-V 服务器”，并向站点添加主机或群集。
    - 如果使用 VMM，在“准备源”中，单击“+ VMM”以添加 VMM 服务器。 在“添加服务器”中，检查“System Center VMM 服务器”是否已显示在“服务器类型”中。
2. 下载提供程序和代理组件，具体要取决于你的环境。
    - 仅对于 Hyper-V，下载提供程序安装文件。 在每个 Hyper-V 主机上运行该文件以安装提供程序和代理。
        
        ![不含 VMM 的提供程序](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - 对于含有 VMM 的 Hyper-V，单独下载提供程序和代理。 在 VMM 服务器上运行提供程序安装。 在每个 Hyper-V 主机上运行代理安装。
    
        ![含有 VMM 的提供程序和代理](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. 下载保管库注册密钥。 运行安装提供程序时需要用到此选项。 生成的密钥有效期为 5 天。

### <a name="install-components"></a>安装组件

安装表中汇总的组件。 

**组件** | **详细信息** | **仅 Hyper-V** | **Hyper-V（包含 VMM）**
--- | --- | --- | ---
**Azure Site Recovery 提供程序** | 安排复制到 Azure | 在每个 Hyper-V 主机上安装 | 在 VMM 服务器上安装
**恢复服务代理** | 处理数据复制 | 在每个 Hyper-V 主机上安装 | 在 Hyper-V 主机上安装


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>在没有 VMM 的 Hyper-V 上安装提供程序

在添加到 Hyper-V 站点的每个 Hyper-V 主机上安装提供程序。 如果要在 Hyper-V 群集上进行安装，则在每个群集节点上运行安装程序。 通过安装并注册每个 Hyper-V 群集节点，可确保 VM 即使在节点之间迁移也会受到保护。

1. 在“Microsoft 更新”中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
2. 在“安装”中接受或修改默认提供程序安装位置，并单击“安装”。
4. 在“保管库设置”中，单击“浏览”以选择所下载的保管库密钥文件。 指定 Azure Site Recovery 订阅、保管库名称和 Hyper-V 服务器属于的 Hyper-V 站点。
5. 在“代理设置”中，指定提供程序在 VMM 服务器上运行的方式，或 Hyper-V 主机通过 Internet 连接到 Site Recovery 的方式。
    * 对于直接连接，请选择“不使用代理直接连接到 Azure Site Recovery”。
    * 对于代理，请选择“使用代理服务器连接到 Azure Site Recovery”。 如果需要，请指定代理地址、端口和凭据。
6. 服务器在保管库中注册后，单击“完成”。

Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器显示在“Site Recovery 基础结构” > “Hyper-V 主机”中。


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>在没有 VMM 的 Hyper-V 主机上安装恢复服务代理

如果在你的部署中使用 VMM，请在每个 Hyper-V 主机上运行恢复服务代理设置。

1. 在“设置向导”>“先决条件检查”中，单击“下一步”。 将自动安装任何缺少的必备组件。

    ![恢复服务代理必备组件](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. 在“安装设置”中，接受或修改安装位置和缓存位置。 缓存驱动器需要至少 5 GB 的存储空间。 我们建议使用 600 GB 或更多可用空间的驱动器。 然后单击“安装”。
4. 在“安装”中，当安装完成后，单击“关闭”以完成向导。

##### <a name="set-up-internet-access-via-a-proxy"></a>通过代理设置 Internet 访问

Hyper-V 主机上的恢复服务代理需有权通过 Internet 访问 Azure 才能进行虚拟机复制。 如果通过代理访问 Internet，请按如下所示进行设置：

1. 在 Hyper-V 主机上打开 Microsoft Azure 备份 MMC 管理单元。 默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“代理配置”选项卡上指定代理信息。

    ![注册 MARS 代理](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. 检查代理能够到达[需要的 URL](#prepare-hyper-v-hosts)。

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>安装 VMM 服务器上的提供程序（含有 VMM 的 Hyper-V）

1. 在“Microsoft 更新”中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
2. 在“安装”中接受或修改默认提供程序安装位置，并单击“安装”。 
3. 现在，在保管库中注册 VMM 服务器。 在“保管库设置”中，单击“浏览”以选择所下载的保管库密钥文件。 指定 Azure Site Recovery 订阅和保管库名称。

    ![服务器注册](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. 在“代理设置”中，指定提供程序在 VMM 服务器上运行的方式，或 Hyper-V 主机通过 Internet 连接到 Site Recovery 的方式。

    * 对于直接连接，请选择“不使用代理直接连接到 Azure Site Recovery”。
    * 对于代理，请选择“使用代理服务器连接到 Azure Site Recovery”。 如果需要，请指定代理地址、端口和凭据。
    - 系统将使用指定的代理凭据自动创建一个 VMM 运行方式帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可通过 VMM 控制台 >“设置” > “安全” > “运行方式帐户”修改运行方式帐户设置。 重启 VMM 服务以更新更改。
5. 在“数据加密”中，指定是否对发送到 Azure 的所有数据进行加密。 如果选择此选项，则 Site Recovery 将颁发一个证书。 稍后将需要使用此证书解密数据。
6. 在“VMM 服务器”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集配置中，请指定 VMM 群集角色名称。 在将云元数据与保管库同步”中，选择是否要将 VMM 服务器上所有云元数据与保管库进行同步。 此操作在每个服务器上只需执行一次。 如果不想要同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。

完成注册后，Azure Site Recovery 将检索服务器中的元数据，该 VMM 服务器显示在“Site Recovery 基础结构”中。






## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。 

1. 单击“准备基础结构” > “目标”。
2. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组。 选择要在 Azure 中用于虚拟机的部署模型。

3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="configure-network-mapping-with-vmm"></a>配置网络映射（含有 VMM）

如果使用 VMM，则设置网络映射。

1. 在“Site Recovery 基础结构” > “网络映射” > “网络映射”中，单击“+网络映射”图标。
2. 在“添加网络映射”中，选择源 VMM 服务器。 选择 Azure 作为目标。
3. 在故障转移后，验证订阅和部署模型。
4. 在“源网络”中，选择源本地虚拟机网络。
5. 在“目标网络”中，选择在故障转移后副本 Azure VM 创建时所在的 Azure 网络。 然后单击“确定”。

    ![网络映射](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>创建复制策略

1. 单击“准备基础结构” > “复制设置” > “+创建和关联”。
2. 在“创建和关联策略”中指定策略名称。
3. 在“复制频率”中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。

    > [!NOTE]
    >  复制到高级存储时，不支持 30 秒的频率。 该限制取决于高级存储支持的每 blob 快照数 (100)。 [了解详细信息](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)。

4. 在“恢复点保留期”中，针对每个恢复点指定保留期的时长（以小时为单位）。 受保护的计算机可以恢复到某个时段内的任意时间点。
5. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。 Hyper-V 使用两种类型的快照：
    - 标准快照：提供整个虚拟机的增量快照。
    - 应用一致快照：拍摄虚拟机内应用程序数据的时点快照。 卷影复制服务 (VSS) 可确保应用在拍摄快照时处于一致状态。 启用应用程序一致性快照会影响源虚拟机上的应用性能。 设置一个小于配置的附加恢复点数的值。
6. 在“初始复制开始时间”中，指定开始初始复制的时间。 复制通过 Internet 带宽进行，因此，最好将它安排在非繁忙时间。
7. 在“加密 Azure 上存储的数据”中，指定是否加密 Azure 存储中的静态数据。 然后单击“确定”。

    ![复制策略](./media/tutorial-hyper-v-to-azure/replication-policy.png)


创建新策略时，该策略将自动与 VMM 云或 Hyper-V 站点关联。

## <a name="enable-replication"></a>启用复制


1. 单击“复制应用程序” > “源”。 
2. 在“源”中选择 Hyper-V 站点/VMM 服务器/云。 然后单击“确定”。
3. 在“目标”中，将 Azure 作为目标、保管库订阅以及在故障转移后想要在 Azure 中使用的模型进行验证。
4. 选择用于复制数据的存储帐户，以及在故障转移后 Azure VM 所在的 Azure 网络。
5. 在“虚拟机” > “选择”中，选择想要复制的虚拟机。 然后单击“确定”。

 可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 “最后完成保护”作业完毕后，初始复制即已完成，虚拟机可执行故障转移。

## <a name="next-steps"></a>后续步骤
[运行灾难恢复演练](tutorial-dr-drill-azure.md)

