---
title: 使用 Azure Site Recovery 将 Azure VM 重新保护到 Azure VMware 解决方案私有云
description: 了解如何使用 Azure Site Recovery 在故障转移到 Azure 后重新保护 Azure VMware 解决方案 VM。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814180"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>从 Azure 重新保护到 Azure VMware 解决方案私有云

将 Azure VMware 解决方案 VM [故障转移](avs-tutorial-failover.md)到 Azure 后，故障回复到 Azure VMware 解决方案私有云的第一步是重新保护故障转移期间创建的 Azure VM。 本文介绍如何执行此操作。 

## <a name="before-you-begin"></a>开始之前

1. 按照[本文](vmware-azure-prepare-failback.md)中的步骤准备重新保护和故障回复，包括在 Azure 中设置进程服务器，并设置 Azure VMware 解决方案私有云主目标服务器，以及配置站点到站点 VPN 或 ExpressRoute 专用对等互连，以进行故障回复。
2. 确保 Azure VMware 解决方案私有云配置服务器正在运行并已连接到 Azure。 故障回复期间，VM 必须位于配置服务器数据库中。 否则，故障回复不会成功。
3. 删除 Azure VMware 解决方案私有云主目标服务器上的所有快照。 如果存在快照，则重新保护将不起作用。  在执行重新保护作业期间，VM 上的快照会自动合并。
4. 如果你正在重新保护为实现多虚拟机一致性而集中到一个复制组中的虚拟机，请确保它们都具有相同的操作系统（Windows 或 Linux），并确保部署的主目标服务器具有相同类型的操作系统。 复制组中的所有 VM 都必须使用相同的主目标服务器。
5. 打开[故障回复所需的端口](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback)以进行故障回复。
6. 确保在故障回复之前已连接 vCenter Server。 否则，断开磁盘连接并将其附加回到虚拟机的操作会失败。
7. 如果使用 vCenter Server 管理要对其进行故障回复的 VM，请确保你具有所需的权限。 如果执行只读的用户 vCenter 发现并保护虚拟机，保护会成功且故障转移可正常工作。 但是，在重新保护期间，故障转移会失败，因为无法发现数据存储，并且在重新保护期间没有列出它们。 若要解决此问题，可以使用[相应的帐户/权限](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery)更新 vCenter 凭据，然后重试作业。 
8. 如果使用模板创建虚拟机，请确保每个 VM 都有自己的磁盘 UUID。 如果 Azure VMware 解决方案 VM UUID 与主目标服务器的 UUID 冲突（因为两者都是基于同一模板创建的），重新保护会失败。 从其他模板部署。
9. 如果要故障回复到备用 vCenter Server，请确保已发现新的 vCenter Server 和主目标服务器。 通常情况下，如果这些数据存储不可访问，或在“重新保护”中不可见  。
10. 请验证无法进行故障回复的下列情况：
    - 是否使用的是 ESXi 5.5 免费版或 vSphere 6 虚拟机监控程序免费版。 升级到其他版本。
    - 你是否有 Windows Server 2008 R2 SP1 物理服务器。
    - VMware VM 无法故障回复到 Hyper-V。
    - 已迁移的 VM。
    - 已移动到另一个资源组的 VM。
    - 已删除的副本 Azure VM。
    - 未受保护的副本 Azure VM。
10. [查看可以使用的故障回复的类型](concepts-types-of-failback.md) - 原始位置恢复和备用位置恢复。


## <a name="enable-reprotection"></a>启用重新保护

启用复制。 你可以重新保护特定 VM 或恢复计划：

- 重新保护恢复计划时，必须为每台受保护的计算机提供值。
- 如果 VM 属于多虚拟机一致性的复制组，则只能使用恢复计划对其进行重新保护。 复制组中的 VM 必须使用相同的主目标服务器

>[!NOTE]
>在重新保护期间从 Azure 发送到 erstwhile 源的数据量可以是 0 字节到所有受保护计算机的磁盘大小总和之间的任何值，并且无法计算。

### <a name="before-you-start"></a>开始之前

- 故障转移后，在 Azure 中启动 VM 后，会留出一段时间让代理重新注册到配置服务器（最多 15 分钟）。 在此期间，将无法进行重新保护并会返回一条错误消息，指出未安装代理。 如果发生这种情况，请等待几分钟，然后重新保护。
- 如果要将 Azure VM 故障回复到现有 Azure VMware 解决方案 VM，请将具有读/写访问权限的 VM 数据存储装载到主目标服务器的 ESXi 主机上。
- 如果要故障回复到备用位置（例如 Azure VMware 解决方案 VM 不存在），请选择针对主目标服务器配置的保留驱动器和数据存储。 故障回复到 Azure VMware 解决方案私有云时，故障回复保护计划中的虚拟机将使用与主目标服务器相同的数据存储。 然后，将在 vCenter 中创建新 VM。

按如下所述启用重新保护：

1. 选择“保管库” > “复制的项”。 右键单击已故障转移的虚拟机，然后选择“重新保护”。**** 也可以从命令按钮中选择该计算机，然后选择“重新保护”。****
2. 验证是否选择了“从 Azure 到本地”保护方向。****
3. 在“主目标服务器”和“进程服务器”中，选择本地主目标服务器和进程服务器。********  
4. 对于“数据存储”，选择要将 Azure VMware 解决方案中的磁盘恢复到的数据存储。 删除 Azure VMware 解决方案 VM 后，如果需要创建新磁盘，可使用此选项。 如果磁盘已存在，则会忽略此选项。 仍需指定一个值。
5. 选择保留驱动器。
6. 将自动选择故障回复策略。
7. 选择“确定”开始重新保护。****

    ![“重新保护”对话框](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. 作业开始将 Azure VM 复制到 Azure VMware 解决方案私有云。 可以在“**作业**”选项卡上跟踪进度。
    - 重新保护成功后，VM 进入受保护状态。
    - Azure VMware 解决方案 VM 在重新保护过程中处于关闭状态。 这有助于确保复制期间的数据一致性。
    - 重新保护完成后，请勿打开 Azure VMware 解决方案 VM。
   

## <a name="next-steps"></a>后续步骤

- 如果遇到任何问题，请查看[故障排除](vmware-azure-troubleshoot-failback-reprotect.md)一文。
- 保护 Azure VM 后，可以[运行故障回复](avs-tutorial-failback.md)。 故障回复会关闭 Azure VM 并启动 Azure VMware 解决方案 VM。 应用程序需要停机一段时间，请相应地选择故障回复时间。


