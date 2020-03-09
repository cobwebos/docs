---
title: 使用 Azure Site Recovery 将 VMware Vm 重新保护到本地站点
description: 了解如何在通过 Azure Site Recovery 故障转移到 Azure 后重新保护 VMware Vm。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362546"
---
# <a name="reprotect-from-azure-to-on-premises"></a>从 Azure 到本地进行重新保护

将本地 VMware VM 或物理服务器[故障转移](site-recovery-failover.md)到 Azure 后，故障回复到本地站点的第一步是重新保护故障转移期间创建的 Azure VM。 本文介绍如何执行此操作。 

## <a name="before-you-begin"></a>开始之前

1. 请按照[本文](vmware-azure-prepare-failback.md)中的步骤准备重新保护和故障回复，包括在 Azure 中设置进程服务器，以及在本地主目标服务器中配置站点到站点 VPN 或 ExpressRoute 专用对等互连。
2. 请确保本地配置服务器正在运行并已连接到 Azure。 在故障转移到 Azure 的过程中，本地站点可能无法访问，并且配置服务器可能不可用或已关闭。 在故障回复期间，虚拟机必须位于配置服务器数据库中。 否则，故障回复不会成功。
3. 删除本地主目标服务器上的所有快照。 如果存在快照，则重新保护将不起作用。  重新保护作业期间，VM 上的快照会自动合并。
4. 如果要为多 VM 一致性收集收集到复制组的 Vm，请确保它们都具有相同的操作系统（Windows 或 Linux），并确保部署的主目标服务器具有相同类型的操作系统。 复制组中的所有 Vm 都必须使用相同的主目标服务器。
5. 打开故障回复[所需的端口](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)。
6. 确保在故障回复之前已连接 vCenter Server。 否则，断开磁盘连接并将其附加回到虚拟机的操作会失败。
7. 如果 vCenter 服务器管理要故障回复到的 Vm，请确保具有所需的权限。 如果执行只读的用户 vCenter 发现并保护虚拟机，保护会成功且故障转移可正常工作。 但是，在重新保护期间，故障转移会失败，因为无法发现数据存储，也不会在重新保护期间列出。 若要解决此问题，可以使用[适当的帐户/权限](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)更新 vCenter 凭据，然后重试该作业。 
8. 如果你使用了模板来创建虚拟机，请确保每个 VM 都有自己的磁盘 UUID。 如果本地 VM UUID 与主目标服务器的 UUID 冲突，因为两者都是基于同一模板创建的，则重新保护会失败。 从其他模板部署。
9. 如果要故障回复到备用 vCenter Server，请确保已发现新的 vCenter Server 和主目标服务器。 通常，如果这些数据存储不可访问，或在重新保护中不**可见。**
10. 验证无法进行故障回复的下列情况：
    - 如果使用的是 ESXi 5.5 免费版或 vSphere 6 虚拟机监控程序免费版。 升级到其他版本。
    - 如果你有 Windows Server 2008 R2 SP1 物理服务器。
    - VMware Vm 无法故障回复到 Hyper-v。
    - 已[迁移](migrate-overview.md#what-do-we-mean-by-migration)的 vm。
    - 已移动到另一个资源组的 VM。
    - 已删除的副本 Azure VM。
    - 未受保护（复制到本地站点）的副本 Azure VM。
10. 查看可使用的[故障回复类型](concepts-types-of-failback.md)-原始位置恢复和备用位置恢复。


## <a name="enable-reprotection"></a>启用重新保护

启用复制。 你可以重新保护特定 Vm 或恢复计划：

- 如果重新保护恢复计划，则必须提供每个受保护计算机的值。
- 如果 Vm 属于多 VM 一致性的复制组，则只能使用恢复计划对其进行重新保护。 复制组中的 Vm 必须使用相同的主目标服务器

### <a name="before-you-start"></a>开始之前

- VM 在故障转移后在 Azure 中启动后，将需要一段时间才能将代理注册回配置服务器（最多15分钟）。 在此期间，将无法进行重新保护并会返回一条错误消息，指出未安装代理。 如果发生这种情况，请等待几分钟，然后重新保护。
- 如果要将 Azure VM 故障回复到现有的本地 VM，请在主目标服务器的 ESXi 主机上装载具有读/写访问权限的本地 VM 数据存储。
- 如果要故障回复到备用位置（例如，如果本地 VM 不存在），请选择针对主目标服务器配置的保留驱动器和数据存储。 故障回复到本地站点时，故障回复保护计划中的 VMware 虚拟机将使用与主目标服务器相同的数据存储。 然后，将在 vCenter 中创建新 VM。

启用重新保护，如下所示：

1. 选择“保管库” > “复制的项”。 右键单击已故障转移的虚拟机，然后选择“重新保护”。 也可以从命令按钮中选择该计算机，然后选择“重新保护”。
2. 验证是否选择了“从 Azure 到本地”保护方向。
3. 在“主目标服务器”和“进程服务器”中，选择本地主目标服务器和进程服务器。  
4. 对于“数据存储”，选择要将本地磁盘恢复到的数据存储。 删除本地虚拟机后，如果需要创建新磁盘，可使用此选项。 如果磁盘已存在，则会忽略此选项。 仍需指定一个值。
5. 选择保留驱动器。
6. 将自动选择故障回复策略。
7. 选择“确定”开始重新保护。

    ![“重新保护”对话框](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 作业开始将 Azure VM 复制到本地站点。 可以在“**作业**”选项卡上跟踪进度。
    - 重新保护成功后，VM 将进入受保护状态。
    - 重新保护期间，本地 VM 将关闭。 这有助于确保复制期间的数据一致性。
    - 重新保护完成后，请勿打开本地 VM。
   

## <a name="next-steps"></a>后续步骤

- 如果遇到任何问题，请查看[故障排除一文](vmware-azure-troubleshoot-failback-reprotect.md)。
- 保护 Azure Vm 后，可以[运行故障回复](vmware-azure-failback.md)。 故障回复将关闭 Azure VM 并启动本地 VM。 应用程序需要停机一段时间，并相应地选择故障回复时间。


