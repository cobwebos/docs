---
title: 排查使用 Azure Site Recovery 复制 Azure VM 时出现的问题
description: 排查使用 Azure Site Recovery 进行 Azure VM 灾难恢复时出现的复制问题
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130403"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>排查 Azure VM 灾难恢复中的复制问题

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机 (VM) 时在 Azure Site Recovery 中经常出现的问题。 另外还介绍了如何排查常见问题。 有关支持的配置的详细信息，请参阅 [support matrix for replicating Azure VMs](./azure-to-azure-support-matrix.md)（复制 Azure VM 的支持矩阵）。

Azure Site Recovery 以一致的方式将数据从源区域复制到灾难恢复区域。 它还每隔 5 分钟创建崩溃一致性恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

以下部分介绍原因和解决方案。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>源虚拟机上的数据更改率较高

如果源虚拟机上的数据更改率超过支持的限制，Azure Site Recovery 将创建一个事件。 若要确定问题是否由高变动率造成，请转到“复制的项” > “VM” > “事件 - 过去 72 小时”。  
你应当会看到“数据更改率超过支持的限制”事件：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="显示数据更改率过高的 Azure Site Recovery 页。":::

如果选择该事件，应会看到确切的磁盘信息：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="显示数据更改率过高的 Azure Site Recovery 页。":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制

下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序输入-输出 (I/O) 组合。 实际结果可能因应用程序 I/O 组合而异。

有两个限制需要考虑：每个磁盘的数据变动率，以及每个虚拟机的数据变动率。 有关示例，请查看下表中的高级 P20 磁盘。 对于单个 VM，Site Recovery 能够处理每个磁盘的 5 MB/秒的变动率，最多可以处理 5 个这样的磁盘。 Site Recovery 对每个 VM 的总变动率限制为 54 MB/秒。

**复限制复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB    | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |    每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |20 MB/秒 | 每个磁盘 1684 GB

### <a name="solution"></a>解决方案

Azure Site Recovery 根据磁盘类型实施数据更改率限制。 若要确定此问题是重复性的还是暂时性的，请确定受影响虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="显示数据更改率过高的 Azure Site Recovery 页。":::

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。  
1. 监视屏幕截图中所示的峰值。
1. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

数据更改率出现高峰的原因可能是偶发性的数据突发。 如果数据更改率高于 10 MB/秒（针对高级存储）或 2 M/秒（针对标准存储），但随后又下降，则复制可以赶上进度。 如果变动率一直远远超过支持的限制，请考虑以下做法之一：

- 排除导致数据更改率变高的磁盘：首先禁用复制。 然后可以使用 [PowerShell](azure-to-azure-exclude-disks.md) 来排除磁盘。
- 更改灾难恢复存储磁盘层：仅当磁盘数据变动率小于 20 MB/秒时，才可以使用此选项。 例如，包含 P10 磁盘的 VM 的数据变动率大于 8 MB/秒，但小于 10 MB/秒。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。 此解决方法仅适用于使用高级托管磁盘的计算机。 执行以下步骤：

  1. 转到受影响的已复制计算机的“磁盘”，并复制副本磁盘名称。
  1. 转到托管磁盘的此副本。
  1. 可能会在“概述”中看到一个横幅，指出已生成 SAS URL。 请选择此横幅并取消导出。 如果看不到该横幅，请忽略此步骤。
  1. 撤销 SAS URL 后，立即转到托管磁盘的“配置”。 增大大小，使 Site Recovery 支持在源磁盘上观测到的变动率。

## <a name="network-connectivity-problems"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟

Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将数据从虚拟机上传到缓存存储帐户时的速度有 3 秒低于 4 MB，则可能是遇到了网络延迟。

若要检查延迟相关的问题，请使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md)。 可以使用此命令行实用工具将数据从虚拟机上传到缓存存储帐户。 如果延迟较高，请检查你是否在使用网络虚拟设备 (NVA) 控制来自 VM 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接

要使 Site Recovery 复制正常运行，需要 VM 提供到特定 URL 或 IP 范围的出站连接。 VM 可能位于防火墙后面，或使用网络安全组 (NSG) 规则控制出站连接。 如果是这样，则可能会遇到问题。 若要确保所有 URL 都已连接，请参阅 [URL 的出站连接](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>错误 ID 153006 - 在过去“X”分钟内没有可供 VM 使用的应用一致性恢复点

下面是一些最常见的问题。

### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL Server 2008/2008 R2 中的已知问题

**如何修复：** SQL Server 2008/2008 R2 有一个已知问题。 请参阅[托管 SQL Server 2008 R2 的服务器的 Azure Site Recovery 代理或其他非组件 VSS 备份失败](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)一文。

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>在使用 AUTO_CLOSE DB 托管任何版本的 SQL Server 实例的服务器上，Azure Site Recovery 作业失败

**如何修复：** 请参阅[托管包含 AUTO_CLOSE DB 的 SQL Server 实例上的非组件 VSS 备份（例如 Azure Site Recovery 作业）失败](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)一文。

### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 和 2017 中的已知问题

**如何解决**：请参阅 [SQL Server 2017 累积更新 16](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017) 一文。

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>正在使用 Azure 存储空间直通配置

**如何解决**：Azure Site Recovery 无法针对存储空间直通配置创建应用程序一致性恢复点。 [配置复制策略](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Linux 服务器上未启用应用一致性

**如何解决**：适用于 Linux 操作系统的 Azure Site Recovery 支持通过应用程序自定义脚本实现应用一致性。 为保障应用一致性，Azure Site Recovery 移动代理将使用带有 pre 和 post 选项的自定义脚本。 [这里](./site-recovery-faq.md#replication)是启用此功能的步骤。

### <a name="more-causes-because-of-vss-related-issues"></a>更多的原因在于 VSS 相关的问题：

若要进一步排查问题，请查看源计算机上的文件，获取故障的具体错误代码：

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

若要查找错误，请在文本编辑器中打开 vacp.log 文件，并搜索字符串“vacpError”。

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

在上面的示例中，**2147754994** 是错误代码，提供有关此句子后面的失败的信息。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装 - 错误 2147221164

**如何解决**：为了生成应用程序一致性标记，Azure Site Recovery 将使用卷影复制服务 (VSS)。 Site Recovery 安装适用于其操作的 VSS 提供程序，以创建应用一致性快照。 Azure Site Recovery 将此 VSS 提供程序作为服务安装。 如果未安装 VSS 提供程序，则创建应用程序一致性快照的操作将会失败。 此时会显示“错误 ID 0x80040154 未注册类”。 请参阅有关[ VSS 编写器安装故障排除](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)的文章。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用 - 错误 2147943458

**如何解决**：为了生成应用程序一致性标记，Azure Site Recovery 将使用 VSS。 Site Recovery 安装适用于其操作的 VSS 提供程序，以创建应用一致性快照。 此 VSS 提供程序作为服务安装。 如果未启用 VSS 提供程序服务，则创建应用程序一致性快照的操作将会失败。 此时会显示错误：“指定的服务已禁用且无法启动(0x80070422)”。

如果已禁用 VSS：

- 验证 VSS 提供程序服务的启动类型是否设置为“自动”。
- 重启以下服务：
  - VSS 服务。
  - Azure Site Recovery VSS 提供程序。
  - VDS 服务。

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序未注册 - 错误 2147754756

**如何解决**：为了生成应用程序一致性标记，Azure Site Recovery 将使用 VSS。 检查是否已安装 Azure Site Recovery VSS 提供程序服务。

使用以下命令重新安装 VSS 提供程序：

1. 卸载现有的提供程序：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. 重新安装 VSS 提供程序：

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

确认 VSS 提供程序服务的启动类型是否设置为“自动”。

重启以下服务：

- VSS 服务。
- Azure Site Recovery VSS 提供程序。
- VDS 服务。
