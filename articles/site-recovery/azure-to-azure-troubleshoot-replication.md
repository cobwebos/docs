---
title: 排查使用 Azure Site Recovery 复制 Azure VM 时出现的问题
description: 排查使用 Azure Site Recovery 进行 Azure VM 灾难恢复时出现的复制问题
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 3c7d4f0a6d33a52fd972815923e60b33ce8a7448
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901356"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>排查 Azure VM 灾难恢复中的复制问题

本文介绍将 Azure 虚拟机（VM）从一个区域复制和恢复到另一个区域时 Azure Site Recovery 的常见问题。 另外还介绍了如何排查常见问题。 有关支持的配置的详细信息，请参阅 [support matrix for replicating Azure VMs](site-recovery-support-matrix-azure-to-azure.md)（复制 Azure VM 的支持矩阵）。

Azure Site Recovery 以一致的方式将数据从源区域复制到灾难恢复区域。 它还每隔 5 分钟创建崩溃一致性恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

以下部分介绍原因和解决方案。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>源虚拟机上的数据更改率较高

如果源虚拟机上的数据更改率高于支持的限制，Azure Site Recovery 将创建一个事件。 若要查看问题是否是由于较高的变动造成的，请访问**复制的项** > **VM** > **事件-过去72小时**。
你应看到事件**数据更改率超出支持的限制**：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="显示高的数据更改率的 Azure Site Recovery 页面。":::

如果选择该事件，应会看到确切的磁盘信息：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="显示数据更改率事件详细信息的页面。":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序输入-输出（i/o）组合。 实际结果可能因应用程序 I/O 组合而异。

需要考虑以下两个限制：每个磁盘的数据变动和每个虚拟机的数据变动。 让我们看一下下表中的高级 P20 磁盘，以获取示例。 对于单个 VM，Site Recovery 可以处理每个磁盘的 5 MB/秒的变动，最多可以处理 5 MB 的磁盘空间。 Site Recovery 的每个 VM 的总流失数限制为 54 MB/秒。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |    每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

### <a name="solution"></a>解决方案

Azure Site Recovery 对数据更改率有限制，这取决于磁盘的类型。 若要查看此问题是重复出现还是临时出现，请找到受影响的虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：****

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="页面，其中显示了用于查找数据更改率的三步过程。":::

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。************
1. 监视屏幕截图中所示的峰值。
1. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

数据更改率的高峰可能源自偶尔的数据突发。 如果数据更改率超过 10 MB/秒（适用于高级版）或 2 MB/秒（适用于标准），则复制将会保持不变。 如果改动量始终超过支持的限制，请考虑以下选项之一：

- 排除导致数据更改率较高的磁盘：首先，禁用复制。 然后，可以使用[PowerShell](azure-to-azure-exclude-disks.md)排除该磁盘。
- 更改灾难恢复存储磁盘的层：仅当磁盘数据改动小于 20 MB/秒时，此选项才可用。 例如，具有 P10 磁盘的 VM 的数据变动大于 8 MB/秒，但小于 10 MB/s。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。 此解决方案仅适用于使用高级托管磁盘的计算机。 请执行这些步骤：

  1. 中转到受影响的复制计算机的**磁盘**，并复制副本磁盘名称。
  1. 请参阅托管磁盘的此副本。
  1. 你可能会看到一个横幅，其中显示了已生成 SAS URL 的**概述**。 选择此标语并取消导出。 如果看不到横幅，请忽略此步骤。
  1. 一旦将 SAS URL 吊销，请在托管磁盘中转到 "**配置**"。 增加大小，以便 Site Recovery 支持在源磁盘上观察到的变动率。

## <a name="network-connectivity-problems"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟

Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将虚拟机中的数据上传到缓存存储帐户的速度低于 4 MB，则可能会遇到网络延迟。

若要检查与延迟有关的问题，请使用[AzCopy](/azure/storage/common/storage-use-azcopy)。 你可以使用此命令行实用工具将数据从虚拟机上传到缓存存储帐户。 如果延迟较高，请检查是否正在使用网络虚拟设备（NVA）来控制 Vm 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接

要使 Site Recovery 的复制正常工作，需要 VM 提供到特定 Url 或 IP 范围的出站连接。 你的 VM 可能位于防火墙之后，或者使用网络安全组（NSG）规则来控制出站连接。 如果是这样，您可能会遇到问题。 若要确保所有 Url 都已连接，请参阅[url 的出站连接](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>错误 ID 153006-过去 "X" 分钟内没有适用于 VM 的应用一致恢复点

下面是一些最常见的问题。

### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL Server 2008/2008 R2 中的已知问题

**如何修复：** SQL server 2008/2008 R2 存在一个已知问题。 [有关托管 SQL Server 2008 R2 的服务器，请参阅 Azure Site Recovery 代理或其他非组件 VSS 备份](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)的项目失败。

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>在使用 AUTO_CLOSE DB 托管任何版本的 SQL Server 实例的服务器上，Azure Site Recovery 作业失败

**如何修复：** 请参阅[非组件 VSS 备份一文，如在托管 SQL Server 实例 AUTO_CLOSE 数据库的服务器上 Azure Site Recovery 作业失败](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)。

### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和 2017 中的已知问题

**如何修复**：有关 SQL Server 2017，请参阅[累积更新 16](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017)一文。

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>正在使用 Azure 存储空间直通配置

**如何修复**： Azure Site Recovery 无法为存储空间直通配置创建应用程序一致的恢复点。 [配置复制策略](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

### <a name="more-causes-because-of-vss-related-issues"></a>由于与 VSS 相关的问题导致的原因更多：

若要进一步进行故障排除，请检查源计算机上的文件以获取错误的确切错误代码：

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

若要查找错误，请在文本编辑器中打开_vacp.exe_文件，并搜索字符串**vacpError**。

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

在前面的示例中， **2147754994**是指出此句子后的失败的错误代码。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装 - 错误 2147221164

**如何修复**：若要生成应用程序一致性标记，Azure Site Recovery 使用卷影复制服务（VSS）。 Site Recovery 安装 VSS 提供程序，使其执行应用一致性快照操作。 Azure Site Recovery 将此 VSS 提供程序安装为服务。 如果未安装 VSS 提供程序，则无法创建应用程序一致性快照。 其中显示**错误 ID 0X80040154 类未注册**。 请参阅[VSS 编写器安装故障排除](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)一文。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用 - 错误 2147943458

**如何修复**：若要生成应用程序一致性标记，AZURE SITE RECOVERY 使用 VSS。 Site Recovery 安装 VSS 提供程序，使其执行应用一致性快照操作。 此 VSS 提供程序作为服务安装。 如果尚未启用 VSS 提供程序服务，则无法创建应用程序一致性快照。 其中显示错误：**指定的服务已禁用，无法启动（0x80070422）**。

如果禁用 VSS：

- 确认 VSS 提供程序服务的启动类型是否设置为“自动”。****
- 重启以下服务：
  - VSS 服务。
  - Azure Site Recovery VSS 提供程序。
  - VDS 服务。

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序未注册 - 错误 2147754756

**如何修复**：若要生成应用程序一致性标记，AZURE SITE RECOVERY 使用 VSS。 检查是否安装了 Azure Site Recovery VSS 提供程序服务。

使用以下命令重新安装 VSS 提供程序：

1. 卸载现有的提供程序：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. 重新安装 VSS 提供程序：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

确认 VSS 提供程序服务的启动类型是否设置为“自动”。****

重启以下服务：

- VSS 服务。
- Azure Site Recovery VSS 提供程序。
- VDS 服务。
