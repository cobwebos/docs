---
title: 关于 Azure Site Recovery 中的故障转移和故障回复
description: 了解 Azure Site Recovery 中的故障转移和 failable。
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 3c461d2de4f9ef8e8159c7b9c86f23a846421c5e
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498275"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>关于本地灾难恢复故障转移/故障回复

本文概述了如何使用[Azure Site Recovery](site-recovery-overview.md)在本地计算机到 Azure 的灾难恢复期间进行故障转移和故障回复。

## <a name="recovery-stages"></a>恢复阶段

Site Recovery 中的故障转移和故障回复具有四个阶段：

- **阶段1：从本地故障转移**：在为本地计算机设置到 Azure 的复制后，当本地站点出现故障时，这些计算机将故障转移到 azure。 故障转移后，将会基于复制的数据创建 Azure VM。
- **阶段2：** 在 azure 中重新保护 azure vm：在 azure 中重新保护 azure vm，使之开始复制回本地站点。 在重新保护期间，本地 VM （如果可用）会关闭，以帮助确保数据的一致性。
- **阶段3：从 Azure 进行故障转移**：如果本地站点再次运行正常，则运行其他故障转移，这一次是将 Azure vm 故障回复到本地站点。 你可以故障回复到从中进行故障转移的原始位置，或故障回复到备用位置。
- **阶段4：重新保护本地计算机**：故障回复后，再次启用将本地计算机复制到 Azure。

## <a name="failover"></a>故障转移：

作为业务连续性和灾难恢复（BCDR）策略的一部分执行故障转移。

- 作为 BCDR 策略的第一步，你将在本地将本地计算机复制到 Azure。 用户访问本地源计算机上运行的工作负荷和应用。
- 如果需要，例如，如果本地中断，则会将计算机故障转移到 Azure。 使用复制的数据创建 Azure Vm。
- 为实现业务连续性，用户可以继续访问 Azure Vm 上的应用。

故障转移是一个两阶段的活动：

- **故障转移**：使用所选恢复点创建并显示 Azure VM 的故障转移。
- **提交**：在故障转移后，请在 Azure 中验证 VM：
    - 然后，可以将故障转移提交到选定的恢复点，或选择其他提交点。
    - 提交故障转移后，无法更改恢复点。
    
    
## <a name="connect-to-azure-after-failover"></a>故障转移后连接到 Azure

若要连接到使用 RDP/SSH 在故障转移后创建的 Azure Vm，有许多要求。

**故障转移** | **位置** | **操作**
--- | --- | ---
**Azure VM （Windows （** | 故障转移之前在本地计算机上 | **通过 Internet 访问**：启用 RDP。 确保为 "**公共**" 添加 TCP 和 UDP 规则，并且**Windows 防火墙**中的所有配置文件都允许使用 RDP > **允许的应用**。<br/><br/> **通过站点到站点 VPN 访问**：在计算机上启用 RDP。 检查 " **Windows 防火墙** -> **允许的应用和功能**" 中的 RDP 是否适用于**域和专用**网络。<br/><br/>  确保操作系统 SAN 策略已设置为 **OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。<br/><br/> 在触发故障转移时，请确保 VM 上没有处于挂起状态的 Windows 更新。 当你进行故障转移时，Windows 更新可能会启动，在更新完成之前，你将无法登录到 VM。
**运行 Windows 的 Azure VM** | 故障转移后在 Azure VM 上 |  为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 故障转移的 VM （及其连接到的 Azure 子网）上的网络安全组规则必须允许与 RDP 端口建立传入连接。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看[故障排除提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**运行 Linux 的 Azure VM** | 故障转移之前在本地计算机上 | 确保 VM 上的安全外壳服务已设置为在系统引导时自动启动。<br/><br/> 确保防火墙规则允许 SSH 连接。
**运行 Linux 的 Azure VM** | 故障转移后在 Azure VM 上 | 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 SSH 端口建立传入连接。<br/><br/> 为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。<br/><br/>

## <a name="types-of-failover"></a>故障转移类型

Site Recovery 提供不同的故障转移选项。

**故障转移** | **详细信息** | **恢复** | **Workflow**
--- | --- | --- | ---
**测试故障转移** | 用于运行验证 BCDR 策略的钻取，而不会丢失任何数据或停机时间。| 在 Azure 中创建 VM 的副本，而不会影响正在进行的复制或生产环境。 | 1. 在单个 VM 上或在恢复计划中的多个 Vm 上运行测试故障转移。<br/><br/> 2. 选择要用于测试故障转移的恢复点。<br/><br/> 3. 选择 azure VM 在故障转移后创建时将位于其中的 azure 网络。 网络仅用于测试故障转移。<br/><br/> 4. 验证钻取是否按预期方式工作。 Site Recovery 在钻取期间自动清理在 Azure 中创建的 Vm。
**计划的故障转移-Hyper-v**  | 通常用于计划的停机时间。<br/><br/> 源 Vm 已关闭。 在启动故障转移之前，将同步最新的数据。 | 计划的工作流的零数据丢失。 | 1. 计划停机维护时段并通知用户。<br/><br/> 2. 使面向用户的应用脱机。<br/><br/> 3. 使用最新的恢复点启动计划的故障转移。 如果计算机未关闭或遇到错误，则故障转移不会运行。<br/><br/> 4. 在故障转移后，请检查副本 Azure VM 在 Azure 中是否处于活动状态。<br/><br/> 5. 提交故障转移以完成。 提交操作将删除所有恢复点。
**故障转移-Hyper-v** | 通常在出现计划外中断或主站点不可用的情况下运行。<br/><br/> 可以选择关闭 VM，并在启动故障转移之前同步最终更改。  | 应用的最小数据丢失。 | 1. 启动你的 BCDR 计划。 <br/><br/> 2. 启动故障转移。 指定 Site Recovery 应关闭 VM，并在触发故障转移之前同步/复制最新更改。<br/><br/> 3. 你可以故障转移到多个恢复点选项，如下表中所示。<br/><br/> 如果未启用关闭 VM 的选项，或者如果 Site Recovery 无法关闭 VM，则使用最新的恢复点。<br/>即使无法关闭计算机，也会运行故障转移。<br/><br/> 4. 在故障转移后，请检查副本 Azure VM 在 Azure 中是否处于活动状态。<br/> 如果需要，你可以从保留时段选择不同的恢复点，时间为24小时。<br/><br/> 5. 提交故障转移以完成。 提交操作将删除所有可用的恢复点。
**故障转移-VMware** | 通常在出现计划外中断或主站点不可用的情况下运行。<br/><br/> （可选）指定 Site Recovery 应尝试触发 VM 关闭，并在启动故障转移之前同步和复制最终更改。  | 应用的最小数据丢失。 | 1. 启动你的 BCDR 计划。 <br/><br/> 2. 从 Site Recovery 启动故障转移。 指定 Site Recovery 是否应在运行故障转移之前尝试触发 VM 关闭并进行同步。<br/> 即使无法关闭计算机，也会运行故障转移。<br/><br/> 3. 在故障转移后，请检查副本 Azure VM 在 Azure 中是否处于活动状态。 <br/>如果需要，可以从72小时的保留期窗口中选择其他恢复点。<br/><br/> 5. 提交故障转移以完成。 提交操作将删除所有恢复点。<br/> 对于 Windows Vm，Site Recovery 在故障转移过程中禁用 VMware 工具。 

## <a name="failover-processing"></a>故障转移处理

在某些情况下，故障转移需要大约 8 到 10 分钟的时间完成其他进程。 对于以下情况，你可能会发现测试故障转移会持续较长时间：

* VMware VM 运行的移动服务版本低于 9.8。
* 物理服务器。
* VMware Linux VM。
* Hyper-V VM 作为物理服务器受到保护。
* VMware VM 未启用 DHCP 服务。
* VMware VM 不包含以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi。

## <a name="recovery-point-options"></a>恢复点选项

在故障转移期间，可以选择多个恢复点选项。

**选项** | **详细信息**
--- | ---
**最新（最低 RPO）** | 此选项提供最低恢复点目标（RPO）。 它首先处理已发送到 Site Recovery 服务的所有数据，以便为每个 VM 创建恢复点，然后再将其故障转移到该恢复点。 触发故障转移时，此恢复点会将所有数据复制到 Site Recovery。
**最新处理**  | 此选项将 Vm 故障转移到 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
**最新的应用一致** |  如果启用了应用一致的恢复点，则此选项会将 Vm 故障转移到 Site Recovery 处理的最新应用程序一致恢复点。 检查 VM 设置中的最新恢复点。
**已处理的最新多 VM** | 此选项适用于包含一台或多台已启用多 VM 一致性的 VM 的恢复计划。 已启用该设置的 VM 会故障转移到最新的常用多 VM 一致恢复点。 计划中的任何其他 Vm 都将故障转移到最新的已处理恢复点。
**最新的多 VM 应用一致** |  此选项适用于包含一台或多台已启用多 VM 一致性的 VM 的恢复计划。 属于复制组的 VM 会故障转移到最新的常用多 VM 应用程序一致恢复点。 其他 VM 故障转移到其最新的应用程序一致恢复点。
**自定义** | 使用此选项可将特定 VM 故障转移到特定的恢复点。 此选项不适用于恢复计划。


## <a name="reprotectionfailback"></a>重新保护/故障回复

故障转移到 Azure 后，复制的 Azure Vm 处于不受保护的状态。

- 作为故障回复到本地站点的第一步，需要启动将 Azure Vm 复制到本地站点。 重新保护进程取决于故障转移的计算机的类型。
- 将虚拟机从 Azure 复制到本地站点后，可以运行从 Azure 到本地站点的故障转移。
- 计算机在本地运行后，可以启用复制，以便将其复制到 Azure 以进行灾难恢复。

故障回复的工作原理如下所示：

- 若要进行故障回复，VM 需要至少一个恢复点才能进行故障回复。 在恢复计划中，计划中的所有 Vm 至少需要一个恢复点。
- 建议使用**最新**的恢复点进行故障回复（这是一个故障一致性点）。
    - 存在应用一致的恢复点选项。 在这种情况下，单个 VM 恢复到其最新的可用应用一致恢复点。 对于具有复制组的恢复计划，每个复制组都将恢复到其通用的可用恢复点。
    - 应用一致的恢复点可能会落后，并且可能会丢失数据。
- 在从 Azure 故障转移到本地站点时，Site Recovery 会关闭 Azure Vm。 提交故障转移时，Site Recovery 会删除 Azure 中已故障回复的 Azure Vm。


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/物理重新保护/故障回复

若要将 VMware 计算机和物理服务器从 Azure 重新保护和故障回复到本地，需要一个故障回复基础结构，并且有许多要求。

- **Azure 中的临时进程服务器**：若要从 Azure 进行故障回复，需要设置用作进程服务器的 Azure VM，以处理从 Azure 进行的复制。 故障回复完成后，可以删除此 VM。
- **VPN 连接**：若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 ExpressRoute）。
- **单独的主目标服务器**：默认情况下，在本地 VMware VM 上与配置服务器一起安装的主目标服务器用于处理故障回复。 如果对大量流量进行故障回复，应创建专用于此目的的独立本地主目标服务器。
- **故障回复策略**：若要复制回到本地站点，需要创建故障回复策略。 此策略是在创建从本地到 Azure 的复制策略时自动创建的。
    - 此策略会自动与配置服务器关联。
    - 无法编辑此策略。
    - 策略值： RPO 阈值-15 分钟;恢复点保留期-24 小时;应用一致性快照频率为60分钟。

详细了解 VMware/物理重新保护和故障回复：
- [查看](vmware-azure-reprotect.md#before-you-begin)重新保护和故障回复的其他要求。
- 在 Azure 中[部署](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure)进程服务器。
- [部署](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server)单独的主目标服务器。

将 Azure Vm 重新保护到本地时，可以指定要故障回复到原始位置或备用位置。

- **原始位置恢复**：这会从 Azure 故障回复到同一源本地计算机（如果存在）。 在这种情况下，只会将更改复制回本地。
- **备用位置恢复**：如果不存在本地计算机，可以从 Azure 故障回复到备用位置。 将 Azure VM 重新保护到本地时，会创建本地计算机。 从 Azure 到本地的完整数据复制。 --[查看](concepts-types-of-failback.md)位置故障回复的要求和限制。



## <a name="hyper-v-reprotectionfailback"></a>Hyper-v 重新保护/故障回复

将 Azure 中的 Hyper-v Vm 重新保护和故障回复到本地：

- 只能对使用存储帐户进行复制的 Hyper-v Vm 进行故障回复。 不支持使用托管磁盘进行复制的 Hyper-v Vm 的故障回复。
- 本地 Hyper-v 主机（如果已使用，则为 System Center VMM）应连接到 Azure。
- 运行计划的从 Azure 到本地的故障回复。
- 不需要为 Hyper-v VM 故障回复设置特定组件。
- 在计划的故障转移期间，可以选择用于在故障回复前同步数据的选项：
    - **在故障转移前同步数据**：此选项可最大程度地减少虚拟机的停机时间，因为它在不关闭虚拟机的情况下 
        - 阶段1：拍摄 Azure VM 的快照，并将其复制到本地 Hyper-v 主机。 计算机将继续在 Azure 中运行。
        - 阶段2：关闭 Azure VM，以便不会发生新的更改。 最终的增量更改集将传输到本地服务器，并启动本地 VM。
    - **仅在故障转移期间同步数据**：此选项速度更快，因为我们预计磁盘的大部分已更改，因此不会执行校验和计算。 此选项会执行磁盘的下载。 如果 VM 已在 Azure 中运行了一段时间（一个月或更多），或者如果已删除了本地 VM，则建议使用此选项。  

[详细了解](hyper-v-azure-failback.md)hyper-v 重新保护和故障回复。

将 Azure Vm 重新保护到本地时，可以指定要故障回复到原始位置或备用位置。

- **原始位置恢复**：这会从 Azure 故障回复到同一源本地计算机（如果存在）。 在这种情况下，你选择上一过程中所述的同步选项之一。
- **备用位置恢复**：如果不存在本地计算机，可以从 Azure 故障回复到备用位置。 将 Azure VM 重新保护到本地时，会创建本地计算机。 如果选择此选项，我们建议选择用于在故障转移之前同步数据的选项 
- [查看](hyper-v-azure-failback.md)位置故障回复的要求和限制。


故障回复到本地站点后，启用**反向复制**，开始将 VM 复制到 Azure，并完成循环。




## <a name="next-steps"></a>后续步骤
- 故障转移[特定的 VMware vm](vmware-azure-tutorial-failover-failback.md) 
- 故障转移[特定 Hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)。
- [创建](site-recovery-create-recovery-plans.md)恢复计划。
- 故障转移[恢复计划中的 vm](site-recovery-failover.md)。
- [准备](vmware-azure-failback.md)VMware 重新保护和故障回复。
- 故障回复[Hyper-v vm](hyper-v-azure-failback.md)。

