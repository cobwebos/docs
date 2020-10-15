---
title: 通过无代理 Azure Migrate 服务器迁移功能迁移 VMware VM
description: 了解如何使用 Azure Migrate 运行 VMware VM 的无代理迁移。
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: 37181246a20044f16414735e2247fa90fc36433b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530516"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>将 VMware VM 迁移到 Azure（使用无代理方法）

本文介绍如何使用 [Azure Migrate 服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具以无代理迁移方法将本地 VMware VM 迁移到 Azure。 还可以使用基于代理的迁移来迁移 VMware VM。 [比较](server-migrate-overview.md#compare-migration-methods)方法。

本教程是演示如何评估 VMware VM 以及将其迁移到 Azure 的教程系列中的第三篇文章。 

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 


在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 添加 Azure Migrate:服务器迁移工具。
> * 发现要迁移的 VM。
> * 开始复制 VM。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行完整的 VM 迁移。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

开始学习本教程之前，应做好以下准备：

1. [完成第一篇教程](tutorial-prepare-vmware.md)来准备 Azure 和 VMware，以便进行迁移。
2. 建议先完成第二篇教程以[评估 VMware VM](tutorial-assess-vmware.md)，然后再将它们迁移到 Azure，但不一定要这样做。 


## <a name="add-the-azure-migrate-server-migration-tool"></a>添加 Azure Migration 服务器迁移工具

如果尚未设置 Azure Migrate 项目，请在添加工具之前[执行该操作](how-to-add-tool-first-time.md)。 如果已设置项目，请如下所述添加工具：

1. 在 Azure Migrate 项目中，单击“概述”。 
2. 在“发现、评估和迁移服务器”中，单击“评估和迁移服务器”。 

     ![评估和迁移服务器](./media/tutorial-migrate-vmware/assess-migrate.png)

3. 在“迁移工具”中，选择“准备好迁移时单击此处添加迁移工具”。 

    ![选择工具](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. 在“工具”列表中，选择“Azure Migrate:服务器迁移” > “添加工具”

    ![服务器迁移工具](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>设置 Azure Migrate 设备

Azure Migrate 服务器迁移运行一个轻型 VMware VM 设备，该设备用于 VMware VM 的发现、评估和无代理迁移。 如果按照[评估教程](tutorial-assess-vmware.md)操作，则已将该设备设置为已启动。 如果尚未这样做，请使用以下方法之一立即进行设置：

- OVA 模板：使用下载的 OVA 模板在 VMware VM 上进行[设置](how-to-set-up-appliance-vmware.md)。
- **脚本**：使用 PowerShell 安装程序脚本在 VMware VM 或物理计算机上进行[设置](deploy-appliance-script.md)。 如果无法使用 OVA 模板设置 VM，或者你使用的是 Azure 政府，则应使用此方法。

创建设备后，请检查它是否可以连接到 Azure Migrate:服务器评估，首次配置该设备，并将其注册到 Azure Migrate 项目。

## <a name="replicate-vms"></a>复制 VM

设置设备并完成发现之后，可以开始将 VMware VM 复制到 Azure。 

- 最多可以同时运行 300 个复制。
- 在门户中，一次最多可选择迁移 10 个 VM。 若要迁移更多计算机，请将它们 10 个一批地添加到组中。

请按如下所述启用复制：

1. 在 Azure Migrate 项目中选择“服务器”>“Azure Migrate: 服务器迁移”，然后单击“复制”。

    ![复制 VM](./media/tutorial-migrate-vmware/select-replicate.png)

2. 在“复制”>“源设置” > “你的计算机是否已虚拟化”中，选择“是，使用 VMware vSphere”。   
3. 在“本地设备”中，选择已设置的 Azure Migrate 设备的名称，然后选择“确定”。  

    ![源设置](./media/tutorial-migrate-vmware/source-settings.png)

4. 在“虚拟机”中，选择要复制的计算机。 若要从评估（如果已运行）应用 VM 大小调整和磁盘类型，请在“从 Azure Migrate 评估导入迁移设置?”中，选择“是”，然后选择 VM 组和评估名称。 如果不使用评估设置，请选择“否”。
   
    ![选择评估](./media/tutorial-migrate-vmware/select-assessment.png)

5. 在“虚拟机”中，选择要迁移的 VM。 然后单击“下一页:目标设置”。

    ![选择 VM](./media/tutorial-migrate-vmware/select-vms.png)

6. 在“目标设置”中，选择订阅和目标区域。 指定迁移后 Azure VM 所在的资源组。
7. 在“虚拟网络”中，选择迁移之后 Azure VM 加入的 Azure VNet/子网。
8. 在“可用性选项”中，选择：
    -  可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 如果选择此选项，则需要在“计算”选项卡中指定用于每个选定计算机的可用性区域。仅当为迁移选择的目标区域支持可用性区域时，此选项才可用
    -  可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。
    - 如果不需要为迁移的计算机使用其中任何可用性配置，则选择“无需基础结构冗余”选项。

9. 在“Azure 混合权益”中：

    - 如果你不想要应用 Azure 混合权益，请选择“否”。 然后单击“下一步”  。
    - 如果你的 Windows Server 计算机享有有效软件保障或 Windows Server 订阅的权益，并且你想要将此权益应用到所要迁移的计算机，请选择“是”。 然后单击“下一步”  。

    ![目标设置](./media/tutorial-migrate-vmware/target-settings.png)

10. 在“计算”中，查看 VM 名称、大小、OS 磁盘类型和可用性配置（如果在上一步中选定）。 VM 必须符合 [Azure 要求](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)。

    - **VM 大小**：如果你正在使用评估建议，则 VM 大小下拉列表会显示建议大小。 否则，Azure Migrate 会根据 Azure 订阅中最接近的匹配项选择大小。 或者，请在“Azure VM 大小”中的手动选择一个大小。 
    - **OS 磁盘**：为 VM 指定 OS（启动）磁盘。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 
    - **可用性区域**：指定要使用的可用性区域。
    - **可用性集**：指定要使用的可用性集。

> [!NOTE]
>如果要为一组虚拟机选择其他可用性选项，请转到步骤 1，在开始为一组虚拟机执行复制后，通过选择其他可用性选项来重复这些步骤。


 ![VM 计算设置](./media/tutorial-migrate-vmware/compute-settings.png)

11. 在“磁盘”中，指定是否要将 VM 磁盘复制到 Azure，并选择 Azure 中的磁盘类型（标准 SSD/HDD 或高级托管磁盘）。 然后单击“下一步”。
   
    ![屏幕截图显示“复制”对话框的“磁盘”选项卡。](./media/tutorial-migrate-vmware/disks.png)

12. 在“检查并开始复制”中检查设置，然后单击“复制”启动服务器的初始复制。 

> [!NOTE]
> 在复制开始之前，随时可以更新复制设置（在“管理” > “复制计算机”中 ）。 复制开始之后无法更改设置。

### <a name="provisioning-for-the-first-time"></a>首次预配

如果这是你要在项目中复制的第一个 VM，则服务器迁移会在项目所在的同一资源组中自动预配这些资源。

- **服务总线**：服务器迁移使用服务总线将复制业务流程消息发送到设备。
- **网关存储帐户**：服务器迁移使用网关存储帐户存储有关所要复制的 VM 的状态信息。
- **日志存储帐户**：Azure Migrate 设备将 VM 的复制日志上传到日志存储帐户。 Azure Migrate 将复制信息应用到副本托管磁盘。
- **Key Vault**：Azure Migrate 设备使用 Key Vault 管理服务总线的连接字符串，以及复制中使用的存储帐户的访问密钥。

## <a name="track-and-monitor"></a>跟踪和监视

1. 在门户通知中跟踪作业状态。
2. 通过单击“Azure Migrate: 服务器迁移”中的“正在复制服务器” 来监视复制状态。

     ![监视复制](./media/tutorial-migrate-vmware/replicating-servers.png)

复制按如下方式进行：
- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 在初始复制期间，会创建一个 VM 快照。 该快照中的磁盘数据将复制到 Azure 中的副本托管磁盘。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

## <a name="run-a-test-migration"></a>运行测试迁移


增量复制开始后，在运行到 Azure 的完整迁移之前，可以针对 VM 运行测试迁移。 我们强烈建议在迁移之前，针对每台计算机至少执行此操作一次。

- 运行测试迁移可以检查迁移是否按预期进行，而不会影响本地计算机，它们仍可保持正常运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

按如下所述执行测试迁移：


1. 在“迁移目标” > “服务器” > “Azure Migrate:  服务器迁移”中，单击“测试已迁移的服务器”。

     ![测试已迁移的服务器](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. 右键单击要测试的 VM，然后单击“测试迁移”。

    ![测试迁移](./media/tutorial-migrate-vmware/test-migrate.png)

3. 在“测试迁移”中，选择 Azure VM 在迁移后所在的 Azure VNet。 建议使用非生产 VNet。
4. “测试迁移”作业随即启动。 在门户通知中监视该作业。
5. 迁移完成后，在 Azure 门户上的“虚拟机”中查看已迁移的 Azure VM。 计算机名称带有 **-Test** 后缀。
6. 测试完成后，在“复制计算机”中右键单击该 Azure VM，然后单击“清理测试迁移”。

    ![清理迁移](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>迁移 VM

确认测试迁移按预期方式进行后，可以迁移本地计算机。

1. 在 Azure Migrate 项目中选择“服务器” > “Azure Migrate: 服务器迁移”，然后单击“复制服务器”。

    ![复制服务器](./media/tutorial-migrate-vmware/replicate-servers.png)

2. 在“复制计算机”中，右键单击该 VM 并选择“迁移”。
3. 在“迁移” > “关闭虚拟机并执行计划迁移(不会丢失任何数据)”中，选择“是” > “确定”。   
    - 默认情况下，Azure Migrate 将关闭本地 VM，并运行按需复制，以同步自上次复制发生以来发生的任何 VM 更改。 这可以确保不会丢失数据。
    - 如果你不想要关闭 VM，请选择“否”
4. 随即会针对该 VM 启动一个迁移作业。 在 Azure 通知中跟踪该作业。
5. 该作业完成后，可以从“虚拟机”页查看和管理该 VM。

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，右键单击该 VM 并选择“停止复制”。 这会停止本地计算机的复制，并清理 VM 的复制状态信息。
2. 在已迁移的计算机上安装 Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) 代理。
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
    - 使用 [Azure 安全中心 - 适时管理](../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../virtual-network/security-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
-  考虑部署[Azure 成本管理](../cost-management-billing/cloudyn/overview.md)以监视资源使用率和支出。


## <a name="next-steps"></a>后续步骤

在 Azure 云采用框架中调查[云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)。
