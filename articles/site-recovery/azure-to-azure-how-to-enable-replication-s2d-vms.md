---
title: 将运行存储空间直通的 Azure Vm 复制 Azure Site Recovery
description: 了解如何使用 Azure Site Recovery 复制运行存储空间直通的 Azure Vm。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 465c0ddd91e81bb597a996637fdc2147e90464de
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942209"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>将运行存储空间直通的 Azure Vm 复制到另一个区域

本文介绍了如何为运行存储空间直通的 Azure VM 启用灾难恢复。

>[!NOTE]
>存储空间直通群集仅支持故障一致恢复点。
>

[存储空间直通（S2D）](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct)是软件定义的存储，它提供了一种在 Azure 上创建[来宾群集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure)的方法。  Microsoft Azure 中的来宾群集是由 IaaS Vm 组成的故障转移群集。 它允许托管 VM 工作负荷跨来宾群集进行故障转移，实现应用程序的更高可用性 SLA，而不是单个 Azure VM 提供。 它在 VM 承载关键应用程序（如 SQL 或横向扩展文件服务器）的情况下非常有用。

## <a name="disaster-recovery-with-storage-spaces-direct"></a>利用存储空间直通进行灾难恢复

在典型方案中，为了提高横向扩展文件服务器等应用程序的复原能力，可能会在 Azure 上使用虚拟机来宾群集。 虽然这样可以提高应用程序可用性，但仍希望使用 Site Recovery 保护这些应用程序免受任何区域级别故障。 发生故障转移时，Site Recovery 将数据从一个 Azure 区域复制到另一个 Azure 区域，并调出灾难恢复区域中的群集。

下图显示了使用存储空间直通的双节点 Azure VM 故障转移群集。

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Windows 故障转移群集中有两个 Azure 虚拟机，每个虚拟机都有两个或多个数据磁盘。
- S2D 同步数据磁盘上的数据，并以存储池的形式提供同步的存储。
- 存储池作为群集共享卷 (CSV) 提供给故障转移群集。
- 故障转移群集对数据驱动器使用 CSV。

**灾难恢复注意事项**

1. 为群集设置[云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)时，请让见证一直位于灾难恢复区域中。
2. 若要将虚拟机故障转移到与源区域不同的 DR 区域上的子网，那么需要在执行故障转移后更改群集 IP 地址。  若要更改群集的 IP，需要使用 Site Recovery[恢复计划脚本。](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[示例脚本](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1)使用自定义脚本扩展在 VM 中执行命令 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>为 S2D 群集启用 Site Recovery：

1. 在恢复服务保管库中，单击“+复制”
1. 选择群集中的所有节点，并将它们归入[多 VM 一致性组](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. 选择复制策略，同时禁用应用程序一致性*（仅支持故障一致性）
1. 启用复制

   ![storagespacesdirect 保护](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. 转到已复制的项，可以看到两个虚拟机的状态。 
3. 两个虚拟机均受到保护，并显示为属于多 VM 一致性组。

   ![storagespacesdirect 保护](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>创建恢复计划
恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="adding-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组

1.  通过添加虚拟机来创建恢复计划。
2.  单击“自定义”将 VM 分组。 默认情况下，所有 VM 属于“组 1”。


### <a name="add-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移后或测试故障转移期间，可能需要在 Azure 虚拟机上执行一些操作才能让应用程序正常工作。 可将某些故障转移后的操作自动化。 例如，在这里，我们将连接负载均衡器并更改群集 IP。


### <a name="failover-of-the-virtual-machines"></a>虚拟机故障转移 
Vm 的两个节点都需要使用 Site Recovery[恢复计划](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)进行故障转移 

![storagespacesdirect 保护](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>运行测试故障转移
1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择已创建的恢复计划。
3.  选择“测试故障转移”。
4.  若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
5.  当辅助环境启动时，执行验证。
6.  完成验证后，选择“清理测试故障转移”清理测试故障转移环境。

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“故障转移”。
4.  若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。
## <a name="next-steps"></a>后续步骤

[详细了解](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)如何运行故障回复。
