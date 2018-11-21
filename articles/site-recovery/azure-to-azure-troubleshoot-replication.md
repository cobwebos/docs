---
title: Azure 到 Azure 复制问题和错误的 Azure Site Recovery 故障排除 | Microsoft Docs
description: 解决复制 Azure 虚拟机进行灾难恢复时出现的错误和问题
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: asgang
ms.openlocfilehash: 0ac90d8ef29d4293a5eeb5f932687788320c218e
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615790"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>排查 Azure 到 Azure VM 持续复制问题

本文介绍将 Azure 虚拟机从一个区域复制和恢复到另一个区域时 Azure Site Recovery 中出现的常见问题，并说明如何解决这些问题。 有关受支持的配置的详细信息，请参阅[复制 Azure VM 支持矩阵](site-recovery-support-matrix-azure-to-azure.md)。


## <a name="recovery-points-not-getting-generated"></a>不生成恢复点

错误消息：在过去 60 分钟内没有可供 VM 使用的崩溃一致性恢复点。</br>
错误 ID：153007 </br>

Azure Site Recovery 以一致的方式将数据从源区域复制到灾难恢复区域，并每隔 5 分钟创建崩溃一致性恢复点。 如果 Site Recovery 在 60 分钟内无法创建恢复点，则会向用户发出警报。 下面是导致此错误的可能原因：

**原因 1：[源虚拟机上的数据更改率较高](#high-data-change-rate-on-the-source-virtal-machine)**    
**原因 2：[网络连接问题](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="causes-and-solutions"></a>原因和解决方法

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>源虚拟机上的数据更改率较高
如果源虚拟机上的数据更改率超过支持的限制，Azure Site Recovery 将激发事件。 若要检查问题是否由高变动率造成，请转到“复制的项”>“VM”，并单击“事件 - 过去 72 小时”。
应会看到以下屏幕截图所示的事件“数据更改率超过支持的限制”

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

如果单击该事件，应会看到以下屏幕截图所示的确切磁盘信息

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序 I/O 组合。 实际结果可能因应用程序 I/O 组合而异。 我们还应注意，有两个限制需要考虑：每个磁盘的数据变动率，以及每个虚拟机的数据变动率。
例如，查看下表中的高级 P20 磁盘可以发现，Site Recovery 能够处理每个磁盘的 5 MB/秒的变动率，但由于每个 VM 的总变动率限制为 25 MB/秒，因此，它最多可以处理每个 VM 中 5 个这样的磁盘。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |10 MB/秒 | 每个磁盘 842 GB

### <a name="solution"></a>解决方案
我们必须知道，Azure Site Recovery 根据磁盘类型实施数据更改率限制。 若要判断此问题是重复性的还是暂时性的，必须确定受影响虚拟机的数据更改率模式。
若要确定受影响虚拟机的数据更改率， 请转到源虚拟机 >“监视”下的“指标”，并添加下面所示的指标。

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. 单击“添加指标”，并添加“OS 磁盘写入字节数/秒”和“数据磁盘写入字节数/秒”。
2. 监视屏幕截图中所示的峰值。
3. 其中会显示 OS 磁盘和所有数据磁盘中总共发生的写入操作次数。 这些指标暂时不能告知磁盘级别的信息，但能够很好地反映总数据变动率模式。

出现上述情况时，如果只是偶尔出现数据迸发，数据更改率间或高于 10 MBps（针对高级存储）和 2 MBps（针对标准存储），但随后又降下来，则复制可同步。 但是，如果变动率在大多数时间远远超过支持的限制，则应考虑以下选项之一是否可行：

**选项 1：** 排除导致数据更改率变高的磁盘： </br>
目前可以使用 [Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) 排除磁盘

**选项 2：** 更改灾难恢复存储磁盘层： </br>
仅当磁盘数据变动率小于 10 MB/秒时，才可以使用此选项。 假设包含 P10 磁盘的 VM 的数据变动率大于 8 MB/秒，但小于 10 MB/秒。 如果客户在保护期间可以使用 P30 磁盘作为目标存储，则可以解决此问题。

### <a name="Network-connectivity-issue"></a>网络连接问题

若要 Site Recovery 复制正常运行，需要从 VM 到特定 URL 或 IP 范围的出站连接。 如果 VM 位于防火墙后或使用网络安全组 (NSG) 规则来控制出站连接，则可能会遇到以下问题之一。</br>
请参阅 [Site Recovery URL 的出站连接](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-troubleshoot-errors?#outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072)
