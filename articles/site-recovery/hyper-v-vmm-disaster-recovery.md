---
title: "使用 Azure Site Recovery 为本地站点之间的 Hyper-V VM 设置灾难恢复 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 为本地站点之间的 Hyper-V VM 设置灾难恢复。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>为 Hyper-V VM 设置到辅助本地站点的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，进而有利于灾难恢复策略。

本文介绍如何为 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V VM 设置到辅助站点的灾难恢复。 在本文中，学习如何：

> [!div class="checklist"]
> * 准备本地 VMM 服务器和 Hyper-V 主机
> * 为 Site Recovery 创建恢复服务保管库 
> * 设置源复制环境和目标复制环境。 
> * 设置网络映射 
> * 创建复制策略
> * 为虚拟机启用复制

## <a name="prerequisites"></a>先决条件

完成本方案：

- 查看[方案体系结构和组件](hyper-v-vmm-architecture.md)。
- 确保 VMM 服务器和 Hyper-V 主机符合[支持要求](hyper-v-vmm-secondary-support-matrix.md)。
- 确保要复制的 VM 符合[复制计算机支持](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support)。
- 准备 VMM 服务器进行网络映射。

### <a name="prepare-for-network-mapping"></a>准备网络映射

[网络映射](hyper-v-vmm-network-mapping.md)在源和目标云中的本地 VMM VM 网络之间映射。 映射可执行以下操作：

- 故障转移后，将 VM 连接到适当的目标 VM 网络。 
- 最好将副本 VM 放于目标 Hyper-V 主机服务器上。 
- 如果不配置网络映射，则故障转移后，VM 副本不会连接到 VM 网络。

准备 VMM，如下所示：

1. 确保源服务器和目标 VMM 服务器上具有 [VMM 逻辑网络](https://docs.microsoft.com/system-center/vmm/network-logical)。
    - 源服务器上的逻辑网络应与 Hyper-V 主机所在的源云相关联。
    - 目标服务器上的逻辑网络应与目标云相关联。
1. 确保源服务器和目标 VMM 服务器上具有 [VM 网络](https://docs.microsoft.com/system-center/vmm/network-virtual)。 VM 网络应链接到每个位置中的逻辑网络。
2. 将源 Hyper-V 主机上的 VM 连接到源 VM 网络。 


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。

1. 单击“Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。
2. 选择“到恢复站点”，并选择“是，使用 Hyper-V”。
3. 选择“是”，指示将使用 VMM 管理 Hyper-V 主机。
4. 如果具有辅助 VMM 服务器，请选择“是”。 如果要在单个 VMM 服务器上的云之间部署复制，请单击“否”。 然后单击“确定”。


## <a name="set-up-the-source-environment"></a>设置源环境

在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在保管库中发现和注册这些服务器。

1. 单击“准备基础结构” > “源”。
2. 在“准备源”中单击“+VMM”以添加 VMM 服务器。
3. 在“添加服务器”中，检查“服务器类型”中是否已显示“System Center VMM 服务器”。
4. 下载 Azure Site Recovery 提供程序安装文件。
5. 下载注册密钥。 安装提供程序时需要此密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. 在每个 VMM 服务器上安装提供程序。 无需在 Hyper-V 主机上显式安装任何组件。

### <a name="install-the-azure-site-recovery-provider"></a>安装 Azure Site Recovery 提供程序

1. 在每个 VMM 服务器上运行提供程序安装文件。 如果 VMM 部署在群集中，请如下进行首次安装：
    -  在活动节点上安装提供程序，并完成安装以在保管库中注册 VMM 服务器。
    - 然后在其他节点上安装提供程序。 所有群集节点应运行相同版本的提供程序。
2. 安装程序将运行几项先决条件检查，并请求停止 VMM 服务的权限。 VMM 服务会在安装程序完成时自动重启。 如果在 VMM 群集上进行安装，系统会提示停止群集角色。
3. 在“Microsoft 更新”中，可选择指定根据 Microsoft 更新策略安装提供程序更新。
4. 在“安装”中接受或修改默认安装位置，并单击“安装”。
5. 安装完成后，单击“注册”，以便在保管库中注册服务器。

    ![安装位置](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. 在“保管库名称”中，验证将要在其中注册服务器的保管库的名称。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“代理连接”中，指定在 VMM 服务器上运行的提供程序如何连接到 Azure。
   - 可指定提供程序应直接连接到 Internet 还是通过代理连接。 根据需要指定代理设置。
   - 如果使用代理，则系统将使用指定的代理凭据自动创建一个 VMM 运行方式帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可通过 VMM 控制台 >“设置” > “安全” > “运行方式帐户”修改运行方式帐户设置。
   - 重启 VMM 服务以更新更改。
8. 在“注册密钥”中，选择已下载并复制到 VMM 服务器的密钥。
9. 此方案与加密设置无关。 
10. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集中，指定 VMM 群集角色名称。
11. 在“同步云元数据”中，选择是否要同步 VMM 服务器上所有云的元数据。 此操作在每个服务器上只需执行一次。 如果不希望同步所有云，请不要选择此设置。 可在 VMM 控制台的云属性中单独同步每个云。
12. 单击“下一步”以完成此过程。 注册后，Site Recovery 将检索 VMM 服务器中的元数据。 服务器显示在保管库中的“服务器” > “VMM 服务器”中。
13. 服务器出现在保管库后，请在“源” > “准备源”中选择 VMM 服务器，并选择 Hyper-V 主机所在的云。 然后单击“确定”。


## <a name="set-up-the-target-environment"></a>设置目标环境

选择目标 VMM 服务器和云：

1. 单击“准备基础结构” > “目标”，并选择目标 VMM 服务器。
2. 此时会显示与 Site Recovery 同步的 VMM 云。 选择目标云。

   ![目标](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>设置复制策略

开始之前，请确保所有使用该策略的主机均具有相同的操作系统。 如果主机运行不同版本的 Windows Server，则需要多个复制策略。

1. 若要创建新的复制策略，请单击“准备基础结构” > “复制设置” > “+创建和关联”。
2. 在“创建和关联策略”中指定策略名称。 源和目标类型应为 **Hyper-V**。
3. 在“Hyper-V 主机版本”中，选择主机上运行的操作系统。
4. 在“身份验证类型”和“身份验证端口”中，指定如何对主 Hyper-V 主机服务器和恢复 Hyper-V 主机服务器之间的流量进行身份验证。
    - 除非有正常运行的 Kerberos 环境，否则请选择“证书”。 Azure Site Recovery 将为 HTTPS 身份验证自动配置证书。 无需手动执行任何操作。
    - 默认情况下，端口 8083 和 8084（用于证书）在 Hyper-V 主机服务器上的 Windows 防火墙中将处于打开状态。
    - 如果选择“Kerberos”，将使用 Kerberos 票证执行主机服务器的相互身份验证。 Kerberos 仅适用于在 Windows Server 2012 R2 或更高版本上运行的 Hyper-V 主机服务器。
1. 在“复制频率” 中，指定要在初始复制后复制增量数据的频率（每隔 30 秒、5 或 15 分钟）。
2. 在“恢复点保留期”中，指定每个恢复点的保留期时长（以小时为单位）。 复制的计算机可以恢复到某个时段内的任意时间点。
3. 在“应用一致性快照频率”中，指定创建包含应用程序一致性快照的恢复点的频率（1-12 小时）。 Hyper-V 使用两种类型的快照：
    - 标准快照：提供整个虚拟机的增量快照。
    - 应用一致快照：拍摄虚拟机内应用程序数据的时点快照。 卷影复制服务 (VSS) 确保应用在拍摄快照时处于一致状态。 启用应用程序一致性快照会影响源 VM 的应用性能。 确保设置的值小于所配置的附加恢复点数量。
4. 在“数据传输压缩”中，指定是否应压缩所传输的复制数据。
5. 选择“删除副本 VM”，指定禁用对源 VM 的保护时应删除副本虚拟机。 如果启用此设置，禁用对源 VM 的保护时，将从站点恢复控制台中删除该 VM、在 VMM 控制台中删除 VMM 的站点恢复设置，并删除副本。
6. 如果要通过网络进行复制，请在“初始复制方法”中指定是要启动还是计划初始复制。 要节省网络带宽，可以将它计划在非高峰时间运行。 然后单击“确定”。

     ![复制策略](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. 新策略自动与 VMM 云关联。 在“复制策略”中单击“确定”。 


## <a name="enable-replication"></a>启用复制

1. 单击“复制应用程序” > “源”。 
2. 在“源”中，选择 VMM 服务器和要复制的 Hyper-V 主机所在的云。 然后单击“确定”。
3. 在“目标”中，确认辅助 VMM 服务器和云。
4. 在“虚拟机”中，从列表中选择要保护的 VM。


可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 “最后完成保护”作业完毕后，初始复制即已完成，VM 可执行故障转移。

## <a name="next-steps"></a>后续步骤

[运行灾难恢复演练](hyper-v-vmm-test-failover.md)
