---
title: 为物理服务器设置故障转移和故障回复 Site Recovery
description: 了解如何使用 Azure Site Recovery 将物理服务器故障转移到 Azure 以及故障回复到本地站点以进行灾难恢复
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497856"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>对复制到 Azure 的物理服务器进行故障转移和故障回复

本教程介绍如何通过[Azure Site Recovery](site-recovery-overview.md)将复制到 Azure 的本地物理服务器故障转移到 Azure。 故障转移后，可以从 Azure 故障回复到本地站点（如果可用）。

## <a name="before-you-start"></a>开始之前

- [了解](failover-failback-overview.md)灾难恢复中的故障转移过程。
- 如果要对多台计算机进行故障转移，请[了解](recovery-plan-overview.md)如何在恢复计划中同时收集计算机。
- 在执行完全故障转移之前，请运行[灾难恢复演练](site-recovery-test-failover-to-azure.md)，以确保一切按预期运行。
- 按照[这些说明](site-recovery-failover.md#prepare-to-connect-after-failover)进行准备，以便在故障转移后连接到 Azure vm。



## <a name="run-a-failover"></a>运行故障转移

### <a name="verify-server-properties"></a>验证服务器属性

验证服务器属性，确保其符合 Azure VM 的 [Azure 要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 在“受保护的项”中，单击“复制的项”，并选择计算机。
2. “复制的项”窗格中具有计算机信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可以修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管磁盘设置
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到有关计算机操作系统和数据磁盘的信息。

### <a name="fail-over-to-azure"></a>故障转移到 Azure

1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
   - **最新**：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
   - **最新处理**：此选项将计算机故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
   - **最新的应用一致**：此选项将计算机故障转移到由 Site Recovery 处理的最新应用一致恢复点。
   - 自定义：指定恢复点。

3. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源计算机，请选择“在开始故障转移前关闭计算机”。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 如果已准备好连接到 Azure VM，请进行连接，以在故障转移后对其进行验证。
5. 验证后，提交故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> 不会取消正在进行的故障转移。 在故障转移开始之前，将停止计算机复制。 如果取消故障转移，它会停止，但计算机不会再次复制。
> 对于物理服务器，其他故障转移处理可能需要大约八到十分钟时间才能完成。

## <a name="automate-actions-during-failover"></a>在故障转移过程中自动执行操作

你可能想要在故障转移过程中自动执行操作。 为此，可以在恢复计划中使用脚本或 Azure 自动化 runbook。

- [了解](site-recovery-create-recovery-plans.md)如何创建和自定义恢复计划，包括添加脚本。
- [了解](site-recovery-runbook-automation.md)如何将 Azure 自动化 runbook 添加到恢复计划。

## <a name="configure-settings-after-failover"></a>故障转移后配置设置

故障转移后，需要[配置 azure 设置](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover)以连接到复制的 Azure vm。 此外，请设置[内部和公共](site-recovery-failover.md#set-up-ip-addressing)IP 地址。

## <a name="prepare-for-reprotection-and-failback"></a>准备重新保护和故障回复

故障转移到 Azure 后，可以通过将 Azure Vm 复制到本地站点来对其进行重新保护。 然后，在复制后，可以通过运行从 Azure 到本地站点的故障转移，将其故障回复到本地站点。

1. 使用 Site Recovery 复制到 Azure 的物理服务器只能作为 VMware VM 故障回复。 必须有 VMware 基础结构，才能进行故障回复。 请按照[本文](vmware-azure-prepare-failback.md)中的步骤准备重新保护和故障回复，包括在 Azure 中设置进程服务器，以及在本地主目标服务器中配置站点到站点 VPN 或 ExpressRoute 专用对等互连。
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
    - 已[迁移](migrate-overview.md#what-do-we-mean-by-migration)的 vm。
    - 已移动到另一个资源组的 VM。
    - 已删除的副本 Azure VM。
    - 未受保护（复制到本地站点）的副本 Azure VM。
10. 查看可使用的[故障回复类型](concepts-types-of-failback.md)-原始位置恢复和备用位置恢复。


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>将 Azure Vm 重新保护到备用位置

此过程假定本地 VM 不可用。

1. 在保管库 >**设置** > **复制的项**，右键单击已故障转移 >**重新保护**的计算机。
2. 在“重新保护”中，确保选择“Azure 到本地”。
3. 指定本地主目标服务器和进程服务器。
4. 在“数据存储”中，选择要将本地磁盘恢复到的主目标数据存储。
       - 如果已删除或不存在本地 VM，并且需要创建新磁盘，请使用此选项。
       - 如果磁盘已存在，但确实需要指定值，则将忽略此设置。
5. 选择主目标保留驱动器。 将自动选择故障回复策略。
6. 单击“确定”开始重新保护。 作业开始将 Azure VM 复制到本地站点。 可以在“**作业**”选项卡上跟踪进度。

> [!NOTE]
> 如果要将 Azure VM 恢复到现有本地 VM，请使用读/写访问权限将本地虚拟机的数据存储装载在主目标服务器的 ESXi 主机上。


## <a name="fail-back-from-azure"></a>从 Azure 进行故障回复

运行故障转移，如下所示：

1. 在“复制的项”页中右键单击该计算机，然后单击“非计划的故障转移”。
2. 在“确认故障转移”中，验证故障转移方向为从 Azure 转移。
3. 选择要用于故障转移的恢复点。
    - 建议使用**最新**的恢复点。 应用一致性点位于最新的时间点之后，导致某些数据丢失。
    - **最新版本**为崩溃一致的恢复点。
    - 故障转移运行时，Site Recovery 会关闭 Azure VM，并启动本地 VM。 这会导致出现停机时间，因此请选择适当的时间。
4. 右键单击该计算机，然后单击“提交”。 由此触发的作业会删除 Azure VM。
5. 验证 Azure VM 已按预期情况关闭。


## <a name="reprotect-on-premises-machines-to-azure"></a>将本地计算机重新保护到 Azure

数据现应返回到本地站点，但不会复制到 Azure。 可按如下操作开始再次复制到 Azure：

1. 在保管库 >**设置**">**复制的项**"，选择已故障回复的故障回复 vm，并单击 "**重新保护**"。
2. 选择用于将复制数据发送到 Azure 的进程服务器，然后单击“确定”。


## <a name="next-steps"></a>后续步骤

重新保护作业完成后，会将本地 VM 复制到 Azure。 根据需要，可以[运行另一个故障转移](site-recovery-failover.md)到 Azure。
