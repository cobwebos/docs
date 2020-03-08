---
title: 在灾难恢复期间使用 Azure Site Recovery 运行故障转移
description: 如何通过 Azure Site Recovery 将 Vm/物理服务器故障转移到 Azure。
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 514f1d6631a70301589943ddb7920ca3c9c46062
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362558"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>运行从本地到 Azure 的故障转移

本文介绍如何在[Azure Site Recovery](site-recovery-overview.md)中将本地计算机故障转移到 Azure

## <a name="before-you-start"></a>开始之前

- [了解](failover-failback-overview.md)灾难恢复中的故障转移过程。
- 如果要对多台计算机进行故障转移，请[了解](recovery-plan-overview.md)如何在恢复计划中同时收集计算机。
- 在执行完全故障转移之前，请运行[灾难恢复演练](site-recovery-test-failover-to-azure.md)，以确保一切按预期运行。

## <a name="prepare-to-connect-after-failover"></a>准备在故障转移后连接

若要确保能够连接到在故障转移后创建的 Azure Vm，需要在故障转移之前在本地执行一些操作。


### <a name="prepare-on-premises-to-connect-after-failover"></a>准备在故障转移后进行连接

如果要在故障转移后使用 RDP/SSH 连接到 Azure Vm，请在故障转移之前在本地执行许多操作。

故障转移后 | **位置** | **操作**
--- | --- | ---
**运行 Windows 的 Azure VM** | 故障转移之前的本地计算机 | 若要通过 Internet 访问 Azure VM，请启用 RDP，并确保已针对“公共”添加 TCP 和 UDP 规则，并在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。<br/><br/> 若要通过站点到站点连接访问 Azure VM，请在计算机上启用 RDP，并确保在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。<br/><br/> <br/><br/> 删除任何静态持久路由和 WinHTTP 代理。 确保操作系统 SAN 策略已设置为 **OnlineAll**。 [了解详细信息](https://support.microsoft.com/kb/3031135)。<br/><br/> 在触发故障转移时，请确保 VM 上没有处于挂起状态的 Windows 更新。 Windows 更新可能会在故障转移时启动，在更新完成之前，无法登录到 VM。
**运行 Linux 的 Azure VM** | 故障转移之前的本地计算机 | 确保 VM 上的安全外壳服务已设置为在系统引导时自动启动。<br/><br/> 确保防火墙规则允许 SSH 连接。


## <a name="run-a-failover"></a>运行故障转移

本过程介绍如何根据[恢复计划](site-recovery-create-recovery-plans.md)运行故障转移。 如果要对单个 VM 运行故障转移，请按照[VMWARE vm](vmware-azure-tutorial-failover-failback.md)、[物理服务器](physical-to-azure-failover-failback.md)或[hyper-v vm](hyper-v-azure-failover-failback-tutorial.md)的说明进行操作。


运行恢复计划故障转移，如下所示：

1. 在 Site Recovery 保管库中，选择 "**恢复计划** > *recoveryplan_name*"。
2. 单击 **“故障转移”** 。

    ![故障转移](./media/site-recovery-failover/Failover.png)

3. 在 "**故障转移** > **故障转移方向**" 下，如果要复制到 Azure，请保留默认值。
4. 在 "**故障转移**" 中，选择要故障转移到的**恢复点**。

    - **最新**：使用最新的点。 这会处理发送到 Site Recovery 服务的所有数据，并为每台计算机创建一个恢复点。 此选项提供最低的 RPO （恢复点目标），因为在故障转移后创建的 VM 将具有触发故障转移时复制到 Site Recovery 的所有数据。
   - **最新处理**：使用此选项将 vm 故障转移到已由 Site Recovery 处理的最新恢复点。 你可以在 VM**最新恢复点**中查看最近处理的恢复点。 此选项提供低 RTO，因为无需花费时间来处理未经处理的数据
   - **最新应用一致**：使用此选项可将 vm 故障转移到 Site Recovery 处理的最新应用程序一致恢复点。
   - **最新多 VM 已处理**：使用此选项时，属于复制组的 vm 将故障转移到最新的公共多 vm 一致恢复点。 其他虚拟机将故障转移到其最新的已处理恢复点。 此选项仅适用于至少具有一个已启用多 VM 一致性的 VM 的恢复计划。
   - **最新的多 VM 应用一致**：通过此选项，属于复制组的 vm 会故障转移到最新的通用多 vm 应用程序一致恢复点。 其他虚拟机将故障转移到其最新的应用程序一致恢复点。 仅适用于至少启用了一个启用了多 VM 一致性的 VM 的恢复计划。
   - **自定义**：不可用于恢复计划。 此选项仅适用于单个 Vm 的故障转移。

5. 如果希望 Site Recovery 在开始故障转移之前关闭源 Vm，请选择 "**在开始故障转移前关闭计算机**"。 即使关机失败，故障转移也仍会继续。  

    > [!NOTE]
    > 如果对 Hyper-v Vm 进行故障转移，则在触发故障转移之前，关机会尝试同步并复制尚未发送到服务的本地数据。 

6. 在 "**作业**" 页上跟踪故障转移进度。 即使发生错误，恢复计划也会运行，直到完成。
7. 故障转移后，登录到 VM 进行验证。 
8. 如果要切换到用于故障转移的不同恢复点，请使用 "**更改恢复点**"。
9. 准备就绪后，可以提交故障转移。**提交**操作会删除服务中的所有恢复点。 "**更改恢复点**" 选项将不再可用。

## <a name="run-a-planned-failover-hyper-v"></a>运行计划的故障转移（Hyper-v）

可以为 Hyper-v Vm 运行计划的故障转移。

- 计划的故障转移是零数据丢失故障转移选项。
- 触发计划内故障转移时，首先会关闭源虚拟机，同步最新数据，然后再触发故障转移。
- 使用**计划的故障转移**选项运行计划的故障转移。 它与常规故障转移的运行方式类似。
 
## <a name="track-failovers"></a>跟踪故障转移

与故障转移相关联的作业有很多。

![故障转移](./media/site-recovery-failover/FailoverJob.png)

- **先决条件检查**：确保满足故障转移所需的所有条件。
- **故障转移**：处理数据，以便可以从中创建 Azure VM。 如果选择了 "**最新**恢复点"，则会从发送到服务的数据创建恢复点。
- **启动**：使用上一步骤中处理的数据创建 Azure VM。

> [!WARNING]
> **请勿取消正在进行的故障转移**：启动故障转移之前，已停止为 VM 复制。 如果取消正在进行的作业，故障转移会停止，但 VM 不会开始复制。 无法重新启动复制。


### <a name="extra-failover-time"></a>额外故障转移时间

在某些情况下，VM 故障转移需要中间步骤，通常需要约8到10分钟才能完成。 这些是受此附加步骤/时间影响的计算机：

* 运行早于9.8 的移动服务版本的 VMware 虚拟机。
* 作为物理服务器受到保护的物理服务器和 Hyper-v Vm。
* VMware Linux VM。
* 这些驱动程序不作为启动驱动程序出现的 VMware Vm：
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 未启用 DHCP 的 VMware Vm，无论它们使用 DHCP 还是静态 IP 地址。


## <a name="automate-actions-during-failover"></a>在故障转移过程中自动执行操作

你可能想要在故障转移过程中自动执行操作。 为此，可以在恢复计划中使用脚本或 Azure 自动化 runbook。

- [了解](site-recovery-create-recovery-plans.md)如何创建和自定义恢复计划，包括添加脚本。
- [了解](site-recovery-runbook-automation.md)如何将 Azure 自动化 runbook 添加到恢复计划。


## <a name="configure-settings-after-failover"></a>故障转移后配置设置

### <a name="retain-drive-letters-after-failover"></a>故障转移后保留驱动器号

Site Recovery 处理驱动器号的保留期。 如果在 VM 复制期间排除磁盘，请[查看其](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)工作原理的示例。

### <a name="prepare-in-azure-to-connect-after-failover"></a>在 Azure 中准备好在故障转移后进行连接

如果要连接到使用 RDP 或 SSH 在故障转移后创建的 Azure Vm，请遵循表中汇总的要求。

**故障转移** | **位置** | **操作**
--- | --- | ---
**运行 Windows 的 Azure VM** | 故障转移后的 Azure VM |  为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 RDP 端口建立传入连接。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。<br/><br/> 如果无法连接，请检查 VM 是否正在运行，并查看这些[故障排除提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。
**运行 Linux 的 Azure VM** | 故障转移后的 Azure VM | 已故障转移的 VM（及其连接到的 Azure 子网）上的网络安全组规则需要允许与 SSH 端口建立传入连接。<br/><br/> 为 VM [添加公共 IP 地址](https://aka.ms/addpublicip)。<br/><br/> 选中“启动诊断”可查看 VM 的屏幕截图。<br/><br/>

按照[此处](site-recovery-failover-to-azure-troubleshoot.md)所述步骤对故障转移后的任何连接问题进行故障排除。

## <a name="set-up-ip-addressing"></a>设置 IP 寻址

- **内部 ip 地址**：若要在故障转移后设置 Azure VM 的内部 ip 地址，可以使用以下两个选项：
    - 保留相同的 IP 地址：你可以在 Azure VM 上使用与分配给本地计算机的 IP 地址相同的 IP 地址。
    - 使用不同的 IP 地址：可以为 Azure VM 使用不同的 IP 地址。
    - [了解](concepts-on-premises-to-azure-networking.md#assign-an-internal-address)有关设置内部 IP 地址的详细信息。
- **外部 ip 地址**：可以保留公共 ip 地址进行故障转移。 在故障转移过程中创建的 azure Vm 必须分配有 azure 区域中可用的 Azure 公共 IP 地址。 你可以手动分配公共 IP 地址，也可以使用恢复计划自动执行此过程。 [了解详细信息](concepts-public-ip-address-with-site-recovery.md)。


## <a name="next-steps"></a>后续步骤

故障转移后，需要重新保护才能开始将 Azure Vm 复制回本地站点。 在复制启动并运行后，可以在准备就绪后在本地进行故障回复。

- [详细了解](failover-failback-overview.md#reprotectionfailback)重新保护和故障回复。
- [准备](vmware-azure-reprotect.md)VMware 重新保护和故障回复。
- [故障回复](hyper-v-azure-failback.md)Hyper-v Vm。
- [了解](physical-to-azure-failover-failback.md)物理服务器的故障转移和故障回复过程。

