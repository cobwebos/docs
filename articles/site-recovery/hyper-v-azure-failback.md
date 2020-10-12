---
title: 使用 Azure Site Recovery 从 Azure 故障回复 Hyper-V VM
description: 如何使用 Azure Site Recovery 从 Azure 将 Hyper-V VM 故障回复到本地站点。
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: a31a28728dd0521262bd0518cc49a385f4314302
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87416224"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>为 Hyper-V VM 运行故障回复

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 对 Hyper-V VM 从本地站点故障转移到 Azure 后创建的 Azure VM 进行故障回复。

- 通过运行从 Azure 到本地站点的计划故障转移，可从 Azure 故障回复 Hyper-V VM。 如果故障转移方向是从 Azure 到本地，则将其视为故障回复。
- 由于 Azure 是高可用性环境并且 VM 始终可用，从 Azure 进行故障回复是计划活动。 可计划短暂的停机时间，以便工作负载可以重新开始在本地运行。 
- 计划故障回复将关闭 Azure 中的 VM，并下载最新更改。 预计不会丢失任何数据。

## <a name="before-you-start"></a>开始之前

1. [查看可以使用的故障回复的类型](failover-failback-overview.md#hyper-v-reprotectionfailback) - 原始位置恢复和备用位置恢复。
2. 确保 Azure VM 使用存储帐户而非托管磁盘。 不支持使用托管磁盘故障回复复制的 Hyper-V VM。
3. 检查并确保本地 Hyper-V 主机（如果与 Site Recovery 配合使用，则为 System Center VMM 服务器）正在运行并已连接到 Azure。 
4. 确保 VM 的故障转移和提交已完成。 无需设置任何特定的 Site Recovery 组件即可从 Azure 故障回复 Hyper-V VM。
5. 完成数据同步和启动本地 VM 所需的时间将取决于多个因素。 若要加快数据下载速度，可将 Microsoft 恢复服务代理配置为使用多个线程并行下载。 [了解详细信息](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)。


## <a name="fail-back-to-the-original-location"></a>故障回复到原始位置

若要将 Azure 中的 Hyper-V VM 故障回复到原始本地 VM，请运行从 Azure 到本地站点的计划故障转移，如下所示：

1. 在保管库中 >“复制的项”，选择 VM  。 右键单击 VM >“计划的故障转移”  。 如果要故障回复恢复计划，请选择计划名称，然后单击“故障转移” > “计划的故障转移”   。
2. 在“确认计划的故障转移”中，选择源位置和目标位置  。 请注意故障转移方向。 如果从主要位置故障转移已按预期完成，并且所有虚拟机都位于次要位置，则本部分仅供参考。
3. 在“数据同步”中，选择一个选项  ：
    - **在故障转移之前同步数据(仅同步增量更改)** - 此选项可最大程度地减少 VM 的停机时间，因为它可在不关闭 VM 的情况下执行同步。
        - **阶段 1**：生成 Azure VM 快照，并将快照复制到本地 Hyper-V 主机。 计算机将继续在 Azure 中运行。
        - **阶段 2**：关闭 Azure VM，确保其不会发生任何新的更改。 将最终的增量更改集传输到本地服务器，并启动本地 VM。
    - **仅在故障转移期间同步数据(完整下载)** - 此选项速度更快，因为我们假定磁盘的大部分内容已更改，并且不需要费时计算校验和。 此选项不会执行任何校验和计算。
        - 此选项会执行磁盘的下载。 
        - 如果已运行 Azure 一段时间（一个月或以上）或已删除本地 VM，我们建议使用此选项。

4. （仅限 VMM）如果为云启用了数据加密，请在“加密密钥”中选择你在 VMM 服务器上安装提供程序期间启用数据加密时颁发的证书  。
5. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
6. 如果选择了在故障转移之前同步数据的选项，请在完成初始数据同步并且已准备好在 Azure 中关闭虚拟机后，单击“作业”> 作业名称 >“完成故障转移”   。 这样会执行以下操作：
    - 关闭 Azure 计算机。
    - 将最新的更改传输到本地 VM。
    - 启动本地 VM。
7. 现在，你可登录到本地 VM 计算机，检查它是否按预期方式提供。
8. 虚拟机处于待提交状态。 单击“**提交**”以提交故障转移。
9. 若要完成故障回复，请单击“反向复制”，重新将本地 VM 复制到 Azure  。



## <a name="fail-back-to-an-alternate-location"></a>故障回复到备用位置 

故障回复到备用位置，如下所示：

1. 如果要设置新硬件，请在计算机上安装[支持的 Windows 版本](hyper-v-azure-support-matrix.md#replicated-vms)和 Hyper-V 角色。
2. 创建与原始服务器上的名称相同的虚拟网络交换机。
3. 在“受保护的项” > “保护组” > \<ProtectionGroupName> -> \<VirtualMachineName> 中，选择要故障恢复的 VM，然后选择“计划的故障转移”  。
4. 在“确认计划的故障转移”中，选择“如果本地虚拟机不存在，则创建它”   。
5. 在“主机名”中，选择要在其上放置 VM 的新 Hyper-V 主机服务器  。
6. 在“数据同步”中，建议选择“在故障转移之前同步数据”这一选项  。 此选项可以最大程度地减少 VM 的停机时间，因为它可以在不关闭 VM 的情况下执行同步。 此选项执行以下操作：
    - **阶段 1**：生成 Azure VM 快照，并将快照复制到本地 Hyper-V 主机。 计算机将继续在 Azure 中运行。
    - **阶段 2**：关闭 Azure VM，确保其不会发生任何新的更改。 最终的更改集将传输到本地服务器，本地虚拟机会启动。
    
7. 单击复选标记开始故障转移（故障回复）。
8. 完成初始同步并准备好关闭 Azure VM 后，请单击“作业” > \<planned failover job> > “完成故障转移” 。 这将关闭 Azure 计算机，将最新更改传输到本地 VM，并启动 VM。
9. 可登录到本地 VM，验证一切是否按预期工作。
10. 单击“提交”完成故障转移  。 提交会删除 Azure VM 及其磁盘，并准备要重新保护的本地 VM。
10. 单击“反向复制”，开始将本地 VM 复制到 Azure  。 仅复制在 Azure 中关闭 VM 以来发生的增量更改。

    > [!NOTE]
    > 如果在数据同步期间中取消故障回复作业，则本地 VM 会处于损坏状态。 这是因为数据同步将 Azure VM 磁盘中的最新数据复制到本地数据磁盘上，在同步完成之前，磁盘数据可能不处于一致状态。 如果在取消数据同步后启动本地 VM，则 VM 可能会无法启动。 在这种情况下，请重新运行故障转移以完成数据同步。


## <a name="next-steps"></a>后续步骤
将本地 VM 复制到 Azure 后，可根据需要[运行其他到 Azure 的故障转移](site-recovery-failover.md)。
