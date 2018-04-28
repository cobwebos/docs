---
title: 排查使用 Azure Site Recovery 执行 Hyper-V 到 Azure 的复制的问题 | Microsoft Docs
description: 介绍如何排查使用 Azure Site Recovery 执行 Hyper-V 到 Azure 的复制的问题
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/09/2018
ms.author: rayne
ms.openlocfilehash: 95a33c80b1aeef7fbf8bea0ab760bbd66babdac8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>排查 Hyper-V 到 Azure 的复制和故障转移的问题

本文介绍在使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 Hyper-V VM 复制到 Azure 时可能遇到的常见问题。

## <a name="enable-protection-issues"></a>启用保护的问题

如果为 Hyper-V VM 启用保护时遇到问题，请检查以下各项：

1. 检查 Hyper-V 主机和 VM 是否符合所有[要求和先决条件](hyper-v-azure-support-matrix.md)。
2. 如果 Hyper-V 服务器位于 System Center Virtual Machine Manager (VMM) 云中，请验证是否已准备好 [VMM 服务器](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)。
3. 检查 Hyper-V 虚拟机管理服务是否在 Hyper-V 主机上运行。
4. 检查 VM 上的 Hyper-V-VMMS\Admin 日志中显示的问题。 此日志位于“应用程序和服务日志” > “Microsoft” > “Windows”中。
5. 在来宾 VM 上，验证 WMI 是否已启用并可访问。
  - [了解](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)基本 WMI 测试。
  - [排查](https://aka.ms/WMiTshooting) WMI 问题。
  - [排查](https://technet.microsoft.com/library/ff406382.aspx#H22) WMI 脚本和服务的问题。
5. 在来宾 VM 上，确保运行最新版本的 Integration Services。
    - [检查](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)是否安装了最新版本。
    - [保持](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integration Services 的最新状态。
    
## <a name="replication-issues"></a>复制问题

按如下所述排查初始和持续复制的问题：

1. 确保运行[最新版本](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)的 Site Recovery 服务。
2. 验证复制是否已暂停：
  - 在 Hyper-V 管理器控制台中检查 VM 运行状况。
  - 如果问题比较严重，请右键单击 VM 并选择“复制” > “查看复制运行状况”。
  - 如果复制已暂停，请单击“恢复复制”。
3. 检查所需的服务是否正在运行。 如果未运行，请将其重启。
    - 如果不使用 VMM 复制 Hyper-V，请检查以下服务是否在 Hyper-V 主机上运行：
        - 虚拟机管理服务
        - Microsoft Azure 恢复服务代理服务
        - Microsoft Azure Site Recovery 服务
        - WMI 提供程序主机服务
    - 如果在环境中使用 VMM 复制，请检查以下服务是否正在运行：
        - 在 Hyper-V 主机上，检查虚拟机管理服务、Microsoft Azure 恢复服务代理和 WMI 提供程序主机服务是否正在运行。
        - 在 VMM 服务器上，确保 System Center Virtual Machine Manager 服务正在运行。
4. 检查 Hyper-V 服务器与 Azure 之间的连接。 为此，请在 Hyper-V 主机上打开任务管理器。 在“性能”选项卡上，单击“打开资源监视器”。 在“网络”选项卡上的“网络活动的进程”中，检查 cbengine.exe 是否正在主动发送大量 (Mbs) 数据。
5. 检查 Hyper-V 主机是否能够连接到 Azure 存储 Blob URL。 为此，请选择并检查 **cbengine.exe**。 查看“TCP 连接”，以验证主机到 Azure 存储 Blob 的连接。
6. 按如下所述检查性能问题。
    
### <a name="performance-issues"></a>性能问题

网络带宽限制可能会影响复制。 按如下所述排查问题：

1. [检查](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)环境中是否存在带宽约束或限制约束。
2. 运行[部署规划器探查器](hyper-v-deployment-planner-run.md)。
3. 运行探查器后，遵循[带宽](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)和[存储](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)方面的建议。
4. 检查[数据变动限制](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)。 如果发现 VM 上的数据变动率较高，请执行以下操作：
  - 检查 VM 是否标记为重新同步。
  - 遵循[这些步骤](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/)来调查变动的起源。
  - 当 HRL 日志文件超过可用磁盘空间的 50% 时，可能会发生数据变动。 如果这是问题所在，请为出现问题的所有 VM 预配更多的存储空间。
  - 检查复制是否未暂停。 如果未暂停，则它会继续将更改写入 HRL 文件，从而可能导致其大小增加。
 

## <a name="critical-replication-state-issues"></a>严重的复制状态问题

1. 若要检查复制运行状况，请连接到本地 Hyper-V 管理器控制台，选择 VM，然后检查运行状况。

    ![复制运行状况](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. 单击“查看复制运行状况”查看详细信息：

    - 如果复制已暂停，请右键单击 VM 并选择“复制” > “恢复复制”。
    - 如果 Site Recovery 中配置的 Hyper-V 主机上的某个 VM 要迁移到同一群集中的不同 Hyper-V 主机或迁移到独立计算机，则该 VM 的复制不受影响。 只需检查新的 Hyper-V 主机是否符合所有先决条件，并已在 Site Recovery 中进行配置。

## <a name="app-consistent-snapshot-issues"></a>应用一致的快照问题

应用一致的快照是 VM 内应用程序数据的时间点快照。 卷影复制服务 (VSS) 确保 VM 上的应用在创建快照时处于一致状态。  本部分详细说明可能会遇到的一些常见问题。

### <a name="vss-failing-inside-the-vm"></a>VSS 在 VM 中失败

1. 检查是否已安装并运行最新版本的 Integration Services。  在 Hyper-V 主机上权限提升的 PowerShell 提示符下运行以下命令，检查是否有可用的更新：**get-vm  | select Name, State, IntegrationServicesState**。
2. 检查 VSS 服务是否正在运行且正常：
    - 为此，请登录到来宾 VM。 然后打开管理员命令提示符，运行以下命令检查所有 VSS 编写器是否正常。
        - **Vssadmin list writers**
        - **Vssadmin list shadows**
        - **Vssadmin list providers**
    - 检查输出。 如果编写器处于失败状态，请执行以下操作：
        - 在 VM 上的应用程序事件日志中检查 VSS 操作错误。
    - 尝试重启与失败的编写器关联的以下服务：
        - 卷影复制
         - Azure Site Recovery VSS 提供程序
    - 执行此操作后，请等待几个小时再查看是否已成功生成应用一致的快照。
    - 最后一招是尝试重新启动 VM。 这可能会解决处于无响应状态的服务。
3. 检查 VM 中是否不包含任何动态磁盘。 应用一致的快照不支持动态磁盘。 可在磁盘管理 (diskmgmt.msc) 中进行此项检查。

    ![动态磁盘](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. 检查是否未将 iSCSI 磁盘附加到 VM。 不支持此操作。
5. 检查是否已启用备份服务。 请在“Hyper-V 设置” > “Integration Services”进行此项检查。
6. 确保创建 VSS 快照的应用未发生冲突。 如果多个应用同时尝试创建 VSS 快照，则可能会发生冲突。 例如，某个备份应用在创建 VSS 快照，同时，Site Recovery 也在根据复制策略的计划创建快照。   
7. 检查 VM 是否遇到较高的变动率：
    - 可以使用 Hyper-V 主机上的性能计数器，测量来宾 VM 的每日数据更改率。 为此，请启用以下计数器。 聚合所有 VM 磁盘的此值的 5-15 分钟样本，即可得出 VM 变动率。
        - 类别：“Hyper-V Virtual Storage Device”
        - 计数器：“Write Bytes / Sec”</br>
        - 根据 VM 或其应用的繁忙程度，此数据变动率将会提高或保持在较高级别。
        - 对于 Site Recovery 的标准存储，平均源磁盘数据变动率为 2 MB/秒。 [了解详细信息](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - 此外，可以[验证存储可伸缩性目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets.md#scalability-targets-for-a-storage-account)。
8. 运行[部署规划器](hyper-v-deployment-planner-run.md)。
9. 查看有关[网络](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)和[存储](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)的建议。


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS 在 Hyper-V 主机中失败

1. 在事件日志中检查 VSS 错误和建议：
    - 在 Hyper-V 主机服务器上，通过“事件查看器” > “应用程序和服务日志” > “Microsoft” > “Windows” > “Hyper-V” > “管理”打开 Hyper-V 管理事件日志。
    - 检查是否有任何事件指示发生应用一致的快照失败。
    - 典型的错误为：“Hyper-V 无法为虚拟机 'XYZ' 生成 VSS 快照集: 编写器遇到非暂时性错误。 如果服务无响应，重启 VSS 服务可能会解决问题。”

2. 若要为 VM 生成 VSS 快照，请检查 VM 上是否已安装 Hyper-V Integration Services，并已启用备份 (VSS) 集成服务。
    - 确保 Integration Services VSS 服务/守护程序在来宾上运行，并处于“正常”状态。
    - 可以在 Hyper-V 主机上权限提升的 PowerShell 会话中，使用命令 **et-VMIntegrationService -VMName<VMName>-Name VSS** 执行此项检查。也可以登录到来宾 VM 来获取此信息。 [了解详细信息](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。
    - 确保 VM 上的备份/VSS Integration Services 正在运行且处于正常状态。 否则，请重启这些服务，并重启 Hyper-V 主机服务器上的 Hyper-V 卷影复制请求程序服务。

### <a name="common-errors"></a>常见错误

**错误代码** | **消息** | **详细信息**
--- | --- | ---
**0x800700EA** | “Hyper-V 无法为虚拟机生成 VSS 快照集: 更多数据可用。 (0x800700EA)。 如果备份操作正在进行，VSS 快照集生成可能失败。<br/><br/> 虚拟机复制操作失败: 更多数据可用。” | 检查是否在 VM 上启用了动态磁盘。 不支持此操作。
**0x80070032** | “由于版本与 Hyper-V 预期的版本不匹配，Hyper-V 卷影复制请求程序无法连接到虚拟机 <./VMname>” | 检查是否安装了最新的 Windows 更新。<br/><br/> [升级](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services.md#keep-integration-services-up-to-date)到最新版本的 Integration Services。



## <a name="collect-replication-logs"></a>收集复制日志

所有 Hyper-V 复制事件都记录在“应用程序和服务日志” > “Microsoft” > “Windows”下的“Hyper-V-VMMS\管理”日志中。 此外，可按如下所示为 Hyper-V 虚拟机管理服务启用分析日志：

1. 在事件查看器中显示分析和调试日志。 为此，请在事件查看器中，单击“视图” > “显示分析和调试日志”。 分析日志显示在“Hyper-V-VMMS”下。
2. 在“**操作**”窗格中，单击“**启用日志**”。 

    ![启用日志](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. 启用后，日志将作为“事件跟踪会话”显示在“性能监视器”中的“数据收集器集”下。 
4. 若要查看收集到的信息，请禁用日志，从而停止跟踪会话。 然后保存日志，并再次在事件查看器中打开日志，或使用其他工具根据需要转换日志。


### <a name="event-log-locations"></a>事件日志位置

**事件日志** | **详细信息** |
--- | ---
**应用程序和服务日志/Microsoft/VirtualMachineManager/服务器/管理**（VMM 服务器） | 用于排查 VMM 问题的日志。
**Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication**（Hyper-V 主机） | 用于排查 Microsoft Azure 恢复服务代理问题的日志。 
**Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational**（Hyper-V 主机）| 用于排查 Microsoft Azure Site Recovery 服务问题的日志。
**Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin**（Hyper-V 主机） | 用于排查 Hyper-V VM 管理问题的日志。

### <a name="log-collection-for-advanced-troubleshooting"></a>用于高级故障排除的日志集合

以下工具可帮助进行高级故障排除：

-   对于 VMM，请使用[支持诊断平台 (SDP) 工具](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)执行 Site Recovery 日志收集。
-   对于不带 VMM 的 Hyper-V，请[下载此工具](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)，并在 Hyper-V 主机上运行该工具来收集日志。

