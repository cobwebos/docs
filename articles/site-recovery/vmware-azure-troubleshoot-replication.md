---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的复制问题 | Microsoft Docs
description: 本文针对使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 期间遇到的常见复制问题提供故障排除信息。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924825"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>解决 VMware VM 和物理服务器的复制问题

本文介绍了一些常见的问题以及在本地 VMware Vm 和物理服务器复制到 Azure 中使用时可能遇到的特定错误[Site Recovery](site-recovery-overview.md)。

## <a name="step-1-monitor-process-server-health"></a>步骤 1：监视进程服务器运行状况

Site recovery[进程服务器](vmware-physical-azure-config-process-server-overview.md#process-server)接收和优化复制的数据，并将其发送到 Azure。

我们建议您监视在门户中，以确保它们已连接并且正常运行，且复制时的进展情况的源计算机与进程服务器关联的进程服务器的运行状况。

- [了解有关](vmware-physical-azure-monitor-process-server.md)监视的进程服务器。
- [查看最佳实践](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [进行故障排除](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)进程服务器运行状况。

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>步骤 2：对连接和复制问题进行故障排除

初始和正在进行的复制故障通常是由源服务器和进程服务器之间或进程服务器与 Azure 之间的连接问题引起。 

若要解决这些问题[连接和复制进行故障排除](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)。




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>步骤 3：对不是可用于复制的源计算机进行故障排除

尝试选择源计算机来通过 Site Recovery 启用复制时，计算机可能由于以下原因之一而不可用：

* **具有相同实例 UUID 的两个虚拟机**：如果 vCenter 中存在具有相同实例 UUID 的两个虚拟机，则配置服务器发现的第一个虚拟机将显示在 Azure 门户中。 若要解决此问题，请确保没有两个虚拟机具有相同的实例 UUID。 此方案常见于的情形： 备份 VM 将变为活动状态，登录到我们的发现记录。 请参阅[使用 Azure Site Recovery 进行 VMware 到 Azure 的复制：如何清理重复或过时的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)来解决问题。
* **vCenter 用户凭据不正确**：确保在使用 OVF 模板或统一安装程序安装配置服务器时添加正确的 vCenter 凭据。 若要验证安装期间添加的凭据是否正确，请参阅[修改用于自动发现的凭据](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 特权不足**：如果未提供所需的权限来访问 vCenter，则发现虚拟机时可能会失败。 确保将[为自动发现准备帐户](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的权限添加到 vCenter 用户帐户。
* **Azure Site Recovery 管理服务器**：如果虚拟机用作管理服务器并充当以下一个或多个角色 - 配置服务器/横向扩展进程服务器/主目标服务器，则无法在门户中选择该虚拟机。 无法复制管理服务器。
* **已通过 Azure Site Recovery 服务进行保护/故障转移**：如果虚拟机已通过 Site Recovery 进行保护或故障转移，则无法在门户中选择保护该虚拟机。 确保要在门户中查找的虚拟机尚未由其他任何用户进行保护，或者位于不同的订阅下。
* **vCenter 未连接**：检查 vCenter 是否处于连接状态。 若要验证，请转到“恢复服务保管库”>“Site Recovery 基础结构”>“配置服务器”，单击相应的配置服务器，此时，右侧会打开一个边栏选项卡，其中显示了关联服务器的详细信息。 检查 vCenter 是否已连接。 如果它处于"未连接"状态，解决此问题，然后[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)门户上。 然后，虚拟机将列在门户中。
* **ESXi 已关机**：如果虚拟机所在的 ESXi 主机处于关机状态，则虚拟机将不会列出，或者在 Azure 门户中不可选择。 打开 ESXi 主机，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **正在等待重新启动**：如果虚拟机正在等待重新启动，则你无法在 Azure 门户中选择该虚拟机。 请务必完成等待中的重新启动活动，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **找不到 IP**：如果虚拟机没有关联有效的 IP 地址，则你无法在 Azure 门户中选择该虚拟机。 请务必将有效的 IP 地址分配到虚拟机，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>在门户中灰显的受保护的虚拟机进行故障排除

如果系统中存在重复的条目，则在 Site Recovery 下复制的虚拟机将不会显示在 Azure 门户中。 若要了解如何删除过时的条目和解决此问题，请参阅[使用 Azure Site Recovery 进行 VMware 到 Azure 的复制：如何清除重复或过时的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)。

## <a name="common-errors-and-solutions"></a>常见错误和解决方案

### <a name="initial-replication-issues-error-78169"></a>初始复制问题 [error 78169]

通过更高版本，确保有无连接、 带宽或时间同步相关的问题，请确保：

- 没有防病毒软件正在阻止 Azure Site Recovery。 了解[详细](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的文件夹排除项。

### <a name="missing-app-consistent-recovery-points-error-78144"></a>缺少应用程序一致的恢复点 [error 78144]

 由于卷影复制服务 (VSS) 问题而发生这种情况。 若要解决问题，请执行以下操作： 
 
- 验证安装的 Azure Site Recovery 代理版本至少 9.22.2。 
- 验证为 Windows 服务中的服务安装 VSS 提供程序，同时还要验证组件服务 MMC 来检查列出了 Azure Site Recovery VSS 提供程序。
- 如果未安装 VSS 提供程序，请参阅[安装故障故障排除文章](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)。

- 如果禁用了 VSS，
    - 验证 VSS 提供程序服务的启动类型设置为**自动**。
    - 重启以下服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务

### <a name="source-machines-with-high-churn-error-78188"></a>[错误 78188] 的高变动量与源计算机

可能的原因：
- 列出的磁盘的虚拟机上的数据更改率 （写入字节数/秒） 是多个[Azure Site Recovery 支持的限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)复制目标存储帐户类型。
- 变动率中没有突增的高数据量因处于挂起状态进行上传。

若要解决问题，请执行以下操作：
- 请确保目标存储帐户类型 （标准或高级） 根据流失率要求在源设置。
- 如果观察到的变动量是暂时性的等待挂起的数据上传来保持同步，并创建恢复点的几个小时。
- 如果问题仍然存在，使用 Site Recovery[部署规划器](site-recovery-deployment-planner.md#overview)来帮助规划复制。

### <a name="source-machines-with-no-heartbeat-error-78174"></a>无检测信号 [error 78174] 与源计算机

发生这种情况是当源计算机上的 Azure Site Recovery 移动代理通信不时与配置服务器 (CS)。

若要解决此问题，使用以下步骤来验证来自源 VM 网络连接到配置服务器：

1. 验证源计算机正在运行。
2. 登录到源计算机使用具有管理员权限的帐户。
3. 验证以下服务是否正在运行，如果不重新启动服务：
   - Svagents （InMage Scout VX 代理）
   - InMage Scout 应用程序服务
4. 源计算机上检查错误详细信息的位置的日志：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>进程服务器无检测信号 [error 806]
如果没有任何检测信号从进程服务器 (PS)，请检查：
1. PS VM 已启动并运行
2. 检查错误详细信息的 PS 上的以下日志：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>主目标服务器无检测信号 [error 78022]

在主目标上的 Azure Site Recovery 移动代理未与配置服务器通信时，将发生这种情况。

若要解决此问题，使用以下步骤来验证服务状态：

1. 验证主目标 VM 正在运行。
2. 登录到主目标 VM，使用具有管理员权限的帐户。
    - 验证 svagents 服务正在运行。 如果正在运行，重新启动服务
    - 检查错误详细信息的位置的日志：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 我们的社区非常活跃，将有一位工程师为你提供帮助。
