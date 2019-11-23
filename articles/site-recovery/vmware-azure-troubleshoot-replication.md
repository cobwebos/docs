---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的复制问题 | Microsoft Docs
description: 本文针对使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 期间遇到的常见复制问题提供故障排除信息。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423962"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>解决 VMware VM 和物理服务器的复制问题

This article describes some common issues and specific errors you might encounter when you replicate on-premises VMware VMs and physical servers to Azure using [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Step 1: Monitor process server health

Site Recovery uses the [process server](vmware-physical-azure-config-process-server-overview.md#process-server) to receive and optimize replicated data, and send it to Azure.

We recommend that you monitor the health of process servers in  portal, to ensure that they are connected and working properly, and that replication is progressing for the source machines associated with the process server.

- [Learn about](vmware-physical-azure-monitor-process-server.md) monitoring process servers.
- [查看最佳实践](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Troubleshoot](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) process server health.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Step 2: Troubleshoot connectivity and replication issues

Initial and ongoing replication failures often are caused by connectivity issues between the source server and the process server or between the process server and Azure. 

To solve these issues, [troubleshoot connectivity and replication](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Step 3: Troubleshoot source machines that aren't available for replication

尝试选择源计算机来通过 Site Recovery 启用复制时，计算机可能由于以下原因之一而不可用：

* **Two virtual machines with same instance UUID**: If two virtual machines under the vCenter have the same instance UUID, the first virtual machine discovered by the configuration server is shown in the Azure portal. 若要解决此问题，请确保没有两个虚拟机具有相同的实例 UUID。 This scenario is commonly seen in instances where a backup VM becomes active and is logged into our discovery records. Refer to [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) to resolve.
* **Incorrect vCenter user credentials**: Ensure that you added the correct vCenter credentials when you set up the configuration server by using the OVF template or unified setup. 若要验证安装期间添加的凭据是否正确，请参阅[修改用于自动发现的凭据](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter insufficient privileges**: If the permissions provided to access vCenter don't have the required permissions, failure to discover virtual machines might occur. 确保将[为自动发现准备帐户](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的权限添加到 vCenter 用户帐户。
* **Azure Site Recovery management servers**: If the virtual machine is used as management server under one or more of the following roles - Configuration server /scale-out process server / Master target server, then you will not be able to choose the virtual machine from portal. 无法复制管理服务器。
* **Already protected/failed over through Azure Site Recovery services**: If the virtual machine is already protected or failed over through Site Recovery, the virtual machine isn't available to select for protection in the portal. 确保要在门户中查找的虚拟机尚未由其他任何用户进行保护，或者位于不同的订阅下。
* **vCenter not connected**: Check if vCenter is in connected state. 若要验证，请转到“恢复服务保管库”>“Site Recovery 基础结构”>“配置服务器”，单击相应的配置服务器，此时，右侧会打开一个边栏选项卡，其中显示了关联服务器的详细信息。 检查 vCenter 是否已连接。 If it's in a "Not Connected" state, resolve the issue and then [refresh the configuration server](vmware-azure-manage-configuration-server.md#refresh-configuration-server) on the portal. 然后，虚拟机将列在门户中。
* **ESXi powered off**: If ESXi host under which the virtual machine resides is in powered off state, then virtual machine will not be listed or will not be selectable on the Azure portal. 打开 ESXi 主机，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **Pending reboot**: If there is a pending reboot on the virtual machine, then you will not be able to select the machine on Azure portal. 请务必完成等待中的重新启动活动，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **IP not found**: If the virtual machine doesn't have a valid IP address associated with it, then you will not be able to select the machine on Azure portal. 请务必将有效的 IP 地址分配到虚拟机，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Troubleshoot protected virtual machines greyed out in the portal

如果系统中存在重复的条目，则在 Site Recovery 下复制的虚拟机将不会显示在 Azure 门户中。 To learn how to delete stale entries and resolve the issue, refer to [Azure Site Recovery VMware-to-Azure: How to clean up duplicate or stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>No crash consistent recovery point available for the VM in the last 'XXX' minutes

Some of the most common issues are listed below

### <a name="initial-replication-issues-error-78169"></a>Initial replication issues [error 78169]

Over an above ensuring that there are no connectivity, bandwidth or time sync related issues, ensure that:

- No anti-virus software is blocking Azure Site Recovery. Learn [more](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) on folder exclusions required for Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Source machines with high churn [error 78188]

Possible Causes:
- The data change rate (write bytes/sec) on the listed disks of the virtual machine is more than the [Azure Site Recovery supported limits](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) for the replication target storage account type.
- There is a sudden spike in the churn rate due to which high amount of data is pending for upload.

若要解决问题，请执行以下操作：
- Ensure that the target storage account type (Standard or Premium) is provisioned as per the churn rate requirement at source.
- If you are already replicating to a Premium managed disk (asrseeddisk type), ensure that the size of the disk supports the observed churn rate as per Site Recovery limits. You can increase the size of the asrseeddisk if required. 请按照以下步骤操作：
    - Navigate to the Disks blade of the impacted replicated machine and copy the replica disk name
    - Navigate to this replica managed disk
    - You may see a banner on the Overview blade saying that a SAS URL has been generated. Click on this banner and cancel the export. Ignore this step if you do not see the banner.
    - As soon as the SAS URL is revoked, go to Configuration blade of the Managed Disk and increase the size so that ASR supports the observed churn rate on source disk
- If the observed churn is temporary, wait for a few hours for the pending data upload to catch up and to create recovery points.
- If the disk contains non-critical data like temporary logs, test data etc., consider moving this data elsewhere or completely exclude this disk from replication
- If the problem continues to persist, use the Site Recovery [deployment planner](site-recovery-deployment-planner.md#overview) to help plan replication.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Source machines with no heartbeat [error 78174]

This happens when Azure Site Recovery Mobility agent on the Source Machine is not communicating with the Configuration Server (CS).

To resolve the issue, use the following steps to verify the network connectivity from the source VM to the Config Server:

1. Verify that the Source Machine is running.
2. Sign in to the Source Machine using an account that has administrator privileges.
3. Verify that the following services are running and if not restart the services:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout 应用程序服务
4. On the Source Machine, examine the logs at the location for error details:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Process server with no heartbeat [error 806]
In case there is no heartbeat from the Process Server (PS), check that:
1. PS VM is up and running
2. Check following logs on the PS for error details:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Master target server with no heartbeat [error 78022]

This happens when Azure Site Recovery Mobility agent on the Master Target is not communicating with the Configuration Server.

To resolve the issue, use the following steps to verify the service status:

1. Verify that the Master Target VM is running.
2. Sign in to the Master Target VM using an account that has administrator privileges.
    - Verify that the svagents service is running. If it is running, restart the service
    - Check the logs at the location for error details:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. To register master target with configuration server, navigate to folder **%PROGRAMDATA%\ASR\Agent**, and run the following on command prompt:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Error ID 78144 - No app-consistent recovery point available for the VM in the last 'XXX' minutes

Enhancements have been made in mobility agent [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) versions to handle VSS installation failure behaviors. Ensure that you are on the latest versions for best guidance on troubleshooting VSS failures.

Some of the most common issues are listed below

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Cause 1: Known issue in SQL server 2008/2008 R2 
**How to fix** : There is a known issue with SQL server 2008/2008 R2. Please refer this KB article [Azure Site Recovery Agent or other non-component VSS backup fails for a server hosting SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Cause 2: Azure Site Recovery jobs fail on servers hosting any version of SQL Server instances with AUTO_CLOSE DBs 
**How to fix** : Refer Kb [article](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Cause 3: Known issue in SQL Server 2016 and 2017
**How to fix** : Refer Kb [article](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>More causes due to VSS related issues:

To troubleshoot further, Check the files on the source machine to get the exact error code for failure:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

How to locate the errors in the file?
Search for the string "vacpError"  by opening the vacp.log file in an editor
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

In the above example **2147754994** is the error code that tells you about the failure as shown below

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS writer is not installed - Error 2147221164 

*How to fix*: To generate application consistency tag, Azure Site Recovery uses Microsoft Volume Shadow copy Service (VSS). It installs a VSS Provider for its operation to take app consistency snapshots. This VSS Provider is installed as a service. In case the VSS Provider service is not installed, the application consistency snapshot creation fails with the error id 0x80040154  "Class not registered". </br>
Refer [article for VSS writer installation troubleshooting](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS writer is disabled - Error 2147943458

**How to fix**: To generate application consistency tag, Azure Site Recovery uses Microsoft Volume Shadow copy Service (VSS). It installs a VSS Provider for its operation to take app consistency snapshots. This VSS Provider is installed as a service. In case the VSS Provider service is disabled, the application consistency snapshot creation fails with the error id "The specified service is disabled and cannot be started(0x80070422)". </br>

- If VSS is disabled,
    - Verify that the startup type of the VSS Provider service is set to **Automatic**.
    - Restart the following services:
        - VSS service
        - Azure Site Recovery VSS 提供程序
        - VDS service

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Error 2147754756

**How to fix**: To generate application consistency tag, Azure Site Recovery uses Microsoft Volume Shadow copy Service (VSS). Check if the Azure Site Recovery  VSS Provider service is installed or not. </br>

- Retry the Provider installation using the following commands:
- Uninstall existing provider: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstall: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Verify that the startup type of the VSS Provider service is set to **Automatic**.
    - Restart the following services:
        - VSS service
        - Azure Site Recovery VSS 提供程序
        - VDS service

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 我们的社区非常活跃，将有一位工程师为你提供帮助。
