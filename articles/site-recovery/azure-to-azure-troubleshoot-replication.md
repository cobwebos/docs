---
title: 排查 Azure Site Recovery 的 Azrue Vm 正在进行的复制
description: 排查复制 Azure 虚拟机进行灾难恢复时出现的错误和问题
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: carmonm
ms.openlocfilehash: b738ffc36334fc540582ba29e803eb2790e2119e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930742"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>排查 Azure 到 Azure VM 复制中持续出现的问题

本文介绍了在不同区域之间复制和恢复 Azure 虚拟机时在 Azure Site Recovery 中经常出现的问题。 另外还介绍了如何排查这些问题。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。

Azure Site Recovery 以一致的方式将数据从源区域复制到灾难恢复区域，并每隔 5 分钟创建崩溃一致性恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，它会通过以下信息向你发出警报：

错误消息： "过去60分钟内，VM 没有可用的崩溃一致恢复点。"</br>
错误 ID：153007 </br>

以下部分介绍原因和解决方案。

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>源虚拟机上的数据更改率较高
如果源虚拟机上的数据更改率超过支持的限制，Azure Site Recovery 将激发事件。 若要检查问题是否由高变动率造成，请转到“复制的项” > “VM” > “事件 - 过去 72 小时”。
应会看到事件“数据更改率超过支持的限制”：

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果选择该事件，应会看到确切的磁盘信息：

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序 I/O 组合。 实际结果可能因应用程序 I/O 组合而异。 

有两个限制需要考虑：每个磁盘的数据变动率，以及每个虚拟机的数据变动率。 有关示例，请看下表中的高级 P20 磁盘。 Site Recovery 能够处理每个磁盘 5 MB/秒的变动率，但由于每个 VM 的总变动率限制为 25 MB/秒，因此，它最多可以处理每个 VM 中 5 个这样的磁盘。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动率** | **每天的总源数据磁盘数据变动率**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |10 MB/秒 | 每个磁盘 842 GB

### <a name="solution"></a>解决方案
Azure Site Recovery 根据磁盘类型实施数据更改率限制。 若要判断此问题是重复性的还是暂时性的，请确定受影响虚拟机的数据更改率。 请转到源虚拟机，在“监视”下找到指标，然后添加以下屏幕截图所示的指标：

![确定数据更改率的三步过程](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 选择“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。
2. 监视屏幕截图中所示的峰值。
3. 查看 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标可能无法提供磁盘级别的信息，但能够反映数据变动率总模式。

如果峰值是由于偶尔出现的数据迸发，数据更改率间或高于 10 MB/秒（针对高级存储）和 2 MB/秒（针对标准存储），但随后又降下来，则复制可同步。 但是，如果变动率在大多数时间远远超过支持的限制，则在可能情况下，请考虑以下选项之一：

* **排除导致数据更改率很高的磁盘**：你可以使用[PowerShell](./azure-to-azure-exclude-disks.md)排除该磁盘。若要排除磁盘，必须先禁用复制。 
* **更改灾难恢复存储磁盘的层**：仅当磁盘数据改动小于 20 MB/秒时，此选项才可用。 假设包含 P10 磁盘的 VM 的数据变动率大于 8 MB/秒，但小于 10 MB/秒。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。 请注意，此解决方案仅适用于使用高级托管磁盘的计算机。 请按照以下步骤操作：
    - 导航到受影响的复制计算机的 "磁盘" 边栏选项卡，并复制副本磁盘名称
    - 导航到此副本托管磁盘
    - 你可能会在概述边栏选项卡上看到一个横幅，指出已生成 SAS URL。 单击此标语并取消导出。 如果看不到横幅，请忽略此步骤。
    - 一旦将 SAS URL 吊销，请在托管磁盘中转到 "配置" 边栏选项卡，增加大小，以便 ASR 支持在源磁盘上观察到的变动率

## <a name="Network-connectivity-problem"></a>网络连接问题

### <a name="network-latency-to-a-cache-storage-account"></a>缓存存储帐户的网络延迟
Site Recovery 会将已复制数据发送到缓存存储帐户。 如果将数据从虚拟机上传到缓存存储帐户时的速度低于 4 MB/3 秒，则可能会出现网络延迟。 

若要检查是否存在与延迟相关的问题，请使用 [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 将数据从虚拟机上传到缓存存储帐户。 如果延迟较高，请检查你是否在使用网络虚拟设备 (NVA) 控制来自 VM 的出站网络流量。 如果所有复制流量都通过 NVA，设备可能会受到限制。 

建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。 有关详细信息，请参阅[网络虚拟设备配置](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)。

### <a name="network-connectivity"></a>网络连接
若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。 若要确保所有 URL 都已连接，请参阅 [Site Recovery URL 的出站连接](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)。 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>错误 ID 153006-最近 "XXX" 分钟内没有适用于 VM 的应用一致恢复点

下面列出了一些最常见的问题

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因1： SQL server 2008/2008 R2 中的已知问题 
**如何修复**： SQL Server 2008/2008 R2 存在一个已知问题。 [有关托管 SQL Server 2008 R2 的服务器，请参阅此知识库文章 Azure Site Recovery 代理或其他非组件 VSS 备份失败](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因2：在承载具有 AUTO_CLOSE Db 的 SQL Server 实例的任何版本的服务器上，Azure Site Recovery 作业失败 
**如何修复**：请参阅知识库[文章](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因3： SQL Server 2016 和2017中的已知问题
**如何修复**：请参阅知识库[文章](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>原因4：你使用的是存储空间直通配置
**如何修复**： Azure Site Recovery 无法为存储空间直通配置创建应用程序一致的恢复点。 请参阅文章以正确[配置复制策略](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-due-to-vss-related-issues"></a>由于 VSS 相关问题而导致的更多原因：

若要进一步进行故障排除，请检查源计算机上的文件以获取错误的确切错误代码：
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

如何查找文件中的错误？
在编辑器中打开 vacp.exe 文件，搜索字符串 "vacpError"
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

在上面的示例中， **2147754994**是说明失败的错误代码，如下所示

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 编写器未安装-错误2147221164 

*如何修复*：若要生成应用程序一致性标记，Azure Site Recovery 使用 Microsoft 卷影复制服务（VSS）。 它会安装 VSS 提供程序，使其执行应用一致性快照操作。 此 VSS 提供程序安装为服务。 如果未安装 VSS 提供程序服务，应用程序一致性快照创建将失败，并出现错误 id 0x80040154 "类未注册"。 </br>
请参阅[VSS 编写器安装疑难解答一文](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 编写器已禁用-错误2147943458

**如何修复**：若要生成应用程序一致性标记，Azure Site Recovery 使用 Microsoft 卷影复制服务（VSS）。 它会安装 VSS 提供程序，使其执行应用一致性快照操作。 此 VSS 提供程序安装为服务。 如果 VSS 提供程序服务处于禁用状态，则应用程序一致性快照创建将失败，并出现错误 id "指定的服务已禁用且无法启动（0x80070422）"。 </br>

- 如果禁用了 VSS，
    - 验证 VSS 提供程序服务的启动类型是否设置为 "**自动**"。
    - 重新启动下列服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 提供程序 NOT_REGISTERED-错误2147754756

**如何修复**：若要生成应用程序一致性标记，Azure Site Recovery 使用 Microsoft 卷影复制服务（VSS）。 检查是否安装了 Azure Site Recovery VSS 提供程序服务。 </br>

- 使用以下命令重试提供程序安装：
- 卸载现有提供程序： C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall .cmd
- 重新安装： C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install .cmd
 
验证 VSS 提供程序服务的启动类型是否设置为 "**自动**"。
    - 重新启动下列服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务
