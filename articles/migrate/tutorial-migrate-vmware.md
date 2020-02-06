---
title: 通过无代理 Azure Migrate 服务器迁移功能迁移 VMware VM
description: 了解如何使用 Azure Migrate 运行 VMware VM 的无代理迁移。
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 79ba3f17b225357267cbd114659851614279bb7f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989359"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>将 VMware VM 迁移到 Azure（使用无代理方法）

本文介绍如何使用 Azure Migrate 服务器迁移工具以无代理方法将本地 VMware VM 迁移到 Azure。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与 AWS/GCP VM 实例的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

本教程是演示如何使用 Azure Migrate 服务器评估与迁移工具评估 VMware VM 以及将其迁移到 Azure 的教程系列中的第三篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备要迁移的 VM。
> * 添加 Azure Migration 服务器迁移工具。
> * 发现要迁移的 VM。
> * 开始复制 VM。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行完整的 VM 迁移。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="migration-methods"></a>迁移方法

可以使用 Azure Migrate 服务器迁移工具将 VMware VM 迁移到 Azure。 此工具提供多个选项用于迁移 VMware VM：

- 使用无代理复制进行迁移。 迁移 VM 且无需在其上安装任何组件。
- 使用复制代理进行迁移。 在要复制的 VM 上安装代理。

若要确定是使用无代理迁移还是基于代理的迁移，请查看以下文章：

- [了解](server-migrate-overview.md)无代理迁移的工作原理，并[比较迁移方法](server-migrate-overview.md#compare-migration-methods)。
- 若要使用基于代理的方法，请[阅读此文](tutorial-migrate-vmware-agent.md)。

## <a name="prerequisites"></a>必备条件

开始学习本教程之前，应做好以下准备：

1. 完成本教程系列的[第一篇教程](tutorial-prepare-vmware.md)来设置 Azure 和 VMware，以便能够完成迁移。 具体而言，在本教程中，需要：
    - [准备 Azure](tutorial-prepare-vmware.md#prepare-azure)，以便能够完成迁移。
    - [准备本地环境](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration)，以便能够完成迁移。
    
2. 在将 VMware VM 迁移到 Azure 之前，我们建议你尝试使用 Azure Migrate 服务器评估工具对其进行评估。 若要设置评估，请完成本教程系列的[第二篇教程](tutorial-assess-vmware.md)。 如果你不想要评估 VM，可以跳过此教程。 尽管我们建议你尝试运行评估，但在迁移之前不一定非要这样做。



## <a name="add-the-azure-migrate-server-migration-tool"></a>添加 Azure Migration 服务器迁移工具

如果你未遵循第二篇教程评估 VMware VM，则需要[遵循这些说明](how-to-add-tool-first-time.md)设置 Azure Migrate 项目，并选择 Azure Migrate 服务器迁移工具。 

如果你已遵循第二篇教程并已设置了一个 Azure Migrate 项目，请按如下所述添加 Azure Migrate 服务器迁移工具：

1. 在 Azure Migrate 项目中，单击“概述”。  
2. 在“发现、评估和迁移服务器”中，单击“评估和迁移服务器”。  

     ![评估和迁移服务器](./media/tutorial-migrate-vmware/assess-migrate.png)

3. 在“迁移工具”中，选择“准备好迁移时单击此处添加迁移工具”。  

    ![选择工具](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. 在“工具”列表中，选择“Azure Migrate:  服务器迁移” > “添加工具” 

    ![服务器迁移工具](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备

Azure Migrate 服务器迁移运行一个轻型 VMware VM 设备。 该设备执行 VM 发现，并将 VM 元数据和性能数据发送到 Azure Migrate 服务器迁移。 Azure Migrate 服务器评估工具也使用该设备。

如果你遵循第二篇教程评估了 VMware VM，则已在该教程中设置了该设备。 如果你未遵循该教程，则现在需要设置该设备。 为此，请执行以下操作： 

- 下载 OVA 模板文件，并将其导入 vCenter Server。
- 创建设备，并检查它是否可以连接到 Azure Migrate 服务器评估。 
- 完成设备的首次配置，并将其注册到 Azure Migrate 项目。

遵照[此文](how-to-set-up-appliance-vmware.md)中的说明设置设备。


## <a name="prepare-vms-for-migration"></a>准备要迁移的 VM

Azure Migrate 需要对 VM 进行一些更改，以确保 VM 可迁移到 Azure。

- 对于某些操作系统，Azure Migrate 可自动做出这些更改。 [了解详细信息](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- 如果要迁移的 VM 不使用其中的某种操作系统，请遵照说明准备 VM。
- 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
- 为本地 VM 启用复制后，对这些 VM 做出的配置更改将复制到 Azure。 为了确保复制这些更改，要迁移到的恢复点必须晚于在本地进行配置更改的时间。


### <a name="prepare-windows-server-vms"></a>准备 Windows Server VM

**Action** | **详细信息** | **说明**
--- | --- | ---
确保 Azure VM 中的 Windows 卷使用的驱动器号分配与本地 VM 相同。 | 将 SAN 策略配置为“全部联机”。 | 1.使用管理员帐户登录到 VM，然后打开命令窗口。<br/> 2.键入 **diskpart** 运行 Diskpart 实用工具。<br/> 3.键入 **SAN POLICY=OnlineAll**<br/> 4.键入 Exit 退出 Diskpart，然后关闭命令提示符。
为 Azure VM 启用 Azure 串行访问控制台 | 这有助于完成故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动，这相当于重新启动新的 VM。 | 遵照[这些说明](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console)启用此功能。
安装 Hyper-V 来宾集成 | 如果要迁移的计算机运行 Windows Server 2003，请在 VM 操作系统上安装 Hyper-V 来宾 Integration Services。 | [了解详细信息](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。
远程桌面 | 在 VM 上启用远程桌面，并在所有网络配置文件中检查 Windows 防火墙是否未阻止远程桌面访问。 | [了解详细信息](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)。

### <a name="prepare-linux-vms"></a>准备 Linux VM

**Action** | **详细信息** 
--- | --- | ---
安装 Hyper-V Linux Integration Services | 默认情况下，大多数最新版本的 Linux 分发版已包含此服务。
重新生成 Linux init 映像，以包含所需的 Hyper-V 驱动程序 | 这可以确保 VM 在 Azure 中启动；只需在某些分发版中执行此操作。
启用 Azure 串行控制台日志记录 | 这有助于完成故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动，这相当于重新启动新的 VM。<br/> 遵照[这些说明](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console)启用此功能。
更新设备映射文件 | 更新包含设备名称到卷的关联的设备映射文件，以使用永久设备标识符
更新 fstab 条目 | 更新条目以使用永久卷标识符。
删除 udev 规则 | 删除基于 MAC 地址等属性保留接口名称的所有 udev 规则。
更新网络接口 | 更新网络接口以基于 DHCP 接收 IP 地址。
启用 SSH | 确保启用 SSH，并将 sshd 服务设置为在重新启动时自动启动。<br/> 确保传入的 SSH 连接请求未被 OS 防火墙或脚本化规则阻止。

请[遵循此文](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)操作，其中介绍了在 Azure 上运行 Linux VM 的步骤，并提供了适用于某些热门 Linux 分发版的说明。  


## <a name="replicate-vms"></a>复制 VM

完成发现后，可以开始将 VMware VM 复制到 Azure。 

> [!NOTE]
> 最多可以同时复制 10 台计算机。 如果需要复制更多，则可以每批 10 台同时复制它们。 对于无代理迁移，最多可运行 100 次同时复制。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate:   服务器迁移”，然后单击“复制”。 

    ![复制 VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化”中，选择“是，使用 VMware vSphere”。    
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称，然后选择“确定”。   

    ![源设置](./media/tutorial-migrate-vmware/source-settings.png)

    - 此步骤假设你在完成本教程时已设置了设备。
    - 如果尚未设置设备，请遵照[此文](how-to-set-up-appliance-vmware.md)中的说明。

4. 在“虚拟机”中，选择要复制的计算机。 
    - 如果已针对 VM 运行评估，则可以应用评估结果中提供的 VM 大小和磁盘类型（高级/标准）建议。 为此，请在“从 Azure Migrate 评估导入迁移设置?”中选择“是”选项。  
    - 如果你未运行评估，或者不想要使用评估设置，请选择“否”选项。 
    - 如果你已选择使用评估，请选择 VM 组和评估名称。

    ![选择评估](./media/tutorial-migrate-vmware/select-assessment.png)

5. 在“虚拟机”中，根据需要搜索 VM，并检查要迁移的每个 VM。  然后单击“下一页:  目标设置”。

    ![选择 VM](./media/tutorial-migrate-vmware/select-vms.png)

6. 在“目标设置”中，选择订阅以及要迁移到的目标区域，并指定迁移之后 Azure VM 所在的资源组。  在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。 
7. 在“Azure 混合权益”中： 

    - 如果你不想要应用 Azure 混合权益，请选择“否”。  然后单击“下一步”  。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。  然后单击“下一步”  。

    ![目标设置](./media/tutorial-migrate-vmware/target-settings.png)

8. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性集。  VM 必须符合 [Azure 要求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表将包含建议的大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。  
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性集**：如果迁移后 VM 应位于某个 Azure 可用性集中，请指定该集。 该集必须位于为迁移指定的目标资源组中。

    ![VM 计算设置](./media/tutorial-migrate-vmware/compute-settings.png)

9. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。  然后单击“下一步”  。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。 

    ![磁盘](./media/tutorial-migrate-vmware/disks.png)

10. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。  

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。   开始复制后无法更改设置。

### <a name="provisioning-for-the-first-time"></a>首次预配

如果这是你要在 Azure Migrate 项目中复制的第一个 VM，Azure Migrate 服务器迁移会在项目所在的同一资源组中自动预配这些资源。

- **服务总线**：Azure Migrate 服务器迁移使用服务总线将复制业务流程消息发送到设备。
- **网关存储帐户**：服务器迁移使用网关存储帐户存储有关所要复制的 VM 的状态信息。
- **日志存储帐户**：Azure Migrate 设备将 VM 的复制日志上传到日志存储帐户。 Azure Migrate 将复制信息应用到副本托管磁盘。
- **Key Vault**：Azure Migrate 设备使用 Key Vault 管理服务总线的连接字符串，以及复制中使用的存储帐户的访问密钥。 在准备阶段，你应已设置 Key Vault 访问存储帐户所需的权限。 请[检查这些权限](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault)。   


## <a name="track-and-monitor"></a>跟踪和监视


- 单击“复制”会启动一个“开始复制”作业。  
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 在初始复制期间，会创建一个 VM 快照。 该快照中的磁盘数据将复制到 Azure 中的副本托管磁盘。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

可以在门户通知中跟踪作业状态。

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器”   来监视复制状态。
![监视复制](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器迁移”中，单击“测试已迁移的服务器”。 

     ![测试已迁移的服务器](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。 

    ![测试迁移](./media/tutorial-migrate-vmware/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。  建议使用非生产 VNet。
4. “测试迁移”作业随即启动。  在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。  计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。  

    ![清理迁移](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate:   服务器迁移”，然后单击“复制服务器”。 

    ![复制服务器](./media/tutorial-migrate-vmware/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。  
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。    
    - 默认情况下，Azure Migrate 将关闭本地 VM，并运行按需复制，以同步自上次复制发生以来发生的任何 VM 更改。 这可以确保不会丢失数据。
    - 如果你不想要关闭 VM，请选择“否” 
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。 

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。  这会停止本地计算机的复制，并清理 VM 的复制状态信息。
2. 在已迁移的计算机上安装 Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) 代理。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 从本地 VM 清单中删除本地 VM。
7. 从本地备份中删除本地 VM。
8. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)锁定和限制入站流量访问。
    - 使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
-  考虑部署[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)。
