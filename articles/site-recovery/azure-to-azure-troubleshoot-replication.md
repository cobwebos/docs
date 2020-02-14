---
title: 排查 Azure Site Recovery 的 Azure Vm 复制问题
description: 排查 Azure Site Recovery 的 Azure VM 灾难恢复中的复制问题
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190810"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>排查 Azure VM 灾难恢复中的复制问题

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机时在 Azure Site Recovery 中经常出现的问题。 还介绍了如何排查常见问题的问题。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

Azure Site Recovery 一致地将源区域中的数据复制到灾难恢复区域。 它还会每隔5分钟创建一个崩溃一致的恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

错误消息： "过去60分钟内，VM 没有可用的崩溃一致恢复点。"</br>
错误 ID：153007

以下部分介绍原因和解决方案。

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>源虚拟机上的数据更改率较高

如果源虚拟机上的数据更改率高于支持的限制，Azure Site Recovery 将创建一个事件。 若要查看问题是否是由于较高的变动造成的，请访问**VM** > 事件 > 的**复制项** **-最近72小时**。
应该会看到 "数据更改率超出支持的限制" 事件：

![显示过高的数据更改率较高的 Azure Site Recovery 页面](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果选择该事件，应会看到确切的磁盘信息：

![显示数据更改率事件详细信息的页面](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序输入-输出（i/o）组合。 实际结果可能因应用程序 I/O 组合而异。

需要考虑以下两个限制：每个磁盘的数据变动和每个虚拟机的数据变动。 让我们看一下下表中的高级 P20 磁盘，以获取示例。 对于单个 VM，Site Recovery 可以处理每个磁盘的 5 MB/秒的变动，最多可以处理 5 MB 的磁盘空间。 Site Recovery 的限制为每个 VM 的总流失数为 25 MB。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |10 MB/秒 | 每个磁盘 842 GB

### <a name="solution"></a>解决方案

Azure Site Recovery 对数据更改率有限制，这取决于磁盘的类型。 若要查看此问题是重复出现还是临时出现，请找到受影响的虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：

![显示用于查找数据更改率的三步过程的页面](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。
1. 监视屏幕截图中所示的峰值。
1. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

数据更改率的高峰可能源自偶尔的数据突发。 如果数据更改率超过 10 MB/秒（适用于高级版）或 2 MB/秒（适用于标准），则复制将会保持不变。 如果改动量始终超过支持的限制，请考虑以下选项之一：

- 排除导致数据更改率较高的磁盘：首先，禁用复制。 然后，可以使用[PowerShell](./azure-to-azure-exclude-disks.md)排除该磁盘。
- 更改灾难恢复存储磁盘的层：仅当磁盘数据改动小于 20 MB/秒时，此选项才可用。 假设具有 P10 磁盘的 VM 的数据变动大于 8 MB/s，但小于 10 MB/s。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。 此解决方案仅适用于使用高级托管磁盘的计算机。 执行以下步骤:

    1. 中转到受影响的复制计算机的**磁盘**，并复制副本磁盘名称。
    1. 请参阅托管磁盘的此副本。
    1. 你可能会看到一个横幅，其中显示了已生成 SAS URL 的**概述**。 选择此标语并取消导出。 如果看不到横幅，请忽略此步骤。
    1. 一旦将 SAS URL 吊销，请在托管磁盘中转到 "**配置**"。 增加大小，以便 Site Recovery 支持在源磁盘上观察到的变动率。

## <a name="Network-connectivity-problem"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟

Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将虚拟机中的数据上传到缓存存储帐户的速度低于 4 MB，则可能会遇到网络延迟。

若要检查与延迟有关的问题，请使用[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)。 你可以使用此命令行实用工具将数据从虚拟机上传到缓存存储帐户。 如果延迟较高，请检查是否正在使用网络虚拟设备（NVA）来控制 Vm 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接

要使 Site Recovery 的复制正常工作，需要 VM 提供到特定 Url 或 IP 范围的出站连接。 你的 VM 可能位于防火墙之后，或者使用网络安全组（NSG）规则来控制出站连接。 如果是这样，您可能会遇到问题。 若要确保所有 URL 都已连接，请参阅 [Site Recovery URL 的出站连接](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>错误 ID 153006-过去 "X" 分钟内没有适用于 VM 的应用一致恢复点

下面是一些最常见的问题。

#### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL server 2008/2008 R2 中的已知问题

**如何修复**： SQL Server 2008/2008 R2 存在已知问题。 [有关托管 SQL Server 2008 R2 的服务器，请参阅 Azure Site Recovery 代理或其他非组件 VSS 备份](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)的项目失败。

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>承载具有 AUTO_CLOSE Db 的 SQL Server 实例的任何版本的服务器上的 Azure Site Recovery 作业失败

**如何修复**：请参阅[非组件 VSS 备份一文，如在托管 SQL Server 实例 AUTO_CLOSE 数据库的服务器上 Azure Site Recovery 作业失败](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)。


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和2017中的已知问题

**如何修复**：[在 SQL Server 2016 和2017中备份具有非基于组件备份的虚拟机时](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)，请参阅文章 "错误"。

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>正在使用 Azure 存储空间直通配置

**如何修复**： Azure Site Recovery 无法为存储空间直通配置创建应用程序一致的恢复点。 [配置复制策略](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)。

### <a name="more-causes-because-of-vss-related-issues"></a>由于与 VSS 相关的问题导致的原因更多：

若要进一步进行故障排除，请检查源计算机上的文件以获取错误的确切错误代码：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

若要在文件中查找错误，请在编辑器中打开 "vacp.exe" 文件，搜索字符串 "vacpError"。

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在前面的示例中， **2147754994**是指出此句子后的失败的错误代码。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装-错误2147221164

**如何修复**：若要生成应用程序一致性标记，Azure Site Recovery 使用卷影复制服务（VSS）。 Site Recovery 安装 VSS 提供程序，使其执行应用一致性快照操作。 Azure Site Recovery 将此 VSS 提供程序安装为服务。 如果未安装 VSS 提供程序，则无法创建应用程序一致性快照。 其中显示错误 ID 0x80040154 "类未注册"。 请参阅[VSS 编写器安装故障排除](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)一文。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用-错误2147943458

**如何修复**：若要生成应用程序一致性标记，AZURE SITE RECOVERY 使用 VSS。 Site Recovery 安装 VSS 提供程序，使其执行应用一致性快照操作。 此 VSS 提供程序安装为服务。 如果尚未启用 VSS 提供程序服务，则无法创建应用程序一致性快照。 其中显示错误 "指定的服务已禁用且无法启动（0x80070422）"。

如果禁用 VSS：

- 验证 VSS 提供程序服务的启动类型是否设置为 "**自动**"。
- 重新启动下列服务：
   - VSS 服务
   - Azure Site Recovery VSS 提供程序
   - VDS 服务

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序 NOT_REGISTERED-错误2147754756

**如何修复**：若要生成应用程序一致性标记，AZURE SITE RECOVERY 使用 VSS。 检查是否安装了 Azure Site Recovery VSS 提供程序服务。

使用以下命令重新安装 VSS 提供程序：
1. 卸载现有提供程序： C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall .cmd
1. 重新安装 VSS 提供程序： C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install .cmd

验证 VSS 提供程序服务的启动类型是否设置为 "**自动**"。

重新启动下列服务：
- VSS 服务
- Azure Site Recovery VSS 提供程序
- VDS 服务
