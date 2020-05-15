---
title: 使用 Azure Migrate 服务器迁移工具将 Hyper-V VM 迁移到 Azure
description: 了解如何使用 Azure Migrate 服务器迁移工具将本地 Hyper-V VM 迁移到 Azure
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 87f746108599928d3e1b4a022abc1b3a3779ef29
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853553"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>将 Hyper-V VM 迁移到 Azure 

本文介绍如何使用 Azure Migrate 通过无代理迁移将本地 Hyper-V VM 迁移到 Azure：服务器迁移工具。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

本教程是教程系列中的第三篇教程，演示了如何使用 Azure Migrate 服务器评估与服务器迁移工具评估 Hyper-V 并将其迁移到 Azure。 在本教程中，你将了解如何执行以下操作：


> [!div class="checklist"]
> * 准备 Azure 和本地 Hyper-V 环境
> * 设置源环境。
> * 设置目标环境。
> * 启用复制。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行到 Azure 的完整迁移。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

开始学习本教程之前，应做好以下准备：

1. [查看](hyper-v-migration-architecture.md) Hyper-V 迁移体系结构。
2. [查看](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V 主机要求以及 Hyper-V 主机需要访问的 Azure URL。
3. [查看](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)要迁移的 Hyper-V VM 的要求。 Hyper-V VM 必须符合 [Azure VM 要求](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)。
2. 我们建议你完成本系列中前面的教程。 [第一篇教程](tutorial-prepare-hyper-v.md)演示了如何针对迁移设置 Azure 和 Hyper-V。 第二篇教程演示了如何在迁移之前使用 Azure Migrate:服务器评估来[评估 Hyper-V VM](tutorial-assess-hyper-v.md)。 
    > [!NOTE]
    > 尽管我们建议你尝试运行评估，但在迁移 VM 之前不一定非要这样做。
    > 对于迁移 Hyper-V VM，Azure Migrate:服务器迁移在 Hyper-V 主机或群集节点上运行软件代理（Microsoft Azure Site Recovery 提供程序和 Microsoft Azure 恢复服务代理），以便进行协调并将数据复制到 Azure Migrate。 [Azure Migrate 设备](migrate-appliance.md)不用于 Hyper-V 迁移。

3. 确保为 Azure 帐户分配“虚拟机参与者”角色，以便你有权执行以下操作：

    - 在所选资源组中创建 VM。
    - 在所选虚拟网络中创建 VM。
    - 写入 Azure 托管磁盘。
4. [设置 Azure 网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 迁移到 Azure 时，创建的 Azure VM 将加入到设置迁移时指定的 Azure 网络。

## <a name="add-the-azure-migrateserver-migration-tool"></a>添加 Azure Migrate:服务器迁移工具

添加 Azure Migrate:服务器迁移工具。

- 如果你已按照第二篇教程来[评估 VMware VM](tutorial-assess-hyper-v.md)，则已设置了一个 Azure Migrate 项目，现在可以继续添加工具。
- 如果未按照第二篇教程操作，请[按照这些说明](how-to-add-tool-first-time.md)设置 Azure Migrate 项目。 创建项目时，请添加 Azure Migrate:服务器迁移工具。

如果已设置项目，请如下所述添加工具：

1. 在 Azure Migrate 项目中，单击“概述”。  
2. 在“发现、评估和迁移服务器”中，单击“评估和迁移服务器”。  
3. 在“迁移工具”中，选择“准备好迁移时单击此处添加迁移工具”。  

    ![选择工具](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. 在“工具”列表中，选择“Azure Migrate:  服务器迁移” > “添加工具” 

    ![服务器迁移工具](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>准备 Hyper-V 主机


1. 在 Azure Migrate 项目中选择“服务器”，在“Azure Migrate:   服务器迁移”中，单击“发现”。 
2. 在“发现计算机” > “计算机是否已虚拟化?”中，选择“是，使用 Hyper-V”。   
3. 在“目标区域”中，选择要将计算机迁移到的 Azure 区域。 
6. 选择“确认迁移的目标区域为 <区域名称>”。 
7. 单击“创建资源”。  随即会在后台创建一个 Azure Site Recovery 保管库。
    - 如果已设置使用 Azure Migrate 服务器迁移进行迁移，则不会显示此选项，因为之前已设置了资源。
    - 单击此按钮后，无法更改此项目的目标区域。
    - 所有后续迁移的目标都是此区域。
    
8. 在“准备 Hyper-V 主机服务器”中，下载 Hyper-V 复制提供程序和注册密钥文件。 
    - 需要使用该注册密钥将 Hyper-V 主机注册到 Azure Migrate 服务器迁移。
    - 生成的密钥有效期为 5 天。

    ![下载提供程序和密钥](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. 将提供程序安装文件和注册密钥文件复制到运行要复制的 VM 的每个 Hyper-V 主机（或群集节点）。
5. 按下一过程中所述，在每台主机上运行提供程序安装程序文件。
6. 在主机上安装提供程序后，在“发现计算机”中单击“完成注册”。  

    ![完成注册](./media/tutorial-migrate-hyper-v/finalize-registration.png)

完成注册后，最长可能需要经过 15 分钟，已发现的 VM 才会出现在 Azure Migrate 服务器迁移中。 随着 VM 的发现，“已发现的服务器”计数会不断增大。 

![已发现的服务器](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>复制 Hyper-V VM

完成发现后，可以开始将 Hyper-V VM 复制到 Azure。

> [!NOTE]
> 最多可以同时复制 10 台计算机。 如果需要复制更多，则可以每批 10 台同时复制它们。

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate:   服务器迁移”，然后单击“复制”。 
2. 在“复制”>“源设置” > “你的计算机是否已虚拟化”中，选择“是，使用 Hyper-V”。     然后单击“下一页:  虚拟机”。
3. 在“虚拟机”中，选择要复制的计算机。 
    - 如果已针对 VM 运行评估，则可以应用评估结果中提供的 VM 大小和磁盘类型（高级/标准）建议。 为此，请在“从 Azure Migrate 评估导入迁移设置?”中选择“是”选项。  
    - 如果你未运行评估，或者不想要使用评估设置，请选择“否”选项。 
    - 如果你已选择使用评估，请选择 VM 组和评估名称。

        ![选择评估](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. 在“虚拟机”中，根据需要搜索 VM，并检查要迁移的每个 VM。  然后单击“下一步:  目标设置”。

    ![选择 VM](./media/tutorial-migrate-hyper-v/select-vms.png)

5. 在“目标设置”中，选择要迁移到的目标区域、订阅，以及迁移之后 Azure VM 所在的资源组。 
7. 在“复制存储帐户”  中，选择将在 Azure 中存储复制数据的 Azure 存储帐户。
8. 在“虚拟网络”中，选择迁移之后 Azure VM 要加入到的 Azure VNet/子网。 
9. 在“Azure 混合权益”中： 

    - 如果你不想要应用 Azure 混合权益，请选择“否”。  然后单击“下一步”。 
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。  然后单击“下一步”  。

    ![目标设置](./media/tutorial-migrate-hyper-v/target-settings.png)

10. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性集。  VM 必须符合 [Azure 要求](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表将包含建议的大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。  
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性集**：如果迁移后 VM 应位于某个 Azure 可用性集中，请指定该集。 该集必须位于为迁移指定的目标资源组中。

    ![VM 计算设置](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。  然后单击“下一步”  。
    - 可以从复制中排除磁盘。
    - 如果排除了磁盘，迁移后，这些磁盘将不会出现在 Azure VM 中。 

    ![磁盘](./media/tutorial-migrate-hyper-v/disks.png)

10. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。  

> [!NOTE]
> 在复制开始之前，随时可以在“管理” > “复制计算机”中更新复制设置。   开始复制后无法更改设置。

## <a name="provisioning-for-the-first-time"></a>首次预配

如果这是你要在 Azure Migrate 项目中复制的第一个 VM，则 Azure Migrate:服务器迁移会自动在该项目所在的资源组中预配这些资源。

- **服务总线**：“Azure Migrate:服务器迁移使用服务总线将复制业务流程消息发送到设备。
- **网关存储帐户**：“Azure Migrate:服务器迁移使用网关存储帐户存储有关所要复制的 VM 的状态信息。
- **日志存储帐户**：Azure Migrate 设备将 VM 的复制日志上传到日志存储帐户。 Azure Migrate 将复制信息应用到副本托管磁盘。
- **Key Vault**：Azure Migrate 设备使用 Key Vault 管理服务总线的连接字符串，以及复制中使用的存储帐户的访问密钥。 在[准备 Azure](tutorial-prepare-hyper-v.md#prepare-azure) 以进行 Hyper-V VM 评估和迁移时，应该已设置密钥保管库访问存储帐户所需的权限。 


## <a name="track-and-monitor"></a>跟踪和监视


- 单击“复制”会启动一个“开始复制”作业。  
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure。

可以在门户通知中跟踪作业状态。

可以通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器”   来监视复制状态。
![监视复制](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 Azure VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:    服务器迁移”中，单击“测试已迁移的服务器”。 

     ![测试已迁移的服务器](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。 

    ![测试迁移](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure 虚拟网络。  建议使用非生产虚拟网络。
4. “测试迁移”作业随即启动。  在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。  计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。  

    ![清理迁移](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate:   服务器迁移”，然后单击“复制服务器”。 

    ![复制服务器](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。  
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。    
    - 默认情况下，Azure Migrate 将关闭本地 VM，并运行按需复制，以同步自上次复制发生以来发生的任何 VM 更改。 这可以确保不会丢失数据。
    - 如果你不想要关闭 VM，请选择“否” 
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。 

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止迁移”。  这样会执行以下操作：
    - 停止本地计算机的复制。
    - 从 Azure Migrate 的“复制服务器”  计数中删除该计算机：服务器迁移。
    - 清除 VM 的复制状态信息。
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
