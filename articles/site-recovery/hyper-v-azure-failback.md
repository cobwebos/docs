---
title: 利用 Azure Site Recovery 故障回复 Azure 中的 Hyper-v Vm
description: 如何使用 Azure Site Recovery 将 Hyper-v Vm 从 Azure 故障回复到本地站点。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281777"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>为 Hyper-V VM 运行故障回复

本文介绍如何在[Azure Site Recovery](site-recovery-overview.md)的情况下，对从本地站点到 Azure 的 hyper-v vm 故障转移后创建的 azure vm 进行故障回复。

- 通过运行从 Azure 到本地站点的计划的故障转移，从 Azure 对 Hyper-v Vm 进行故障回复。 如果故障转移方向从 Azure 到本地，则会将其视为故障回复。
- 由于 Azure 是高度可用的环境，并且 Vm 始终可用，从 Azure 进行故障回复是一种计划的活动。 你可以计划一小段停机时间，使工作负荷可以在本地运行。 
- 计划的故障回复将关闭 Azure 中的 Vm，并下载最新的更改。 不需要数据丢失。

## <a name="before-you-start"></a>开始之前

1. 查看可使用的[故障回复类型](failover-failback-overview.md#hyper-v-reprotectionfailback)-原始位置恢复和备用位置恢复。
2. 确保 Azure Vm 正在使用存储帐户而非托管磁盘。 不支持使用托管磁盘复制的 Hyper-v Vm 的故障回复。
3. 检查本地 Hyper-v 主机（或 System Center VMM 服务器（如果使用的是带 Site Recovery）是否正在运行并连接到 Azure。 
4. 请确保 Vm 的故障转移和提交已完成。 不需要设置任何特定的 Site Recovery 组件，就可以从 Azure 故障回复 Hyper-v Vm。
5. 完成数据同步并启动本地 VM 所需的时间取决于许多因素。 若要加快数据下载速度，可将 Microsoft 恢复服务代理配置为使用多个线程并行下载。 [了解详细信息](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)。


## <a name="fail-back-to-the-original-location"></a>故障回复到原始位置

若要将 Azure 中的 Hyper-v Vm 故障转移到原始本地 VM，请按如下方式运行从 Azure 到本地站点的计划的故障转移：

1. 在保管库中 > "**复制的项**"，选择 VM。 右键单击 VM > 计划的**故障转移**。 如果要对恢复计划进行故障回复，请选择计划名称，然后单击 "**故障转移** > **计划的故障转移**"。
2. 在 "**确认计划的故障转移**" 中，选择源和目标位置。 请注意故障转移方向。 如果主数据库的故障转移按预期方式工作，并且所有虚拟机都位于辅助位置，则此项仅用于信息。
3. 在 "**数据同步**" 中，选择一个选项：
    - **在故障转移前同步数据（仅同步增量更改）** -此选项可最大程度地减少 vm 在同步时不会关闭 vm 的停机时间。
        - **阶段 1**：拍摄 Azure VM 的快照，并将其复制到本地 hyper-v 主机。 计算机将继续在 Azure 中运行。
        - **阶段 2**：关闭 Azure VM，以便不会发生新的更改。 最终的增量更改集将传输到本地服务器，并启动本地 VM。
    - **仅在故障转移期间同步数据（完整下载）** -此选项速度更快，因为我们假定磁盘的大部分已更改，并且不需要花费时间来计算校验和。 此选项不会执行任何校验和计算。
        - 此选项会执行磁盘的下载。 
        - 如果已运行 Azure 一段时间（一个月或以上）或已删除本地 VM，我们建议使用此选项。

4. 对于 VMM，如果为云启用了数据加密，请在 "**加密密钥**" 中，选择在 VMM 服务器上安装提供程序期间启用数据加密时颁发的证书。
5. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
6. 如果选择了在故障转移之前同步数据的选项，则在完成初始数据同步并且已准备好在 Azure 中关闭虚拟机后，请单击 "**作业**" > 作业名称 ">"**完成故障转移**"。 这样会执行以下操作：
    - 关闭 Azure 计算机。
    - 将最新更改传输到本地 VM。
    - 启动本地 VM。
7. 你现在可以登录到本地 VM 计算机，以检查它是否按预期方式提供。
8. 虚拟机处于待提交状态。 单击“提交”提交故障转移。
9. 若要完成故障回复，请单击 "**反向复制**" 以开始再次将本地 VM 复制到 Azure。



## <a name="fail-back-to-an-alternate-location"></a>故障回复到备用位置 

故障回复到备用位置，如下所示：

1. 如果要设置新硬件，请在计算机上安装[受支持的 Windows 版本](hyper-v-azure-support-matrix.md#replicated-vms)和 hyper-v 角色。
2. 创建与原始服务器上的名称相同的虚拟网络交换机。
3. 在 "**受保护的项** > **保护组** > \<ProtectionGroupName >-> \<VirtualMachineName > 中，选择要故障回复的 VM，然后选择"**计划的故障转移**"。
4. 在 "**确认计划的故障转移**" 中，选择 **"创建本地虚拟机" （如果不存在**）。
5. 在 "**主机名**" 中，选择要在其上放置虚拟机的新 hyper-v 主机服务器。
6. 在 "**数据同步**" 中，我们建议选择用于在故障转移之前同步数据的选项。 这可最大程度地减少 Vm 在同步时不会关闭虚拟机的停机时间。 它执行以下操作：
    - **阶段 1**：获取 Azure VM 的快照，并将其复制到本地 hyper-v 主机。 计算机将继续在 Azure 中运行。
    - **阶段 2**：关闭 Azure VM，以便不会发生新的更改。 最终的更改集将传输到本地服务器，本地虚拟机会启动。
    
7. 单击复选标记开始故障转移（故障回复）。
8. 初始同步完成并且准备好关闭 Azure VM 后，请单击 "**作业**" > \<计划的故障转移作业 > > "**完成故障转移**"。 这会关闭 Azure 计算机，将最新更改传输到本地 VM，并启动该更改。
9. 可以登录到本地 VM，验证一切是否按预期工作。
10. 单击 "**提交**" 完成故障转移。 Commit 将删除 Azure VM 及其磁盘，并准备要再次保护的本地 VM。
10. 单击 "**反向复制**" 开始将本地 VM 复制到 Azure。 仅复制在 Azure 中关闭 VM 以来发生的增量更改。

    > [!NOTE]
    > 如果在数据同步过程中取消故障回复作业，则本地 VM 将处于损坏状态。 这是因为数据同步将 Azure VM 磁盘中的最新数据复制到本地数据磁盘，在同步完成之前，磁盘数据可能不是一致的状态。 如果在取消数据同步后启动本地 VM，则可能无法启动。 在这种情况下，请重新运行故障转移以完成数据同步。


## <a name="next-steps"></a>后续步骤
将本地 VM 复制到 Azure 之后，可以根据需要[运行另一个](site-recovery-failover.md)到 azure 的故障转移。
