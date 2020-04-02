---
title: 使用 Azure 站点恢复对 Azure VM 的复制进行故障排除
description: 使用 Azure 站点恢复在 Azure VM 灾难恢复中排除复制故障
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: c5389c064e137358670aecabe97f1cea38dfbcbf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549271"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>在 Azure VM 灾难恢复中排除复制故障

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机时在 Azure Site Recovery 中经常出现的问题。 它还解释了如何排除常见问题。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

Azure 站点恢复始终将数据从源区域复制到灾难恢复区域。 它还每 5 分钟创建一个崩溃一致的恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

错误消息："在过去 60 分钟内没有 VM 可用的崩溃一致恢复点。</br>
错误 ID： 153007

以下部分介绍原因和解决方案。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>源虚拟机上的数据更改率高

如果源虚拟机上的数据更改速率高于支持的限制，Azure 站点恢复将创建事件。 要查看问题是否由于高改动，转到**复制项** > **VM** > **事件 - 持续 72 小时**。
你应当会看到事件“数据更改率超过支持的限制”：

![Azure 站点恢复页，显示过高的数据更改率](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果选择该事件，应会看到确切的磁盘信息：

![显示数据更改率事件详细信息的页面](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但它们不能涵盖所有可能的应用程序输入输出 （I/O） 组合。 实际结果可能因应用程序 I/O 组合而异。

需要考虑两个限制：每个磁盘的数据改动和每个虚拟机的数据改动。 让我们看下表中的高级 P20 磁盘的示例。 对于单个 VM，站点恢复可以处理每个磁盘 5 MB/s 的改动，最多只能处理 5 个此类磁盘。 站点恢复每个 VM 的总改动量限制为 25 MB/s。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |    每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

### <a name="solution"></a>解决方案

Azure 站点恢复对数据更改速率有限制，具体取决于磁盘类型。 要查看此问题是重复的还是临时的，查找受影响的虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：****

![显示查找数据更改速率的三步过程的页面](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。************
1. 监视屏幕截图中所示的峰值。
1. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

数据更改速率的峰值可能来自偶尔的数据突发。 如果数据更改速率大于 10 MB/s（对于高级版）或 2 MB/s（标准版）并停机，则复制将迎头赶上。 如果改动始终超出支持的限制，请考虑以下选项之一：

- 排除导致高数据更改速率的磁盘：首先，禁用复制。 然后，您可以使用[PowerShell](./azure-to-azure-exclude-disks.md)排除磁盘。
- 更改灾难恢复存储磁盘的层：仅当磁盘数据改动小于 20 MB/s 时，才可以使用此选项。 假设具有 P10 磁盘的 VM 的数据改动量大于 8 MB/s，但小于 10 MB/s。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。 此解决方案仅适用于使用高级管理磁盘的计算机。 执行以下步骤:

    1. 转到受影响的复制计算机的**磁盘**并复制副本磁盘名称。
    1. 转到托管磁盘的此副本。
    1. 您可能会在 **"概述"** 中看到一个横幅，该横幅显示已生成 SAS URL。 选择此横幅并取消导出。 如果看不到横幅，请忽略此步骤。
    1. 一旦 SAS URL 被吊销，转到托管磁盘的**配置**。 增加大小，以便站点恢复支持源磁盘上的观察到的改动率。

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟

Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将数据从虚拟机上载到缓存存储帐户在 3 秒内低于 4 MB，则可能会遇到网络延迟。

要检查与延迟相关的问题，请使用[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。 您可以使用此命令行实用程序将数据从虚拟机上载到缓存存储帐户。 如果延迟较高，请检查您是否使用网络虚拟设备 （NVA） 来控制来自 VM 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接

要使站点恢复复制正常工作，需要 VM 提供与特定 URL 或 IP 范围的出站连接。 您可能将 VM 放在防火墙后面，或者使用网络安全组 （NSG） 规则来控制出站连接。 如果是这样，您可能会遇到问题。 若要确保所有 URL 都已连接，请参阅 [Site Recovery URL 的出站连接](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>错误 ID 153006 - 过去"X"分钟中没有可用于 VM 的应用一致性恢复点

以下是一些最常见的问题。

#### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL Server 2008/2008 R2 中的已知问题

**如何修复**：SQL 服务器 2008/2008 R2 存在已知问题。 请参阅文章[Azure 站点恢复代理或其他非组件 VSS 备份失败，对于托管 SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)的服务器。

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>在使用 AUTO_CLOSE DB 托管任何版本的 SQL Server 实例的服务器上，Azure Site Recovery 作业失败

**如何修复**：请参阅一些[非组件 VSS 备份，例如 Azure 站点恢复作业在承载具有AUTO_CLOSE GB 的 SQL Server 实例的服务器上失败](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)。


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和 2017 中的已知问题

**如何修复**： 请参阅在[SQL Server 2016 和 2017 中备份具有非基于组件的备份的虚拟机时发生的错误](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017)。

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>您正在使用 Azure 存储空间直接配置

**如何修复**：Azure 站点恢复无法为存储空间直接配置创建应用程序一致的恢复点。 [配置复制策略](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)。

### <a name="more-causes-because-of-vss-related-issues"></a>更多原因由于 VSS 相关问题：

要进一步排除故障，请检查源计算机上的文件以获取有关故障的确切错误代码：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

要查找文件中的错误，请在编辑器中打开 vacp.log 文件来搜索字符串"vacpError"。

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在前面的示例中 **，2147754994**是错误代码，用于告诉您此句子之后的故障。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装 - 错误 2147221164

**如何修复**：要生成应用程序一致性标记，Azure 站点恢复使用卷卷卷影副本服务 （VSS）。 站点恢复安装 VSS 提供程序的操作以拍摄应用一致性快照。 Azure 站点恢复将此 VSS 提供程序安装为服务。 如果未安装 VSS 提供程序，则应用程序一致性快照创建将失败。 它显示错误 ID 0x80040154"未注册类"。 请参阅[VSS 编写器安装故障排除](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)的文章。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用 - 错误 2147943458

**如何修复**：要生成应用程序一致性标记，Azure 站点恢复使用 VSS。 站点恢复安装 VSS 提供程序的操作以拍摄应用一致性快照。 此 VSS 提供程序作为服务安装。 如果未启用 VSS 提供程序服务，则应用程序一致性快照创建将失败。 它显示错误"指定的服务已禁用，无法启动 （0x80070422）。

如果禁用 VSS：

- 确认 VSS 提供程序服务的启动类型是否设置为“自动”。****
- 重启以下服务：
   - VSS 服务
   - Azure Site Recovery VSS 提供程序
   - VDS 服务

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序未注册 - 错误 2147754756

**如何修复**：要生成应用程序一致性标记，Azure 站点恢复使用 VSS。 检查是否安装了 Azure 站点恢复 VSS 提供程序服务。

使用以下命令重新安装 VSS 提供程序：
1. 卸载现有提供程序：C：\程序文件 （x86）\微软 Azure 站点恢复\代理\InMageVSSProvider_Uninstall.cmd
1. 重新安装 VSS 提供程序：C：\程序文件 （x86）\微软 Azure 站点恢复\代理\InMageVSSProvider_Install.cmd

确认 VSS 提供程序服务的启动类型是否设置为“自动”。****

重启以下服务：
- VSS 服务
- Azure Site Recovery VSS 提供程序
- VDS 服务
